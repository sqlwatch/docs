---
nav_order: 10
parent: Configuration
title: Data Collection
---

# Data Collection
{: .no_toc }
---

- TOC 
{:toc}

## Collection schedules

You may adjust collection schedules to your liking, however, it is not recommended to change the frequency of the `SQLWATCH-LOGGER-PERFORMANCE`. It has been optimised to run every minute. Apart from reducing [collection granulatiry](https://sqlwatch.io/blog/impact-of-aggregation-on-granularity-and-observability/) it may cause some dashboard to return null data for some aggregation. The collector also offloads data from XE sessions and if not run frequently, sessions will start rolling over and overwriting collected data.

## Performance counters

Performance counters collected by SQLWATCH are defined in table `[dbo].[sqlwatch_config_performance_counters]`.
Collection of individual performance counters can be set using the `collect` column (1=yes, 0=no). Disabling the default performance counter collectors will stop them from being collected and therefore may break the default dashboard. New collectors can be added to the list if required. 

Please note the `instance_name` is dynamic and contains actual names of objects i.e. database names. The collection definition takes this into account with a dynamic approach:


|                                               Definition                                               |                                                                                                                                                                  Description                                                                                                                                                                   |
|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Will translate to literal 'Elapsed Time:Total(ms)' or '' Note that instance name must a valid instance | Will translate to literal 'Elapsed Time:Total(ms)' or '' Note that instance name must a valid instance                                                                                                                                                                                                                                        |
| _Total                                                                                                 | Will only include '_Total' instances. This is useful if we only want to collect high-level aggregates and are not interested in low-level objects i.e. database                                                                                                                                                                                |
| <* !_Total>                                                                                            | Will not include '_Total' instances, i.e. it will collect any other instances for this particular counter_name but not totals. This is useful if we want to collect low-level objects i.e. database performance and will be aggregating and calculating totals, for example in Power BI. In this case, there is no value in collecting totals.  |