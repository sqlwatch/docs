---
title: Getting Started
nav_order: 10
---

## Getting Started

SQLWATCH is a SQL Server database with some agent jobs. Installing SQLWATCH means deploying the database and corresponding agent jobs. 
It must be installed on each monitored SQL Server instance. 

The project has been developed in Visual Studio Data Tools and the base for deployments are Data Application Tier Packages (DacPac). 
This makes installation very simple and the easiest way is to install with [dbatools](https://dbatools.io/).

### Install with dbatools

dbatools support multi-server installation with a single command:

### Stable Release

```
Install-DbaSqlWatch -SqlInstance Server1, Server2, Server3 -Database SQLWATCH
```

### Beta (Pre) Release

```
Install-DbaSqlWatch -SqlInstance DevServer1 -Database SQLWATCH -PreRelease
```

<div class="responsive-iframe-container responsive-iframe-container-4-3">
  <iframe class="responsive-iframe" src="https://www.youtube-nocookie.com/embed/W38osuBv_Q8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

>The `Install-DbaSqlWatch` was designed for unattended multi-server installations. It will download the latest release and unpack it, including the Power BI dashboard, into its own temporary directory.
