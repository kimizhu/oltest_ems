---
description: na
keywords: na
title: P1 - ContentRepository index service stopped working
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 964df4df-a1fe-4eae-995f-94c82ed2fa64
ms.author: dfchen@microsoft.com
---
# P1 - ContentRepository index service stopped working
P1 - ContentRepository index service stopped working
===

What happened?
---

Content Repository index service VMs on DevINT and PROD environments were experiencing unexpected shutdowns/reboots and were not working properly due to an Azure VM incident. **A subset of Content Repository APIs depend on index service returned 408 timeout** during the incident and detailed impacts (data based on CHIP site) for each environment were:

-  **DevINT**: **1 hour and 1 min** (from 2015-01-22 10:17:43 UTC to 2015-01-22 11:18:20 UTC), total **2223** APIs returned 408 timeout:
	* 1047 x Hierarchies.GetTenantChildren
	* 596 x Actions.Lookup
	* 314 x Permissions.Query
	* 261 x Hierarchies.GetEntityChildren
	* 5 x Hierarchies.GetEntityPositionAmongSiblings



-  **PROD**: **46 mins** (from 2015-01-22 10:18:12 UTC to 2015-01-22 11:06:48 UTC), total **712** APIs returned 408 timeout:
	* 404 x Hierarchies.GetTenantChildren
	* 238 x Hierarchies.GetEntityChildren
	* 55 x Permissions.Query
	* 14 x Actions.Lookup
	* 1	Hierarchies.GetEntityPositionAmongSiblings


What was the trigger?
---

Combined event trigger data from Trace database, VMs' event logs and Azure incident alerts, the timelines of this issue (by environment) are:

**DevINT (in UTC time)**

*  **2015-01-22 10:14:31**   DevINT index instance #2 shut down;
*  **2015-01-22 10:14:51**   DevINT index instance #1 shut down;
*  **2015-01-22 10:15:00**   Azure VM incident reported to start;
*  **2015-01-22 10:17:43**   APIs depend on index services started to fail;
*  **2015-01-22 10:19:01**   DevINT index instance #1 booted;
*  **2015-01-22 10:28:09**   DevINT index instance #1 shut down again;
*  **2015-01-22 10:36:30**   DevINT index instance #1 booted again;
*  **2015-01-22 10:51:24**   DevINT index instance #2 booted;
*  **2015-01-22 10:53:00**   Su Shi reported CR returned timeout errors when querying permission bindings;
*  **2015-01-22 11:18:20**   APIs depend on index service were back to normal;
*  **2015-01-22 11:30:00**   Azure VM incident reported to be resolved.


**PROD (in UTC time)**

*  **2015-01-22 10:15:00**   Azure VM incident reported to start;
*  **2015-01-22 10:18:12**   APIs depend on index started to fail;
*  **2015-01-22 10:28:30**   PROD index instance #2 shut down;
*  **2015-01-22 10:28:34**   PROD index instance #1 shut down;
*  **2015-01-22 10:49:13**   PROD index instance #2 booted;
*  **2015-01-22 10:49:38**   PROD index instance #1 booted;
*  **2015-01-22 11:06:00**   Su Shi reported he did not have permission on PROD ClientTest Tenant;
*  **2015-01-22 11:06:48**   APIs depend on index services were back to normal;
*  **2015-01-22 11:30:00**   Azure VM incident reported to be resolved;
*  **2015-01-22 13:24:00**   Su Shi verified the issue had been resolved.


What was the "root cause"?
---

There was an Azure VM incident (**LSI7531717 Virtual Machines and Storage - West US - Partial service interruption**) from **2015-01-22 10:15:00
UTC** to **2015-01-22 11:30:00 UTC**. During that period of time, 4 Content Repository index service VMs (2 x DevINT and 2 x PROD) were all affacted, thus index services were unavailable and APIs depend on index services returned 408 timeouts.

The summary of this issue provided by Azure team was:

>  **Summary**: a subset of customers using Virtual Machines in West US may have been unable to access their VMs due to a storage issue impacting part of the region.


And the Technical Information of this incident (also provided by Azure team):

>  **Technical Information**
>
>  *  WALS received multiple alerts for IaaS VM failures and few Gomez alerts for BY3PrdStr04 Cluster
>  *  Customers would have faced VM failures as a result. The VMs were spread across different clusters in the region, but all using the impacted storage cluster.
>  *  XStore confirmed that there was a large disk failure on the Cluster during that time.
>  *  Exact cause of the disk failure was not known, but the issue self-mitigated and  MDS and Gomez received condition improved alerts.
>  *  XStore team still on call and will continue to investigate the root cause.
>  *  1 CRI.


What did we learn from the event?
---

1.  Backup / failover options should be provided for critical components.
2.  For live site issues, combine information from different sources can make it easier to identify the problem.
 

What are we going to do?
---

1. (Already done) Work with Insight team to set an extra rule for 408 timeout errors, which should be treated as 5xx system errors like sending alert emails.
2. (S78) Investigate on geo-replication for index service.
3. (S78) Investigate on importing Elastic search's logs to Insight's trace database automatically.
4. (S79) Consider adopting Azure Search service. Investigate from view of stability, availability, performance, scalability, etc. 