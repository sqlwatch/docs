---
title: Getting Started
nav_order: 10
---

# Getting Started
{: .no_toc }
---

SQLWATCH is a SQL Server database with some agent jobs. Installing SQLWATCH means deploying the database and corresponding agent jobs. 
It must be installed on each monitored SQL Server instance. 

The project has been developed in Visual Studio Data Tools and the base for deployments are Data Application Tier Packages (DacPac). 
This makes installation very simple and the easiest way is to install with [dbatools](https://dbatools.io/).

- TOC
{:toc}

## Install with dbatools

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

## Install with SqlPackage

The `SqlPackage.exe` is a command line utility that automates SQL Server database deployments. This command comes with SQL Server Management Studio (SSMS) and is located in the ...\DAC\bin folder: `C:\Program Files (x86)\Microsoft SQL Server\140\DAC\bin\SqlPackage.exe` Where 140 is the version of installed Management Studio. Your version and path may be different to the example. [Learn more about SqlPackage](https://docs.microsoft.com/en-us/sql/tools/sqlpackage)

To install SQLWATCH using `SqlPackage.exe`, please download the required release from our GitHub Releases and unzip. In this example we are using C:\Temp:

```
SqlPackage.exe 
    /Action:Publish 
    /SourceFile:C:\Temp\SQLWATCH.dacpac 
    /TargetDatabaseName:SQLWATCH 
    /TargetServerName:YOURSQLSERVER 
    /p:RegisterDataTierApplication=True
```

## Install with SSMS

SQL Server Management Studio (SSMS) is a free, integrated development environment (IDE) for SQL Server. [Learn more about SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms).

To install SQLWATCH using SSMS, please download the required release from our GitHub Releases and unzip. In this example we are using C:\Temp
Connect to the desired SQL Server, right click on Databases and select Deploy Data-Tier Application. Find the SQLWATCH.dacpac you have unzipped and follow the instructions:


<div class="responsive-iframe-container responsive-iframe-container-4-3">
  <iframe class="responsive-iframe" src="https://www.youtube-nocookie.com/embed/caufO79tKo4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
