---
description: na
keywords: na
title: P1 – S93 Rollback Task 461724
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 4d1d94a2-29d5-4858-a37c-0e1d44e5573b
ms.author: arthma@microsoft.com
ms.createdat: 12/23/2015 2:18:44 PM
---
# P1 – S93 Rollback Task 461724
## P1 – S93 Rollback task 461724 ## 

### What happened? ###

We wanted to deploy new feature (task 461724) in S93 to enable updating index / follow flag from SMS side, but our partner raised the data inconsistency problem between SMS and MTPS up and then called back this feature at the last minute. We canceled the SMS updating and continued the MTPS part because I considered backend change would not affect the whole logic of index / follow from SMS that time. After some discussion and deep digging we found that this still caused the data change of index / follow flag.

### What was the trigger? ###
    
•	**11/16/2015 PST**: Got requirement from partner to enable modify index / follow from SMS and PT! side, task 461724 was created and put on S93.

•	**11/17/2015 PST**: Confirmed from partner that only enable SMS.

•	**12/10/2015 PST**: Partner called back this change on SMS side because the problem of data inconsistency is bigger than expected.. 

•	**12/13/2015 PST**: Partner asked if the MTPS part should be canceled too, after some checked we didn’t find any issue so decided to continue the deployment. 

•	**12/15/2015 PST**: OPs Deployed the change to PPE & PROD.

•	**12/16/2015 PST**: In the discussion with partner, Jeffery and Kurt found the defect in one SP which updates the index / follow, it will cause incorrect setting for index / follow from SMS so we decided to rollback it.

•	**12/17/2015 PST**: OPs helped to rollback this change for task 461724 on PPE & PROD.

### What was the "root cause"? ###

The metadata of index / follow in MTPS DB is Boolean data type but have 3 states: true, false and NULL. One index / follow update logic in the MTPS SP is:
1.	If content is published from SMS and PT! then nothing changes on index / follow.
2.	If content isn’t published from SMS and PT! then convert NULL value to another value, maybe true or false, and save to db.

We didn’t aware this and expected SMS should always send true or false to MTPS even without the corresponding changes in SMS. 
But after SMS reverted the change we found the value of index / follow from SMS is NULL, so when we released the restriction the index / follow for SMS content was set to an incorrect value.


### What did we learn from the event? ###

•	We should not take assumptions about what the data coming from any legacy tools, and should do more E2E testing to dig out the right value.
•	We already raised the data inconsistency issue with our partner but didn’t do well communication and let them truly realized the issue.


### What are we going to do? ###

•	More communications before rush a feature.
•	Add more test cases to handle special cases.
•	Take this as a sample in developer’s best practice to avoid the similar mistake in the future.


