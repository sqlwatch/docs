---
title: Known Issues
nav_order: 40
---

# Known Issues
{: .no_toc }
---

- TOC
{:toc}

## Collation conflict

If you encounter collation conflict during installation it means that your server’s collation is different from the SQLWATCH database collation and SQL cannot handle string comparisons.
`We use the Latin1_General_CI_AS collation`.

If you encounter this issue you can:

**Manually create an empty database**

By default, SQL Server creates new databases with the default servers' collation unless otherwise specified. The collation cannot be changed once the database has been created. By manually creating SQLWATCH database will force default collation.

```
CREATE DATABASE [SQLWATCH]
GO
```

Once the empty shell database has been created we can proceed with the installation as usual.

**Rebuild the project into the desired target collation**

If the above does not meet your requirements you can load the database solution in Visual Studio (please follow "Deploy from source code" guide for details) , change database collation to the desired value and rebuild and deploy.
Rebuilding the project into specific collation may result in a better performance. 

## Database drift

In some cases, you may receive the following error when upgrading from DacPac:

```
Database has drifted from its registered data-tier application
```

When SQLWATCH is deployed, it registers itself as data-tier application, which adds a record in `msdb.dbo.sysdac_instances`. 
The error may happen when database objects have been changed since the last deployment, for example, when you deploy SQLWATCH into your existing "dba_tools" database and add or remove some other tables, not related to SQLWATCH.

Registering data-tier application is useful to identify the currently installed version, however since version 2.x we have alterative method where we log this information directly in the SQLWATCH table there is no need for it.

**Unregister Data-Tier Application**

To work around this issue, we have to unregister the data-tier application. This can be done in SQL Server Management Studio: Right-click database -> Tasks -> Delete Data-Tier Application.

Alternatively, you can use T-SQL to achieve the same:

```
declare @instance_id uniqueidentifier
declare @database_name sysname = 'SQLWATCH'

select @instance_id = instance_id
from msdb.dbo.sysdac_instances dp 
where dp.instance_name = @database_name

exec dbo.sp_sysdac_delete_instance 
	    @instance_id = @instance_id
    
exec dbo.sp_sysdac_update_history_entry 
    @action_id=21,
    @instance_id=@instance_id,
    @action_type=14,
    @dac_object_type=0,
    @action_status=2,
    @dac_object_name_pretran=@database_name,
    @dac_object_name_posttran=@database_name,
    @sqlscript=NULL,
    @error_string=N''
```
