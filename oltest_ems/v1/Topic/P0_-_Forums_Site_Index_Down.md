---
description: na
keywords: na
title: P0 - Forums Site Index Down
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 2ec042d9-f67e-4a04-affc-a6f20600b196
ms.author: jixin@microsoft.com
ms.createdat: 11/25/2015 2:40:47 AM
---
# P0 - Forums Site Index Down
## P0 MSDN Forums Site Index Down ##

### What happened?
Users will see a service down error while accessing the forums site.
### What was the trigger?
(Redmond time)
*	** 2015-11-13 11:45 PM ** Ops team sent an incident notification email reported the issue
*	** 2015-11-13 12:02 PM ** Site is back. Katarina restarted the Elastic Index Search Cluster. The index recovered and the site content began to resolve.
*	** 2015-11-13 12:57 PM ** The content within the Forums site is showing intermittent search service down errors, due to continued problems within the Elastic Search Index Cluster.
*	** 2015-11-13 2:26 PM ** Site is back. Ops team has found the root cause of the index flickering, an Elastic Search cluster VIP node within the load balancer which had been mistakenly renamed, causing secondary to main node association issues.  After taking this renamed node off the load balancer, and restarting the Elastic Search services which were now able to associate to a functional main node, this issue was remedied.
*	** 2015-11-14 2:58 PM ** The Forums website is intermittently showing the "service down" message again.
*	** 2015-11-15 2:10 AM ** The Elastic Search cluster index was reconfigured and rebuilt, resolving the flickering issues. Site back to normal.
 
### What was the "root cause"?
For the first time incident occurred, it was caused by an Elastic Search cluster master node SOC20 had a network problem(IP incorrect for some time) which made it outage, also with inconsistent ES configuration on the cluster nodes, some point to SOC20, some set to discovery, and this led to an associate issue. Katarina changed the master node to SOC13 and restart the Elastic Search index service to recover it. Ops team had create a ticket to Lab team for investigating the network issue.

And the next day's incident was caused by low disk and inconsistent Elastic Search configuration for all the nodes. At this time, the configuration was not uniformed, master node already changed to SOC13, but still some nodes point to SOC20 with discovery mode. Katarina was waiting a low traffic time to correct them all. However, due to the issue the day before, a lot dump files were created under C drive led to a low disk issue. With a few attempts, they got the correct configuration was set one node to master and others point to master also configure node.master=false. With all the nodes reconfigured and rebuilt, the cluster was back.  
 
### What did we learn from the event?
* Elastic Cluster is a key component to Forums and more monitoring needs to be added.  Any work needs to be completed with extreme caution due to its high risk.
### What are we going to do?
* Prepare a second backup master configuration for patching purposes.( [472146](https://mseng.visualstudio.com/web/wi.aspx?pcguid=0efb4611-d565-4cd1-9a64-7d6cb6d7d5f0&id=472146) created for Jialin and Ops team will apply to PROD before patching)
* To add network, cluster health monitoring, review disk space threshold monitoring, tune configs with two master nodes, allowing for patching. ([472927](https://mseng.visualstudio.com/DefaultCollection/VSChina/_workitems/edit/472927?fullScreen=false) created for Katarina)
* Update auto-dump file to E drive ([472925](https://mseng.visualstudio.com/DefaultCollection/VSChina/_workitems/edit/472925?fullScreen=false) created for Katarina)
