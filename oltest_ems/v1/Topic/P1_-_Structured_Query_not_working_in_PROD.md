---
description: na
keywords: na
title: P1 - Structured Query not working in PROD
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 54134655-f91e-4d3a-ad24-915d6fa58f40
ms.author: lvyu@microsoft.com
---
# P1 - Structured Query not working in PROD
##P1 - Structured Query not working in PROD

### What happened?

The impact was while trying to create a new team query, the 'Field' selector will appear to be an empty list, no valid field could be chosen during that period.

The issue has been lasted for about 50 minutes.

### What was the trigger?

 - **2015-01-13 15:26 PM** _From E2E HourlyReport, total pass ratio dropped down to 88% and some handoff related cases were failed [[Test run]](mtms://capservice.visualstudio.com:443/defaultcollection/p:CAPS/Testing/testrun/open?id=34364)_.
 - **2015-01-13 15:50 PM** _Jeff created LSI [[11164]](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems#_a=edit&id=11164)_. 
 - **2015-01-13 16:02 PM** _After some investigation Rome Li found out the issue was caused by newly imported S77 schema, as the newly added schema files caused the get schema API call exceeded max API url length_.
 - **2015-01-13 16:20 PM** _Liam Yu did a fix to roll back the schema files back to S76 version_.
 
### What was the "root cause"?
 * There's a url length limitation of any IIS hosted .NET web applications of 260. [[Refer]](http://forums.iis.net/t/1105360.aspx)
 * In S71 we've already found this issues and [[Task 5117]](https://capservice.visualstudio.com/DefaultCollection/CAPS/CAPS%20API%20Service/_workitems#_a=edit&id=5117&triage=true) has been created as a P1Blocker and been resolved by providing a new 'PostGet' API, but client didn't finish integration. 
 * In S77 the number domain data items reached 32 which causes the get domain data API call exceeds the url limitation, so [[Bug 11042]](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems#_a=edit&id=11042) has been filed for client and has fixed accordingly to use API's 'PostGet' API.
 * API service team have API PROD refreshed this (Jan 13) afternoon with latest bits and schema, with a wrong assumption that client team's also doing continuous deployment, but actually the fix for bug 11042 hasn't been online yet, hence we triggered this issue.
 
### What did we learn from the event?
 * Developer should always try to turn the production to a robust state as early as possible, for this case we should make the integration of PostGet done earlier before problem occurs.
 * Should be more careful while doing deployments, for this case we should double confirm with client team about their production environment state and should postpone the schema deployment.

### What are we going to do?

 * Service will do production deployment more carefully, especially when there might be some breaking changes included.