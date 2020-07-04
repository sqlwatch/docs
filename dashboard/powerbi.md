---
title: Dashboards
has_children: true
nav_order: 30
---

# Power BI dashboard
{: .no_toc }

---

SQLWATCH comes with a [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) dashboard to help visualise and analyse collected data. 
It's a free Windows application that can be downloaded from their [website](https://powerbi.microsoft.com/en-us/get-started/).

>Power BI Desktop release cycle is so frequent that is difficult to target a specific version, please refer to the release notes on GitHub which version to use.

Power BI is very efficient in handling data and displaying visuals. It is also very efficient in data storage making files very small. 90% of Power BI Performance will depend on your local desktop. 
I have used SQLWATCH dashboard comfortably on a Surface 4 with i7 and 8GB RAM. [Hardware requirements](https://docs.microsoft.com/en-us/power-bi/desktop-get-the-desktop#minimum-requirements)

SQLWATCH Dashboard can also run in the Power BI Service. There are no custom requirements for Visuals or any other components. Neither R nor Python are required.

1. TOC 
{:toc}

## Permissions

No admin permissions are required on the workstation to run Power BI. 
Database permissions required to ingest data into Power BI are `SELECT` on views only. `db_datareader` will be sufficient too. 

## Getting Started

To set started, simply open the SQLWATCH dashboard (.`pbx`) in the Power BI Dekstop application.

>Please note, if you have deploued SQLWATCH using dbatools, the deployment package has been downloaded to the PowerShell temporary directory. This will also include the Power BI template. If you are struggling to find it, you can grab a copy from [GitHub](https://github.com/marcingminski/sqlwatch/releases/latest) 

## Parameters
