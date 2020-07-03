---
has_children: true
nav_order: 3
---

## Requirements

SQLWATCH has been tested with the following SQL Server editions and versions: 
* SQL Server Standard, Enterprise and Express:
  * 2008 R2 SP3
  * 2012
  * 2014
  * 2016
  * 2017
  * 2019
  
Data collection is invoked via SQL Server Agent Jobs, since the Express Edition does not have Agent Job, the invokation must happen via Windows Task Scheduler or alternative way. SQLWATCH can generate the required commands to Windows Scheduled tasks:

```sql
exec [dbo].[usp_sqlwatch_config_set_default_agent_jobs] @print_WTS_command = 1
```
