---
description: na
keywords: na
title: P0 - S84 code deployment did not right permission for one SP
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 6bddd426-ada1-4b04-bf57-cbfb495c08a8
ms.author: arthma@microsoft.com
ms.createdat: 6/23/2015 8:42:09 AM
---
# P0 - S84 code deployment did not right permission for one SP
## P0 - S84 code deployment did not right permission for one SP
### What happened? ### 

Customer saw requested page cannot be found when user navigate to MSDN Magazine site after S84 MTPS deployment.

This issue lasts for *3* hours in total. The time between the issue raised by partner team and issue fixed in production was within *90* minutes.   

### What was the trigger? ### 

- **6/16/2015 10:37 PST** Ops Team finished S84 MTPS production SQL deployment.
- **6/16/2015 11:01 PST** Ops team got a LSI that customer saw requested page cannot be found when user navigate to MSDN Magazine site. 
- **6/16/2015 12:24 PST** After some investigations Ops and Engineer team found it's about the permission issue with a SP usp\_EyeBrowsSelect which was updated in the S84 MTPS deployment. To mitigate the issue, Ops manually granted the permission to usp\_EyeBrowsSelect SP in all FE SQL’s and Publisher, problem resolved. 
- **6/17/2015 08:29 PST** Engineer team found the root cause and verified it on production with the helps from Ops team.

### What was the "root cause"? ### 

We modified the SP usp\_EyeBrowsSelect for the new Archive feature in S84, and the deployment is only on the publisher database and the deployment of this sp to the FE database is via the SQL Replication publication. But due to the snapshot refresh this sp is remove then create a new one on all FE databases, then all permissions on this sp were missing. Previously for this scenario there has a post step to set back all permissions, but due to lack of the knowledge after transition we didn't know this.


### What did we learn from the event? ### 

- We don't have a production like environment to verify the deployment before it goes to production now, currently even the PPE is not such kind of environment.
- Fix all test cases which current are all broken, enable these test cases and add more cases will give us more confidence and ensure the quality of our service.
- Enhance our monitoring system to find out issues more quickly even on our internal environments.


### What are we going to do? ### 
- Document the post step in our dev guide.
- In S85, we will work with Ops team to build up the new devint environment which will keep sync with production environment.
- In future sprints, we will fix all test cases.
- Add more traces and work with Insight term to setup a more efficient monitoring system.
