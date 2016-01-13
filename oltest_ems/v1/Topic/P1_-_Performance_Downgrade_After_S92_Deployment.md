---
description: na
keywords: na
title: P1 - Performance Downgrade After S92 Deployment
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 43fcd9f4-569f-49b2-80a4-8fc51a4dc809
ms.author: roml@microsoft.com
ms.createdat: 12/15/2015 7:50:34 AM
---
# P1 - Performance Downgrade After S92 Deployment
### What Happened?
Timeline (Shjanghai Time):
- [Monday, November 30, 2015 4:00 PM] Issue of E2E test not being able to create project reported by Shuang Jiang after deployment
- [Monday, November 30, 2015 5:27 PM] Andy engaged and started investigation
- [Monday, November 30, 2015 21:37] Zhen engaged with performance data which showed significant performance downgrade
- [Tuesday, December 1, 2015 2:38 PM] API team found the perf data had huge difference between API Service and CR. Network latency was suspected and ticket was opened for Azure team to investigate
- [Monday, December 7, 2015 5:26 PM] Andy locked the problem down to TraceEx. After downgrading the TraceEx lib to earlier version, both DEVINT and PROD were back to normal
- [Thursday, December 10, 2015 11:47 PM] Chaoyi found the a code bug in TraceEx and made a fix

### What was the trigger?
TraceEx was updated during S92. Later API Service upgraded to reference the new version of TraceEx. The performance downgrade actually started around 11/20 but nobody noticed until the production deployment on 11/30.

### What was the root cause?
- The root cause was the introducing of Sampling feature of TraceEx
- The actual code bug was in the logic to flush the trace buffer every 100 ms. This change list was submitted even earlier
- The 100 ms timer was never triggered because it was defined as a static member and nobody referenced it (.net lazy load). So we don't have this issue immediately after the submission
- When introducing the Sampling feature, a new static member was added and referenced. This resulted in the load of all the static members and enabled the timer and the flushing logic
- The flush of trace data took more than 100 ms to finish and all TraceEx calls were blocked during the flushing

### What did we learn from the incident?
- We should have found out the issue by monitoring the performance of API Service, not just from E2E run time
- We should have unit test and perf test for fundamental libraries like TraceEx

### What are we going to do?
- API team will work with Alex' team to setup perf monitoring for each API **[Xiaoyan] what is the bug id to track this?** [Rome] Task 23641 assigned to Chaoyi.
- Alert will be sent when performance of single API drops out of tolerance range **[Xiaoyan] bug id?** [Rome] Task 23642 assigned to Chaoyi.
- The current owner of TraceEx (Chaoyi) will set up unit test and perf test for TraceEx library **[Xiaoyan] bug id?** [Rome] Task 23643 assigned to Chaoyi.