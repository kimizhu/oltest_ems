---
description: na
keywords: na
title: P0 - Localization service PROD deployment delay
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 16997126-8b1f-41f3-9b96-2ea87c55927d
---
# P0 - Localization service PROD deployment delay
## P0 - Localization service PROD deployment delay ##

### What happened? ###
The **ADS production environment has been unavailable** for 9 hours - 2 hours planned deployment down time, plus **7 hours unplanned deployment down time**.
Localization service could not be deployed on schedule due to 2 code bugs.

 - Code bug: AggregateRequest.QueryUri was incorrectly initialized with default QueryTanent value rather than real value from HandoffRequest
 - Code bug: Enum parse exception throw when parsing TranslationType string without ignoring case.

### What was the trigger? ###

 - **2014-10-08 8:00 AM** Large code changes checked in after running unit test cases and functional testing except for query-based tests
 - **2014-10-08 3:00 PM** Checked-in BuildClient code changes and deployed the Localization service code changes to DevInt ENV to start integration testing. AggregateRequest.QueryUri bug was detected.
 - **2014-10-08 4:00 PM** Fixed AggregateRequest.QueryUri bug and deployed it to DevInt ENV. This time still not perform E2E testing or query-related testing with BuildInternal ENV. After deployed to DevInt ENV again, the second bug, Enum parse exception, was detected. 
 - **2014-10-08 5:00 PM** Checked-in the code changes of second bug fixing, and directly deployed to DevInt EVN. However, during verification, Qin found one article with token dependency could not be handoff. So Qin suspected that something wrong with handoff-with-dependency feature, and starting debugging with focusing on logics of this feature. 
 - **2014-10-08 6:00 PM** Qin spent much time on investigate this issue, but could not find the root cause, although all auto test case of handoff-dependency passed. Catherine determine to disable this feature to unblock PROD deployment.  
 - **2014-10-08 8:00 PM** Stop debugging to prepare Prod deployment, and finally completed deployment. 

### What was the "root cause"? ###

E2E testing and query related functional testing were not fully executed after large code changes checked-in. Two bugs escaped before DevInt ENV deployment.

 - AggregateRequest.QueryUri should be initialized with de-serialized property values, rather than self-defined default-value.
 - TranslationType string enum parsing should be case-insensitive.

Search service behavior change is also not timely handled. Entities of non-default locales will returned if locale is not restricted in query conditions, which leads to Localization service stop processing with unexpected query result.


### What did we learn from the event? ###
1.	Any code change check-in should be followed by fully and efficiently execution of functional testing and E2E testing against proper environment with focusing on changed code paths.
2.	Improve cross-services debugging skills to be able to quickly setup debug environment and extract and analyze trace information.
3.	Split big code changes to small pieces, so that each piece of codes could be verified quickly.
4.  If service quality of latest version is not ready for PROD development, restore to last working version to unblock whole system development. 

### What are we going to do? ###

1.	Make sure test verification/coverage before checkin and during integration

 - Execute query-related test cases using BuildInternal ENV
 - Perform E2E test cases after deploy to Sandbox/DevInt ENV
 - Design more scenario-based functional tests.
 - Categorize and prioritize test cases, to enable selective and purposeful test execution.

2.	Improve test efficiency so that codes could be quickly verified
	- Categorize and prioritize test cases, to enable selective and purposeful test execution
3.	Control code changes in one checkin to make sure it’s quality is controllable.
4.	Improve cross-services debug skills
 - Centralize key HowTo information, e.g. service end-points, required headers, how to setup local debug environment.
 - Setup common troubleshooting list to guide detecting some well-known issues.
 - Utilize debug tools, e.g. Fake client request with Fiddle
 - Develop small tools to facilitate extracting and understanding key trace data.

