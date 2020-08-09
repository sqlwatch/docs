---
parent: Central Repository
grand_parent: Configuration
title: Linked Server
nav_order: 30
---

# Import remote data using Linked Server

In order to invoke collection via Linked Server, a linked server object to the SQLWATCH database on each monitored instance must be created. This can be achieved by executing stored procedure `[dbo].[usp_sqlwatch_user_repository_create_linked_server]`

## Create all required linked servers

The procedure can create all required linked servers as per the `[linked_server_name]` column in `[dbo].[sqlwatch_config_sql_instance]` table:

```
exec [dbo].[usp_sqlwatch_config_repository_create_linked_server]
    @rmtuser --optional user name for the remote instance (same for all) or blank to use default windows auth,
    @rmtpassword --optional password for the remote instance (same for all) or blank to use default windows auth
```

## Create a specific linked server

Alternatively, it can create a specific linked server. This is the default behaviour when executing `[dbo].[usp_sqlwatch_user_repository_add_remote_instance]`

```
exec [dbo].[usp_sqlwatch_config_repository_create_linked_server]
    @sql_instance --name of the existing sql instance in [dbo].[sqlwatch_config_sql_instance],
    @linked_server --optional, name of the required linked server. if blank a default name will be created,
    @rmtuser --optional user name for the remote instance (same for all) or blank to use default windows auth,
    @rmtpassword --optional password for the remote instance (same for all) or blank to use default windows auth
```

[Learn more about creating Linked Servers](https://docs.microsoft.com/en-us/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine)

## Create remote collector jobs

Linked Server collector can be multithreaded and there is no limit on the number of threads providing the performance of the central repository is adequate. The linked server approach creates a table based queue of all remote objects to import with the required dependency (i.e. meta tables first, the logger tables) in `[dbo].[sqlwatch_meta_repository_import_queue]`. The queue can be then processed by executing stored procedure:exec `[dbo].[usp_sqlwatch_repository_remote_table_import]`. To increase the number of import threads schedule the above procedure multiple times. To create default repository agent jobs, please execute the below procedure:

```
exec [dbo].[usp_sqlwatch_config_repository_create_agent_jobs]
    @threads = --number of thread jobs to create
```

This will result in the following jobs to be created:
1. A single enqueuing job that creates a list of remote objects to pull data from ([dbo].[sqlwatch_meta_repository_import_queue]) and ultimately controls how often they are processed. 
1. A single or multiple import jobs, depending on the @threads variable that will process the import queue. These jobs can run every 1 minute and will process any outstanding items in the queue table. 

```
  SQLWATCH-REPOSITORY-IMPORT-ENQUEUE
  SQLWATCH-REPOSITORY-IMPORT-T2
  SQLWATCH-REPOSITORY-IMPORT-T3
  SQLWATCH-REPOSITORY-IMPORT-T4
  SQLWATCH-REPOSITORY-IMPORT-T5
  SQLWATCH-REPOSITORY-IMPORT-T6
  SQLWATCH-REPOSITORY-IMPORT-T7
  SQLWATCH-REPOSITORY-IMPORT-T8
```

## Execution

Each thread registers itself in the threads table `[dbo].[sqlwatch_meta_repository_import_thread]` which contains the name of the SQL Agent Job currently running the thread. When the thread completes, it is also removed from the threads table.

Import status of each object can be seen in the '[dbo].[sqlwatch_meta_repository_import_status]' table.
