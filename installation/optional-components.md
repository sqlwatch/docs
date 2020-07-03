---
parent: Getting Started
nav_order: 2
---

# Optional Components
{: .no_toc }

---

SQLWATCH can also capture output from the following optional components. 

- TOC
{:toc}

## sp_WhoIsActive

SQLWATCH will log output from Adam Machanic's, fantastic `sp_WhoIsAcitve` which can also be installed using dbatools. SQLWATCH will look for the procedure in either the `master` database or the database where the SQLWATCH is installed (default SQLWATCH but it could any database)

```
Install-DbaWhoIsActive -SqlInstance YourServer -Database master
```

## dbachecks

https://dbachecks.readthedocs.io/en/latest/
