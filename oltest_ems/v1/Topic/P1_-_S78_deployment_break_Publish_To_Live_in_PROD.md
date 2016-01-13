---
description: na
keywords: na
title: P1 - S78 deployment break Publish To Live in PROD
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 604adf0e-2804-435e-bd71-f92015478c94
ms.author: reyang@microsoft.com
ms.createdat: 1/19/2015 9:28:01 AM
---
# P1 - S78 deployment break Publish To Live in PROD
##P1 - S78 deployment break Publish To Live in PROD##

### What happened? ###
Failed to promote a outline node to live on PROD.

### What was the trigger? ###
* **2014-01-19 3:35 PM** Client deployed the bits for S77 on PROD.
* **2014-01-19 5:03 PM** PROD E2E reported that **PublishNewOutlineE2E** case failed.

### What was the "root cause"? ###
[11359](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/11359) was not created under S77 path, instead, it's under S78 path. When Client team prepared the bits, there was no any active bug under S77 path, that's why Client team decided to move foward to kick off PROD deployment.

### What did we learn from the event? ###
1. We need to improve the E2E coverage for publish scenario on Client side, and if possible, do some static checking.
2. We need to improve the monitoring of DEVINT E2E on EI side, since the problemtic commit was pushed on 1/15, but the issue was captured by EI team on 1/17(Saturday) afternoon.
3. We need to create bug in correct path with correct priority, so that feature team can decide where to get it fixed.

### What are we going to do? ###
1. [11492](https://capservice.visualstudio.com/web/wi.aspx?pcguid=3afc7cea-e643-4785-a32c-e3a73e6f08db&id=11492) is created for Client team to improve the scenario coverage.
2. EI team needs to improve the process of E2E monitoring and bug creation.
3. Need to define clearly what's the exit criteria for sprint.
