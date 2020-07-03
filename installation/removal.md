---
parent: Getting Started
nav_order: 99
---

# Removal

---

SQLWATCH is a SQL Server database. It can be removed automatically via dbatools:

```
Uninstall-DbaSqlWatch
```

When removing manually please ensure the follwowing objects are removed:
- SQLWATCH database
- Extended Events (`SQLWATCH-%`)
- Agnet Jobs (`SQLWATCH-%`)
- Any additional PowerShell scripts used by the Actions engine (only if you have installed any manually, SQWALTCH does not create anything on the disk)
