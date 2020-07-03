---
parent: Getting Started
nav_order: 2
---

## Optional Components
{: .no_toc }

---

The following optional components can be installed:

- TOC
{:toc}

##sp_WhoIsActive

SQLWATCH can log output from Adam Machanic's, fantastic `sp_WhoIsAcitve` which can also be installed using dbatools:

```
Install-DbaWhoIsActive -SqlInstance YourServer -Database master
```

SQLWATCH will look for the procedure in either the `master` database or the database where the SQLWATCH is installed (default SQLWATCH but it could any database)
