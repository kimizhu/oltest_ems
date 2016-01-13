---
description: na
keywords: na
title: P0 - S78 Service deployment break Structured Query in PROD
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: e687a820-f8e7-4a77-a894-5a17c3521738
---
# P0 - S78 Service deployment break Structured Query in PROD
##P0 - S78 Service deployment break Structured Query in PROD

### What happened?

The impact was while trying to create a new team query, the 'Field' selector will appear to be an empty list, no valid field could be chosen during that period.

The issue has been lasted for about 60 minutes.

### What was the trigger?

 - **2015-01-19 15:13 PM** _From E2E HourlyReport, total pass ratio dropped down to 88.89% and some publish related cases were failed [[Test run]](mtms://capservice.visualstudio.com:443/defaultcollection/p:CAPS/Testing/testrun/open?id=35778)_.
 - **2015-01-19 16:?? PM** _We didn't provided any fix on this issue, it has been resolve after client finishes S77 PROD deployment_.
 
### What was the "root cause"?
 * The root cause is as same as [[P1 - Structured Query not working in PROD]](https://capsweb-devint-wus.azurewebsites.net/#/organizations/950e523a33b54538824f57577374d39e/projects/b52b2ce1-048f-441f-92b9-78abb8bf42f5/containers/378d2d59-f457-46c6-8ed3-f72afeececdf/articles/54134655-f91e-4d3a-ad24-915d6fa58f40/locales/en-US) 
 * During the PROD deployment today, client have dependencies on API service like some permission and DocSet features, so we decided to deploy API service first, just after CR has finished their deployment.
 * So the issue is as expected and the deployment was one of the choices that causes a minimal break.
 
### What did we learn from the event?
 * For the previous LSI, we should have provide a hot fix on the production from the client side than API service to rollback changes, then we could be able to resolve the dependencies today.
 * We should have allocate some downtime for the S77 deployment. We're not able to keep every deployment finish with a zero downtime, especially when we didn't have a full control over our infrastructures.

### What are we going to do?

 * Try introduce ways to switch between staging & prod slots on multiple components together.
 * Rollback or roll forward is depend on what the issue is, not always rollback or roll forward.
 * Reconsider our release deployment plan, as we can not always avoid introducing breaking change between components in all the sprints.