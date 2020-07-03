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

The SSIS package can be easily deployed using the provided `.ispac` file

<div class="responsive-iframe-container responsive-iframe-container-16-9">
  <iframe class="responsive-iframe" src="https://www.youtube-nocookie.com/embed/RKfOBlTXk_A" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
