---
parent: Configuration
title: Central Repository
---

# Central Repository Configuration
{: .no_toc }
---

The central repository is an ordinary SQLWATCH database where data from other instances is being imported for centralised reporting. Any SQLWATCH database can become a central repository. Data collection can happen via [SQL Server Integration Package (SSIS)](https://docs.microsoft.com/en-us/sql/integration-services/sql-server-integration-services) or via [Linked Server](https://docs.microsoft.com/en-us/sql/relational-databases/linked-servers/linked-servers-database-engine). 

- TOC 
{:toc}

## Central repository collector

The performance impact on the remote instance will be mainly driven by the amount of data we are pulling with each connection. The collection from the remote instance into the central repository is primarily delta with few exceptions of full load. The more often we are pulling data into the central repository the less impact it will have. Below is an example of the CPU utilisation of the remote instance whilst pulling a 5 minutes delta into the central repository. Note that pull does not last longer than a few seconds:
![SQLWATCH Central Repository impact on remote instance]({{ site.baseurl }}/assets/images/sqlwatch-central-repository-reading-impact.png)

The permission required to collect data from the remote instance is `db_datareader`. Permissions required to insert data on the central repository are: `db_datareader` to read parameters, `db_datawriter` to write data and `db_ddladmin` for bulk inserts.

Central Repository can download data from remote instances in two ways:

- SQL Server Integration Services (SSIS)
- Linked Server

Both methods do a FULL load of the relatively small `meta*` tables and delta loads of the `logger*` tables that contain the actual performance data. Whilst SSIS is a performance optimised engine and may perform faster, there is no noticable performance advantage of using either method and both perform in a similar way when pulling remote data. The advantage of using Linked Server is that it does not require SSIS and can be run on a SQL Server Express Edition with jobs invoked via Windows Scheduled Tasks instead of the SQL Agent.

## SSIS Package Deployment

>It is assumed that the SSIS Server is installed and configured and that the SSISDB has been initialised and the environment is operational.

The SSIS package can be easily deployed using the provided `.ispac` file. [Learn more about SSIS deployment](https://docs.microsoft.com/en-us/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages). Alternatively, it can be also deployed from the Visual Studio Project. Upgrading SSIS package is simply re-deploying a newer version and removal is done by deleting the deployed package.

<div class="responsive-iframe-container responsive-iframe-container-16-9">
  <iframe class="responsive-iframe" src="https://www.youtube-nocookie.com/embed/RKfOBlTXk_A" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## SSIS Package Configuration

To configure SSIS package, navigate to the Project in the Integration Services Catalogs:
![SQLWATCH SSIS Configuration]({{ site.baseurl }}/assets/images/sqlwatch-ssis-package-configuration.png)

You can apply the configuration to the project, or individual packages. The project will contain the collection of all configuration options from child packages. [Learn more about SSIS Catalog](https://docs.microsoft.com/en-us/sql/integration-services/catalog/ssis-catalog)

### Control Package

The control package `control_import.dtsx` is responsible for orchestrating multi-threaded data collection and execution of the Worker Package `import_remote_data.dtsx`
![SQLWATCH SSIS Control Package]({{ site.baseurl }}/assets/images/sqlwatch-control-package.png)

**Parameters**

*number_of_parallel_collectors*
The number of threads for parallel collection. If this is set to > 1, then multiple servers will be collected in parallel, in addition to each collector data flow being run in parallel, according to the MaxConcurrentExecutables parameter. Be careful as running parallel collectors may be slower than a single thread. Make sure the central repository can sustain the workload. Maximum allowed parallel threads are 8.

*repository_database* 
The name of the database where the central repository is. Default SQLWATCH.

*repository_instance_name*
The name of the SQL Server instance where the central repository is hosted.

*repository_password*
The SQL Password to access central repository or blank for Windows authentication.

*repository_user_name*
The SQL User to access central repository or blank for Windows authentication.

## Linked Server

>Linked Server approach is built programmatically and therefore easier to develop and maintain. It contains several improvements over the SSIS method, such as message logging to a table, automatic table import without the explicit declaration (SSIS requires metadata refresh which slows down the development) and forces full load of the header tables if missing keys are detected.
