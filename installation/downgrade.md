---
parent Getting Started
nav_order: 50
---

# Downgrade

---

Whilst it is possible to downgrade database schema by deploying a previous DacPac there are few things to have in mind. 
Database deployments are designed to avoid data loss which means that any modification that could result in data loss will be rejected. For example, if new columns have been added in the latest release, downgrading to the previous release would result in the removal of the new columns. By default this operation is not allowed, however, it can be forced with the SqlPackage.exe and the following parameters:

```
/p:BlockOnPossibleDataLoss=false
```

Please make sure you have tested the downgrade in a non-production environment first and that you are familiar with the SqlPackage.exe parameters available on the Microsoft Docs website before you downgrade database version.
