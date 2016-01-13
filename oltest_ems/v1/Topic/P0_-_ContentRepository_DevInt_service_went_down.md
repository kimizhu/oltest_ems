---
description: na
keywords: na
title: P0 - ContentRepository DevInt service went down
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 68af5010-e59a-4d40-9c83-f3aad9ebbe04
ms.author: jiache@microsoft.com
---
# P0 - ContentRepository DevInt service went down
## P0 - ContentRepositroy DevInt service went down ##

### What happened? ###
The impact was **DevInt down for 40 minutes**, all calls to ContentRepository service return error code 50001 timeout.

### What was the trigger? ###

**Start**
(Shanghai time)

- **2014-10-24 10:20 AM** From chip report, service got timeout error from CR.
- **2014-10-24 10:30 AM** Service team received API heartbeat failure.
- **2014-10-24 10:42 AM** CR was notified and start to investigate. API trace, IIS logs were checked to figure out detail reason.
- **2014-10-24 10:45 AM** Kain found IIS threads count are abnormally high (over 1000 threads) on all six DevInt instances. After that, IIS connection status report showed 192 connections points to CR backend index service.
- **2014-10-24 10:50 AM** IIS status reports was recorded and memory dump was captured.

**Recovery**

- **2014-10-24 11:01 AM** CR reboot all instance to fix large amount of threads in IIS. After that, timeout issue gone. API calls went normal.
- **2014-10-24 11:10 AM** CR index service VMs are rebooted in order to rule out any potential issue.

### What was the "root cause"? ###

CR needs to build index on normal CRU operations. These operations are running as background tasks in web roles. In a cornered time-out case, connection from w3wp to index service can't be released in time, then huge amount of threads are accumulated in w3wp process waiting to establish connection to index service, then no threads can be allocated to handle normal web requests, time-out then happened.


### What did we learn from the event? ###

1.	Major business components and background tasks should be isolated.
2.	Any background tasks that established TCP connections should also be responsible to release it instead of relying on default behavior. Same to other machine resource, thus any crucial resource leaking won't happen.

### What are we going to do? ###

1.	Move out the real work from background tasks to worker role and leave a very light-weight message dispatch in API. CR has a task in s74 to first move Index build, unique check and storage replication. Others will be followed after s74.
2.	Refactor index task code to release connections when meeting timeout or index server error, this will be done in s74.
