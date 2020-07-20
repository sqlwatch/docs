---
nav_order: 20
has_children: true
title: Configuration
---

# Configuration

---

SQLWATCH has been designed with "set it and forget it" approach and does not require any maintenance or configuration to get started. It will run out of the box. However, some configuration is available.

- TOC 
{:toc}

## Basic Configuration

### Blocked Process Monitor (important)

For SQLWATCH to record blocking chains we have to enable The `blocked process threshold` for a specific time window. Learn more about [Blocked Process Threshold](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/blocked-process-threshold-server-configuration-option)

The blocked process monitor will log any transactions (queries) being blocked for longer than the set threshold. In the example below, we are setting it to log blocking chains lasting longer than 15 seconds.

```
exec sp_configure 'show advanced options', 1 ;  
RECONFIGURE ;  
exec sp_configure 'blocked process threshold', 15 ;  
RECONFIGURE ; 
```

To make this easier, SQLWATCH comes with a stored procedure that can execute the above configuration:

```
--default threshold will be 15 seconds:
exec [dbo].[usp_sqlwatch_config_set_blocked_proc_threshold] 

--to applly different threshold:
exec [dbo].[usp_sqlwatch_config_set_blocked_proc_threshold] @threshold_seconds = x 
```

### Table and Index compression

You may wish to compress data in SQLWATCH to improve storage utilisation and I/O performance at the cost of CPU utilisation. You can do so by running:

```
exec [dbo].[usp_sqlwatch_config_set_table_compression];
exec [dbo].[usp_sqlwatch_config_set_index_compression];
```

### Collection schedules

You may adjust collection schedules to your liking, however, it is not recommended to change the frequency of the `SQLWATCH-LOGGER-PERFORMANCE`. It has been optimised to run every minute. Apart from reducing [collection granulatiry](https://sqlwatch.io/blog/impact-of-aggregation-on-granularity-and-observability/) it may cause some dashboard to return null data for some aggregation. The collector also offloads data from XE sessions and if not run frequently, sessions will start rolling over and overwriting collected data.

### Retention periods

Retention periods are configurable for each snapshot and stored in the `[dbo].[sqlwatch_config_snapshot_type]` table in the `[snapshot_retention_days]` column. If you are offloading data to a central repository or Azure, you can drop local retention to a day or two. 

The action retention is executed by `SQLWATCH-INTERNAL-RETENTION` and runs every hour by default.

### Performance counters

Performance counters collected by SQLWATCH are defined in table `[dbo].[sqlwatch_config_performance_counters]`.
Collection of individual performance counters can be set using the `collect` column (1=yes, 0=no). Disabling the default performance counter collectors will stop them from being collected and therefore may break the default dashboard. New collectors can be added to the list if required. 

Please note the `instance_name` is dynamic and contains actual names of objects i.e. database names. The collection definition takes this into account with a dynamic approach:


|                                               Definition                                               |                                                                                                                                                                  Description                                                                                                                                                                   |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Will translate to literal 'Elapsed Time:Total(ms)' or '' Note that instance name must a valid instance | Will translate to literal 'Elapsed Time:Total(ms)' or '' Note that instance name must a valid instance                                                                                                                                                                                                                                        |
| _Total                                                                                                 | Will only include '_Total' instances. This is useful if we only want to collect high-level aggregates and are not interested in low-level objects i.e. database                                                                                                                                                                                |
| <* !_Total>                                                                                            | Will not include '_Total' instances, i.e. it will collect any other instances for this particular counter_name but not totals. This is useful if we want to collect low-level objects i.e. database performance and will be aggregating and calculating totals, for example in Power BI. In this case, there is no value in collecting totals.  |

### Recreate agent jobs

To create all default SQLWATCH agent jobs you can run:

```
--add any missing SQLWATCH jobs, will not remove existing SQLWATCH jobs:
exec [dbo].[usp_sqlwatch_config_set_default_agent_jobs]

--remove existing and recreate all SQLWATCH jobs:
exec [dbo].[usp_sqlwatch_config_set_default_agent_jobs] @remove_existing = 1
```

## Global (Application) Configuration Items

The table `[dbo].[sqlwatch_config]` holds configuration items that influence the way the application works.

### Logging level

By default, SQLWATCH will only log Warnings and Errors in the `[dbo].[sqlwatch_app_log]` table. To enable verbose (informational) logging you can change the item 7 value to 1, or to 0 to disable verbose logging:

```
  update [dbo].[sqlwatch_config]
  set config_value = 1 
  where config_id = 7
```

Remember to change it back to 0 after you have done investigating the issue as it may cause the `app_log` table to grow rapidly.
