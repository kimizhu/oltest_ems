---
description: na
keywords: na
title: P1 - ContentRepository notification stopped working
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 1c978f8c-f134-4407-a808-8cc67dabc10f
---
# P1 - ContentRepository notification stopped working
P1 - ContentRepository notification stopped working
===

What happened?
---

Content Repository notification service in Sandbox, DevInt and Prod was down for **2 hours 20 mins**. During that period of time, some scenarios depended on CR notification service were impacted, including:

* Search was unable to index entities created/updated.
* Localization was unable to get newly created entities.
* Build Preview was unable to be auto-triggered on creating/updating entities.
* Build Preview/Publish would get wrong result if any referenced entities were updated.

What was the trigger?
---

**Start (Shanghai time)**

  * **2014-12-12 12:30 AM** The PublishNewOutlineE2E test case on Prod started to fail.
  * **2014-12-12 01:35 PM** Jeff Chen (JI) reported the error.
  * **2014-12-12 01:53 PM** Dooriya Li reported service bus was not stable and Search could get any message from notification queue.
  * **2014-12-12 01:56 PM** Su Shi reported DevInt had the same issue and Client's E2E cases failed.
  * **2014-12-12 01:58 PM** Dflying Chen found Azure Service Bus was in unhealthy state and an error notification was given:
  

      *From 12 DEC, 2014 04:00 to 06:20 UTC a subset of customers using Service Bus in West US might have experienced intermittent failures - accessing Service Bus Queues, Topics and RelayEndpoints.*



**Recovery**

  * **2014-12-12 02:20 PM** Azure status page showed the issue had been mitigated.
  * **2014-12-12 02:42 PM** Reiley Yang verified and reported the issue had been mitigated. All services were back to normal.
  * **2014-12-12 02:45 PM** Jiahua Chen reported the messages inserted during the incident time were lost so an index rebuild was need on Search sides.
  * **2014-12-15 10:30 AM** Jiahua Chen sync up with Search team and start rebuilding index.

What was the "root cause"?
---

When entities are created/updated/deleted in Content Repository, CR will insert a message into Azure Service Bus. Then CR notification worker will dequeue the messages and route them to Azure Storage Queues based on subscribers' info. Thus other CAPS components like Build and Search will receive the message and be triggered to do further jobs.

Since Azure Service Bus was down from 2014-12-12 12:00 AM to 2014-12-12 02:20 PM (Shanghai time), Content Repository was unable to push messages into Service Bus and other CAPS component did not receive any message. So scenarios depending on notifications were broken.

What did we learn from the event?
---

 1. Backup / failover options should be provided for critical components.

What are we going to do?
---

 1. (S77) Test and monitor existing Service Bus stability, and investigate on a failover option for CR notification service.
 2. (S76) Find a way to do incremental index rebuild to reduce recovery time.
