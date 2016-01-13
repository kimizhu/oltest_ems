---
description: na
keywords: na
title: P0 – API Service can’t publish outline on PROD after S75 deployment
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 3e9c0474-7e1b-4644-b6a3-bacc7b52a4ff
ms.author: arthma@microsoft.com
---
# P0 – API Service can’t publish outline on PROD after S75 deployment
## P0 API Service can't publish outline on PROD after S75 deployment##

### What happened? ###

User couldn't publish outline on PROD for around 40 minutes, all outline property retrieving APIs didn't return any build/publish related properties.

### What was the trigger? ###

 - **2014-12-08 15:02 PM** From E2E HourlyReport, total pass ratio dropped down to 60% and all publish related cases were failed.
 - **2014-12-08 15:04 PM** Jun Han filed 2 bug to client team. 
 - **2014-12-08 15:34 PM** After some investigation Rome Li found out one bug may caused by API Service because the API Service didn't return any publish related properties for outline.
 - **2014-12-08 15:44 PM** Liam Yu got the root cause which was caused by a mismatch between API Service bits version and the schema version, he did a quick fix that update all schema (entity definitions) on PROD and then PROD was back to normal immediately. 
 
### What was the "root cause"? ###

 - In S75 we found a ambiguous definition of 'persistence_type' in outline. Previously 'persistence_type' was set to 'virtual' for any property which it doesn't be saved in CR, but for the TOC feature most of the properties on outline are not only 'virtual' but also need redirect to it's underline topic, so for these properties it's not accurate if still set its 'persistence_type' to 'virtual'. 
 - We found this problem in S75 then changed its value to 'redirect' and did some corresponding changes on API Service, this was not a backward compatible change and need deploy schema first then deploy API Service bits.
 - But when in PROD deployment, we didn't follow this sequence that the API Service bits was deployed first and forgot to update schema.
 
### What did we learn from the event? ###
 - Like the table schema for SQL, all entity definition and domain data in CAPS are very important and any change may affect multiply services / components and business scenarios, so we need to take more care about any changes on entity definition or domain data. Here our long term goal shall support continuous schema upgrading, that mean there shall avoid any backward incompatible changes. 
 - Manually step is always a ticking time bomb.

### What are we going to do? ###

- In S76, I will work with Xiaokai to coordinate all component teams and find out a comprehensive process which is simple but can avoid any break changes for schema.
 - a. Separate all entity definitions into several domains base on business requirement, this has been done in S75 but need for further refinement.
 - b. Simplify schema changes, nested logical structure like windows registry key is one of the candidates.
 - c. Gated check-in for schema changes: first find out any backward incompatible changes, second run some test cases before commit changes to git repository.
 - d. Continuous schema deployment, roll-back mechanism for any accidents, notification mechanism for schema deployment. 