---
title: FAQ
nav_order: 500
---

# Frequently Asked Questions
{: .no_toc }
---

- TOC
{:toc}

# The `date_last_seen` is not showing the correct date.
This field is on some of the `sqlwatch_meta*` tables and used to remove old items as part of the retention process. By default, anything "not seen" in over 32 days will be removed.
When the any of the meta tables is merged, this field is also updated for any matched rows to ensure they are not deleted as part of the retention process. Any new rows have the `date_last_seen` set as `getutcdate()` 

```
merge table_target as target
using table_source as source
on target.column = source.column

when matched then
    update set date_last_seen = getutcdate()

when not matched then
    insert (column, date_last_seen)
    values (value, getutcdate())
```

Since the version `3.x`, the `SqlWatchImport.exe` is also using this field when importing data to limit the number of rows and to speed up the import. On systems where both, the `SqlWatchImport.exe` and the merge run frequently we would be importing the same data with every run. To speed up the import process, we are delaying the update of the `date_last_seen` until it at least 24 hours old:


```
merge table_target as target
using table_source as source
on target.column = source.column

when matched then and datediff(hour,date_last_see,getutcdate()) > 24
    update set date_last_seen = getutcdate()

when not matched then
    insert (column, date_last_seen)
    values (value, getutcdate())
```
