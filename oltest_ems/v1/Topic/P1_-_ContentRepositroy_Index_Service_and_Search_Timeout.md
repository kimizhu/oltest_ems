---
description: na
keywords: na
title: P1 - ContentRepositroy Index Service and Search Timeout
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 30285590-6585-4135-889a-4b13c5e4790c
ms.author: jiache@microsoft.com
---
# P1 - ContentRepositroy Index Service and Search Timeout
## P1 - ContentRepositroy Index Service and Search Timeout ##

### What happened? ###
Start from 2015-03-31 01:00 AM (Shanghai time), API calls to CR index and search experienced intermittent high latency or timeout. All environment (Sandbox, Devint, Prod) were impacted.

### What was the trigger? ###

**Start** (Shanghai time)

- **2014-03-31 01:00 AM** From CR index server logs, all connections (internal connection in virtual network) among cluster nodes were lost. Nodes can't communicate to each other even after 3 retries.
- **2014-03-31 08:02 AM** PROD E2E report show 40% pass rate start since 1 AM.
- **2014-03-31 08:44 AM** Ted did opened four bugs by mail. ([14897](https://capservice.visualstudio.com/web/wi.aspx?pcguid=3afc7cea-e643-4785-a32c-e3a73e6f08db&id=14897), [14898](https://capservice.visualstudio.com/web/wi.aspx?pcguid=3afc7cea-e643-4785-a32c-e3a73e6f08db&id=14898), [14900](https://capservice.visualstudio.com/web/wi.aspx?pcguid=3afc7cea-e643-4785-a32c-e3a73e6f08db&id=14900), [14901](https://capservice.visualstudio.com/web/wi.aspx?pcguid=3afc7cea-e643-4785-a32c-e3a73e6f08db&id=14901)).

**Recovery**

- **2014-03-31 09:00 AM** Osmond switch Search to the backup nodes to mitigate the issue.
- **2014-03-31 09:40 AM** Osmond restarted CR index services on each nodes, PROD and DevInt back to normal.

### What was the "root cause"? ###

Normally, ElasticSearch cluster has one master node. However, in this incident, all the nodes lost internal connection that each of them will automatically elect itself as master, because it believes it's part of a cluster which does not have a master anymore. In an ElasticSearch cluster, it's the responsibility of the master node to allocate the shards equally among the nodes.

With the split-brain problem happened, the cluster no longer has the capability of scaling since each node think it as a single master node. Some old data is on the other node, so that an upcoming query may return incomplete data or experience high latency.


### What did we learn from the event? ###

1.	Need to setup monitoring and provide TSG.
2.	A proper recovery mechanism for those lost connection nodes.

### What are we going to do? ###

1.	Continue investigation on connection lost and already opened a ticket to Azure.
2.	Change default elastic search cluster configuration to prevent “multi-master” issue.
3.	Setup alert with help of Insight team, provide TSG for such kind of incident.
