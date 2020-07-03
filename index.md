---
nav_order: 1
permalink: /
title: Home
---

## Free and Open Source SQL Server Monitoring Framework

1. TOC
{:toc}

## Concept

SQLWATCH was primarily developed for decentralised Performance Monitoring, ad-hoc Performance Testing and Performance Data Logging in Production Environments for reactive analysis - i.e. to investigate problems after they have occurred. It relies on the SQL Server Agent to invoke local data collection.

>Each SQL Server monitors itself and alerts only when it needs attention. Power BI Report can be used to analyse historical performance data. To meet the popular demand for central reporting, an optional centralised reporting repository was recently introduced. 

Most enterprise monitoring solutions are centralised which means they often consist of a central repository and monitoring servers, where monitoring servers execute queries against the monitored SQL instance and send the results back to the repository. Whilst this approach has a lot of benefits it also requires a set of dedicated monitoring infrastructure, servers, licensing, and network configuration to allow remote access to the monitored instances which can add complexity and increase the cost. 
It can also become a single point of failure and a bottleneck. Some solutions also require monitoring agent to be installed locally further increasing complexity. Any network outages between the monitoring server and monitored instance could cause gaps in the collected data. 

SQLWATCH has been designed to address some of these challenges, especially in smaller or test environments where dedicated monitoring infrastructure is not feasible. Since we are monitoring SQL Server, we already have one so we can also use it to store the monitoring data.  

Automation and integration with dbatools make it easy to keep decentralised deployment in sync and up to date.

## Components


SQLWATCH is made of:
- SQLWATCH database
- Dashboards
- Optional SSIS collector

## Performance Overhead

Whilst the decentralised approach can be easier to implement, it introduces additional performance overhead. As we are reading the performance data form SQL Server Dynamic Management Views, we also have to write it on the same instance. SQLWATCH is designed with minimum overhead. It utilises SQL Server Extended Events (XES) where possible and Dynamic Management Views (DMV) collectors that run every minute by default.

> SQL Server The Extended Events architecture enables users to collect as much or as little data as is necessary to troubleshoot or identify a performance problem. Extended Events is configurable, and it scales very well.

The frequent data collectors such as performance run every minute and take less than a second to execute. There are areas for improvement in the way the XML output from XE sessions is being parsed which will be addressed in the future releases.

The below screenshot shows a 60 seconds window of CPU utilisation of the `sqlservr.exe` process. The spike is the performance collection which lasts a few milliseconds:

![SQLWATCH CPU Impact](/assets/sqlwatch-perf-collection-cpu-impact.png)

## Requirements

SQLWATCH has been tested with the following SQL Server editions and versions: 
* SQL Server Standard, Enterprise and Express:
  * 2008 R2 SP3
  * 2012
  * 2014
  * 2016
  * 2017
  * 2019
  
Data collection is invoked via SQL Server Agent Jobs since the Express Edition does not have Agent Job, the invocation must happen via Windows Task Scheduler or alternative way. SQLWATCH can generate the required commands to Windows Scheduled tasks:

```sql
exec [dbo].[usp_sqlwatch_config_set_default_agent_jobs] @print_WTS_command = 1
```

Additional reading
- [Extended Events](https://docs.microsoft.com/en-us/sql/relational-databases/extended-events/extended-events)
- [Dynamic Management Views](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

## Storage Utilisation

SQLWATCH schema has been designed with long term storage in mind. If possible, it is advisable to enable page compression on SQLWATCH data tables and indexes reducing utilisation by further 25-50%. The amount of space used by SQLWATCH depends on the retention period, the number of databases on the server and the workload. As guidance, below is the size of SQLWATCH in my test environment, with data compression enabled, after 30 days of use:

![SQLWATCH Storage utilisation](/assets/sqlwatch-storage-utilisation-example.png)

>Appropriate index maintenance must in place in order to make sure table size is not being bloated over time.