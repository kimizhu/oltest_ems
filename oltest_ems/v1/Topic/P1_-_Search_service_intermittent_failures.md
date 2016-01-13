---
description: na
keywords: na
title: P1 - Search service intermittent failures
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 500bb8e5-2034-4d1b-8fc7-5fde17b690bc
ms.author: reyang@microsoft.com
---
# P1 - Search service intermittent failures
## P1 - Search service intermittent failures ##

### What happened? ###
The impact was **search service in DevInt is becoming slow, having intermittent HTTP 500 failures for nearly 40 minutes**.

### What was the trigger? ###

**Before Start**

 - To address the intermittent timeout introduced JVM garbage collection, Reiley has tuned the configuration of JVM and Windows. Operating system page file is disabled, JVM heap is locked in physical memory, and Elastic search has been configured to commit 12GB RAM (on a 16GB VM). Kernel **Paging Executive** flag was turned off from Registry.

**Start**

 - **2014-08-28 13:56 PM** Automatic Windows update triggered, **Windows Update Service** is trying to install security patches.
 - **2014-08-28 14:13 PM** Received email alert from queue monitor.
 - **2014-08-28 14:31 PM** Resource-Exhaustion-Detector in windows event log. The windows update installer crashed, the whole system is running short of memory, which triggered the **CritSecOutOfMemoryEvent** and broadcase to all processes.
 
**Recovery**

 - **2014-08-28 14:37 PM** Search service switched from West US data center to East US data center by Dooriya LI, service fully recovered.
 
**Fix and Deployment**

 - **2014-08-28 14:42 PM** Recieved after-fact email report from Junyi YI.
 - **2014-08-28 14:50 PM** CritSecOutOfMemoryEvent triggered again due to remote session creation, which crashed the system, OS rebooted.
 - **2014-08-28 14:51 PM** Reiley YANG has applied the configuration update and restarted the ElasticSearch instance **search-int-wus.cloudapp.net**.
 - **2014-08-28 14:51 PM** Recieved service recovery email notification.

**Verification**

 - **2014-08-28 14:53 PM** Receieved confirmation from Client, E2E testing and Monitoring service that everything is working as expected.

### What was the "root cause"? ###

Configuration leaves limited resource to the operating system, which failed to handle corner case (e.g. when there is an update installer running).

### What did we learn from the event? ###
 
 1. Configuration is critical, sometimes it could hurts the whole system.
 2. Never be too confident.

### What are we going to do? ###

 1. For performance tuning, the configuration value needs to be well tested before rolling out.
 2. Revise the Elastic Search performance tuning and share with Content Repository team.

