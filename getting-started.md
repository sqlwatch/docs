---
title: Getting Started
---

## Getting Started

SQLWATCH is a SQL Server database with some agent jobs. Installing SQLWATCH means deploying the database and corresponding agent jobs. 
It must be installed on each monitored SQL Server instance. 

The project has been developed in Visual Studio Data Tools and the base for deployments are Data Application Tier Packages (DacPac). 
This makes installation very simple and the easiest way is to install with [dbatools](https://dbatools.io/).

### Install with dbatools

dbatools support multi-server installation with a single command:

```PS
Install-DbaSqlWatch -SqlInstance Server1, Server2, Server3 -Database SQLWATCH
```

[![SQLWATCH install with dbatools](https://img.youtube.com/vi/W38osuBv_Q8/0.jpg)](https://www.youtube.com/watch?v=W38osuBv_Q8)

<iframe width="100%" height="600" src="http://www.youtube.com/embed/W38osuBv_Q8" frameborder="0" allowfullscreen></iframe>