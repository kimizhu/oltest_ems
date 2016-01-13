---
description: na
keywords: na
title: P0 - Localization: Handoff with dependency not working after S72 deployment
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 5ad8709b-3b13-44b6-8762-0e502c97cd1c
ms.author: yungez@microsoft.com
ms.createdat: 10/28/2014 6:41:43 PM
---
# P0 - Localization: Handoff with dependency not working after S72 deployment
## P0 - Localization: Handoff with dependency not working after S72 Deployment##

### What happened? ###
User is not able to handoff with dependency after Localization S72 deployment. This function was working before but was disabled at S72 deployment. This is caused by

 - Communication gap inside feature team, dev team and PM communication is not timely and effective.
 - Feature team didn’t clearly and timely communicated with partner about the fact that handoff with dependency is turned off caused partner not aware of disabled feature.
 - Testing missed coverage of continuously handoff to detect search service behavior change


### What was the trigger? ###

 - **2014-10-08 8:00 AM** Large code changes checked in after running unit test cases and functional testing except for query-based tests
 - **2014-10-08 3:00 PM** The code changes deployed to DevInt ENV to start integration testing. 2 code bugs was detected. 
 - **2014-10-08 5:00 PM** Checked-in fixing of code bugs, and directly deployed to DevInt ENV. However, during verification, found one article with token dependency could not be handoff. So Qin suspected that something wrong with handoff-with-dependency feature, and starting debugging with focusing on logics of this feature.
 - **2014-10-08 6:00 PM** Spent much time on investigate this issue, but could not find the root cause, although all auto test case of handoff-dependency passed. Decided to disable this feature to unblock PROD deployment. 
 - **2014-10-08 8:00 PM** Stop debugging to prepare Prod deployment, and finally completed deployment. 
 - **2014-10-09 6:57 AM** Found root cause of handoff failure. Search behavior change makes handoff workflow processing stopped by the search result validation warning. 
 - **2014-10-09 10:08 AM** Checked-in bug fixing of Search result handling, and deployed to DevInt ENV.
 - **2014-10-10 2:10 PM** Dev team emailed with PM to notify the configuration change, and ask for impact confirmation. But not got acknowledge from PM side.
 - **2014-10-13  4:50 PM** Dev team decided to enable the configuration, and started preparation. But at this time, still not take this as high priority.
 - **2014-10-15  2:30 AM** Partner emailed that they found handoff with dependency not working, reactived old bug.
 - **2014-10-15 2:27 PM** Dev team communicated with partner on background, and started enable the configuration.
 - **2014-10-15 4:02 PM** The configuration was turned on at Prod, and verified.
 - **2014-10-15 5:51 PM** Dev team communicated with partner to notify the configuration is turned on.

### What was the "root cause"? ###

 - Testing missed coverage of continuously handoff to detect search service behavior change.  Basic scenario coverage missed: continuously handoff after hand back. This test scenario are in test enumeration, but not in regression test suite because Qin didn’t check-in test code after long time after CodeFlow has been signed-off, which could detect the Search behavior changing.
 - Dev team is not clear and aligned with partner's usage priority, take wrong assumption of priority of handoff with dependency scenario.
 - Communication gap inside feature team, Dev team didn’t notify PM about the change  immediately. PM didn’t response. Feature team inside is not aligned on priority of scenarios.
 - Feature team didn’t clearly and timely communicated with partner that handoff with dependency is turned off caused partner not aware of disabled feature.

### What did we learn from the event? ###
 - Check-in any code ASAP once it is get signed-off and ready to checked-in. and Keep tracking of test results instead of take assumption of “they should be there”.
 - For any change made on Prod, feature team need to identify partner impact and priority, and communicate to related teams before make the change.
 - Smoothen Dev team and PM communication channel, to make sure it’s efficient and effective.
 - Communication inside feature team and/or with partners must be timely, and get acknowledge to make sure message is delivered are well received. 

### What are we going to do? ###

 - Check-in any code ASAP it is get signed-off and ready to checked-in.
 - Any regression or loss in end-user facing functionality in a sprint must be explicitly planned, reviewed and approved by the VSC trio
 - Feature team must clearly communicate to affected users in advance about any regression or loss in end-user facing functionality. 
 - Feature team setup a smoothly and effective communication channel inside between dev team and PM.
 
	 a. Dfeine effective communication channel: email/lync meeting syc up. 
	 b. Not take assumption that email is received until got acknowledge.
	Cc. ommunication transparent. Any change at each side will involve the other. 


