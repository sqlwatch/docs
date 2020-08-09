---
parent: Configuration
title: Central Repository
nav_order: 20
has_children: true
---

# Centrap Repository Overview
{: .no_toc }
---

The central repository is an ordinary SQLWATCH database where data from other instances is being imported for centralised reporting. Any SQLWATCH database can become a central repository. Data collection can happen via [SQL Server Integration Package (SSIS)](https://docs.microsoft.com/en-us/sql/integration-services/sql-server-integration-services) or via [Linked Server](https://docs.microsoft.com/en-us/sql/relational-databases/linked-servers/linked-servers-database-engine). 

- TOC 
{:toc}

The performance impact on the remote instance will be mainly driven by the amount of data we are pulling with each connection. The collection from the remote instance into the central repository is primarily delta with few exceptions of full load. The more often we are pulling data into the central repository the less impact it will have. Below is an example of the CPU utilisation of the remote instance whilst pulling a 5 minutes delta into the central repository. Note that pull does not last longer than a few seconds:
![SQLWATCH Central Repository impact on remote instance]({{ site.baseurl }}/assets/images/sqlwatch-central-repository-reading-impact.png)

The permission required to collect data from the remote instance is `db_datareader`. Permissions required to insert data on the central repository are: `db_datareader` to read parameters, `db_datawriter` to write data and `db_ddladmin` for bulk inserts.

Central Repository can download data from remote instances in two ways:

- SQL Server Integration Services (SSIS)
- Linked Server

Both methods do a FULL load of the relatively small `meta*` tables and delta loads of the `logger*` tables that contain the actual performance data. Whilst SSIS is a performance optimised engine and may perform faster, there is no noticable performance advantage of using either method and both perform in a similar way when pulling remote data. The advantage of using Linked Server is that it does not require SSIS and can be run on a SQL Server Express Edition with jobs invoked via Windows Scheduled Tasks instead of the SQL Agent.

>Linked Server approach is built programmatically and therefore easier to develop and maintain. It contains several improvements over the SSIS method, such as message logging to a table, automatic table import without the explicit declaration (SSIS requires metadata refresh which slows down the development) and forces full load of the header tables if missing keys are detected.

## Adding remote server to collection
In both cases, the configuration of the remote instance is the same. For the central repository to know which remote instances to collect data from, they must be defined in `[dbo].[sqlwatch_config_sql_instance]`. This can be achieved by directly inserting data into the table, or by executing a stored procedure:

![SQLWATCH Config SQL Instance]({{ site.baseurl }}/assets/images/sqlwatch-config-sqlinstance.png)

```
exec [dbo].[usp_sqlwatch_config_repository_add_remote_instance]
    @sql_instance --sql instance name,
    @hostname --hostname, if different to the @sql_instance, for example this could be in IP if no DNS records present,
    @sql_port --non standard sql port, leave NULL for the default 1433,
    @sqlwatch_database_name --name of the SQLWATCH database,
    @environment --name of the environment (DEV,PROD,QA or anything) - this is for the user convinience,
    @linked_server_name --name of the linked server, a new LS will be created if not exists. If you prefer to use existing LS, leave this blank and manually update [linked_server_name] in [dbo].[sqlwatch_config_sql_instance]. If you are using SSIS, leave NULL. 
    @rmtuser --username for the linked server authentication, leave NULL for default Windows Auth or when using SSIS,
    @rmtpassword --password for the linked server authentication, leave NULL for default Windows Auth or when using SSIS,
```
## Removing remote server from central repository
To remove remote server from the central repository and DELETE all of its data, run the following:
```
delete from [dbo].[sqlwatch_config_sql_instance]
where sql_instance = 'SQL-1'
```
This will REMOVE all data assosiated with this instance from the central repository. The deletion will run in small batches to avoid performance impact and thus it may take a long time - few minutes to even few hours but with minimum impact. It is advisable to run this as a one off agent job and leave until it is finished. 

## Ways to import data

Data from remote instances can be imported into the central repository in several ways:
* Using `SqlWatchImport.exe` - a proprietary console application specifically designed and optimised to import remote SqlWatch data (from version `3.x`)
* SSIS package
* Linked Server 

Check the TABLE OF CONTENTS below for details