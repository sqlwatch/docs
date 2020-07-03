---
nav_order: 1
permalink: /
---

## Concept


SQLWATCH was primarily developed for decentralised Performance Monitoring, ad-hoc Performance Testing and Performance Data Logging in Production Environments for reactive analysis - i.e. to investigate problems after they have occured. It relies on the SQL Server Agent to invoke local data collection.

>Each SQL Server monitors itself and alerts only when it needs attention. Power BI Report can be used to analyse historical performance data. To meet the popular demand for central reporting, an optional centralised reporting repository was recently introduced. 

Most enterprise monitoring solutions are centralised which means they often consist of a central repository and monitoring servers, where monitoring servers execute queries against the monitored SQL instance and send the results back to the repository. Whilst this approach has a lot of benefits it also requires a set of dedicated monitoring infrastructure, servers, licensing, and network configuration to allow remote access to the monitored instances which can add complexity and increase the cost. 
It can also become a single point of failure and a bottleneck. Some solutions also require monitoring agent to be installed locally further increasing complexity. Any network outages between the monitoring server and monitored instance could cause gaps in the collected data. 

SQLWATCH has been designed to address some of these challenges, especially in smaller or test environments where dedicated monitoring infrastructure is not feasible. Since we are monitoring SQL Server, we already have one so we can also use it to store the monitoring data.  

Automation and integration with dbatools make it easy to keep decentralised deployment in sync and up to date.
