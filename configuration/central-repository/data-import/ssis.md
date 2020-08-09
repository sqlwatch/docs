---
parent: Central Repository
grand_parent: Configuration
title: SSIS
nav_order: 20
---

# Import remote data using SQL Server Integration Services
{: .no_toc }
---

- TOC 
{:toc}

>It is assumed that the SQL Server Intergration Services (SSIS) is installed and configured and that the SSISDB has been initialised and the environment is operational.

## SSIS Package Deployment

The SSIS package can be easily deployed using the provided `.ispac` file. [Learn more about SSIS deployment](https://docs.microsoft.com/en-us/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages). Alternatively, it can be also deployed from the Visual Studio Project. Upgrading SSIS package is simply re-deploying a newer version and removal is done by deleting the deployed package.

<div class="responsive-iframe-container responsive-iframe-container-16-9">
  <iframe class="responsive-iframe" src="https://www.youtube-nocookie.com/embed/RKfOBlTXk_A" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## SSIS Package Configuration

To configure SSIS package, navigate to the Project in the Integration Services Catalogs:
![SQLWATCH SSIS Configuration]({{ site.baseurl }}/assets/images/sqlwatch-ssis-package-configuration.png)

You can apply the configuration to the project, or individual packages. The project will contain the collection of all configuration options from child packages. [Learn more about SSIS Catalog](https://docs.microsoft.com/en-us/sql/integration-services/catalog/ssis-catalog)

### Control Package
{: .fs-5 }

The control package `control_import.dtsx` is responsible for orchestrating multi-threaded data collection and execution of the Worker Package `import_remote_data.dtsx`
![SQLWATCH SSIS Control Package]({{ site.baseurl }}/assets/images/sqlwatch-control-package.png)

#### Parameters

**number_of_parallel_collectors**:
The number of threads for parallel collection. If this is set to > 1, then multiple servers will be collected in parallel, in addition to each collector data flow being run in parallel, according to the MaxConcurrentExecutables parameter. Be careful as running parallel collectors may be slower than a single thread. Make sure the central repository can sustain the workload. Maximum allowed parallel threads are 8.

**repository_database**:
The name of the database where the central repository is. Default SQLWATCH.

**repository_instance_name**:
The name of the SQL Server instance where the central repository is hosted.

**repository_password**:
The SQL Password to access central repository or blank for Windows authentication.

**repository_user_name**:
The SQL User to access central repository or blank for Windows authentication.

### Worker Package
{: .fs-5 }

The worker package `import_remote_data.dtsx` is responsible for the actual data collection from remote instances into the central repository.
![SQLWATCH SSIS Worker Package]({{ site.baseurl }}/assets/images/sqlwatch-worker-package.png)

#### Parameters

>Worker package parameters are passed from the control package. However, you can also invoke the worker package manually for a specific instance with the same parameters

**last_snapshot_offset_minutes (NOT USED)**: 
Offset in minutes to increase delta time slice. By default, only data since the last snapshot will be collected from the remote instance. We may increase this and go further beyond that to cover any gaps. Behind the scenes this translates to `[snapshot_time] > dateadd(minute,-@last_snapshot_offset_minutes,[last_snapshot_time])`

**remote_instance_name**: 
SQL Instance to collect data from. This parameter is passed from the control package during run time.

**remote_password**: 
SQL Password for the remote instance or blank for Windows authentication.

**remote_user_name**: 
SQL User for the remote instance or blank for Windows authentication.

>In the current implementation, it is not possible to specify different accounts for accessing remote instances. This means that when using SQL Authentication all instances must have the same SQL User and Password. When using Windows authentication this is usually not a problem as the SSIS runs under a context of one the SQL agent or proxy account

**repository_database** The name of the database where the central repository is. Default SQLWATCH.

**repository_instance_name** The name of the SQL Server instance where the central repository is hosted.

**repository_password** The SQL Password to access central repository or blank for Windows authentication.

**repository_user_name** The SQL User to access central repository or blank for Windows authentication.

## Execution

The package does delta loads of the `logger*` tables and full loads of the `meta*` tables. Meta tables are relatively small and should not contain more than a few hundred rows. Logger tables can be quite big and thanks to delta loads, the more often the package runs, the less data it pulls with every run. A good start is to run it every 10 minutes.

There is no predefined agent job for the SSIS based repository collector due to a variety of environments and folder names in SSISDB. Once the package has been deployed onto the preferred Integration Services Server and configured please crate agent job with the schedule as you please.

When scheduling the `.dtsx`, the control package should be called from the agent job:
![SQLWATCH SSIS Agent Job]({{ site.baseurl }}/assets/images/sqlwatch-ssis-agent-job.png)
