---
parent: Getting Started
---

# Upgrading

SQLWATCH has been developed in Visual Studio which is based on [Declarative Deployment Model](https://blogs.msdn.microsoft.com/gertd/2009/06/05/declarative-database-development/). 
This means that when a database is deployed, the deployment mechanism works out what needs to be done to bring the target database to the desired version.

To upgrade SQLWATCH database one simply follows the installation process. 
The only exception is when using SSMS, there is an explicit Upgrade Option in the database context (Right-click on database -> Tasks -> Upgrade data-tier application):

![SSMS Upgrade DacPac]({{ site.baseurl }}/assets/images/ssms-upgrade-data-tier-application.png)

### Challenges
Whilst declarative deployment makes the development very easy and deployment very reliable (either all or nothing) it can be a bottleneck when large schema changes are required. 
For example, migrating data type from UNIQUE IDENTIFIER to INTEGER would fail as such conversion is impossible. 
When this happens, manual migrations scripts are required which will be noted in the release notes.
