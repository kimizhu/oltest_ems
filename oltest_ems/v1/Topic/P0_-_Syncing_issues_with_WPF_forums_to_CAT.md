---
description: na
keywords: na
title: P0 - Syncing issues with WPF forums to CAT
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: a9b45469-c836-468e-a7d8-e944ff98f50c
ms.author: xinyi.zhang@microsoft.com
---
# P0 - Syncing issues with WPF forums to CAT
## P0 Syncing issues with WPF forums to CAT
### What happened? ### 

CAT team is **unable to get MSDN forum data via REST API for some threads** on Production starting from *04/05/2015 20:44 PST*.

This issue lasts for *7* days in total. The time between the issue raised by partner team and issue fixed in production was *6* days.   

### What was the trigger? ### 

- **4/5/2015 22:44 PST** CAT team reported they cannot sync the data back from MSDN forum for some threads to Ops team
- **4/5/2015 23:44 PST**  Ops team was investigating and advised users to check forum via website directly, instead of relying on CAT tool
- **4/6/2015 11:47 PST** Ops team escalated this issue to dev team under bug [255072](https://mseng.visualstudio.com/web/wi.aspx?pcguid=0efb4611-d565-4cd1-9a64-7d6cb6d7d5f0&id=255072). AppX dev from Redmond side  was involved in the investigation
- **4/6/2015 17:33 PST** CAT team provided event logs from CAT server and VS China team is involved in the session for investigation
- **4/7/2015 23:30 PST** The fix was checked in and Ops team deployed the bits to PPE and Prod. However, the deployed bits are not the correct one. CAT Team met with the same issue continuously
- **4/9/2015 21:46 PST** Ops team begin another deployment of the right bits, and met with Azure cloud service hung issue. Hand over to China team  
- **4/10/2015 4:06 PST** Azure cloud service hung continues, Ops team created a ticket to Azure.
- **4/11/2015 15:04 PST** Azure cloud service was back to normal. 4 instances of forum API service had been deployed successfully by Ops team. Ops team was also working with Azure team and found cloud service hung issue is a bug from azure side. They will deploy the fix in the end of April.
- **4/12/2015 23:00 PST** VS China team started a bridge with CAT team to verify the fix. The issue was gone with hotfix

### What was the "root cause"? ### 

There is a parameter in the REST API "IsDebugMode" and somehow we got null reference for this value. Currently we don't know what is request URL as there is no log for it, so we don't know why null reference is passed in. But we can see call stack from our side and the fix is to ignore the null reference as IsDebugMode should only be used for debug purpose.
After this livesite, we also found CAT team is not using ForumAPI with correct way. By default, the query will go to MSDN foru. So if the query does not specify the brand but want to retrieve something from technet, it will fail. CAT team is aware of this and working on the fix from their side.

### What did we learn from the event? ### 

- We need to setup more powerful tracing system for forum, gallery, etc. Currently we can only get basic information but all request URLs are missing.
- Setup a monitoring system to help us find the issue ASAP.
- Need to communicate more between Ops and engineering team. As two teams are working in the different time zone, it will be a challenge.


### What are we going to do? ### 
- In S82, we begin investigating how to integrate forum with CAPS tracing system
- In S82, we are working on defining forum tracing scenarios
- In future sprints, we will do the same thing for gallery and search.
- After traces are in place, we will take a look how to setup monitoring system.
