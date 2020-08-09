---
parent: Central Repository
grand_parent: Configuration
title: SqlWatchImport.exe
nav_order: 10
---

# Import remote data using `SqlWatchImport.exe`
{: .no_toc }
---

- TOC 
{:toc}

## Overview

**This documentation is in progress.**

The console application has been written to efficiently handle imports from the remote instances. This is the preferred way. The application was written to address the following problems:

* SSIS is fast but cumbersome to maintain - every time new table or column is changed or added to the SqlWatch database, the package requires manual changes to reflect database changes. This is very time consuming and error prone. I am also trying to stay away from BIML to reduce complexity of the solution.
* Threading in SSIS is limited by the design of the package. 
* Not everyone has Integration Server.
* Linked Server does not require as much maintenance but it does rely on SQL Agent for data collection so cannot be run on SQL Express directly.
* Linked Server is not as fast as SSIS and does not handle XML fields.

The `SqlWatchImport.exe` leverages the performance of `SqlBulkCopy` and data streaming for fast inserts and .NET framework for Thread and Connection pooling which makes it very efficient. In my test setup, importing data from the same, single remote instance takes ~10 seconds in SSIS and Linked Server and 1.2 second using the console application.

## Configuration

The configuration parameters are in the `App.config` file.

## Console parameters

In addition to the normal, unattended execution, the application accepts a number of arguments.
Please run `SqlWatchImport.exe -h` for help.

