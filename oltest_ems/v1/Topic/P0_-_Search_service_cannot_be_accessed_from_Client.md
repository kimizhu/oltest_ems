---
description: na
keywords: na
title: P0 - Search service cannot be accessed from Client
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 3c199cdc-2d17-4031-8f01-2b7f092781bd
ms.author: reyang@microsoft.com
---
# P0 - Search service cannot be accessed from Client
## P0 - Search service cannot be accessed from Client ##

### What happened? ###
The impact was **ALL clients\* not able to search within 18 minutes**, getting HTTP UNAUTHORIZED exception.

* services which call search are not affected, since service call does not use CORS.

### What was the trigger? ###

**Start**

 - **2014-08-15 15:42 PM** Reiley has made a one line change in search service web.config, which breaks CORS (cross-site request). All search test cases passed, and the change got deployed to devint.
 - **2014-08-15 15:43 PM** Rome met the issue and reported via IM. The issue report was not paid enough attention by Reiley, since search service looks all good.
 - **2014-08-15 15:59 PM** Reiley received Rome’s email on the error response, and immediately realized the problem.

**Recovery**

 - **2014-08-15 16:00 PM** Deployment was reverted in 1 minute. Things back to normal. Total interruption time is 18 minutes.

**Fix and Deployment**

 - **2014-08-15 16:22 PM** Reiley finished code fix, tested locally, sent out code review.
 - **2014-08-15 16:25 PM** The fix got sign off from Dooriya.
 - **2014-08-15 16:26 PM** Fix has been deployed to devint in 5 seconds.

**Verification**

 - **2014-08-15 16:26 PM** The deployment has been verified.


### What was the "root cause"? ###

Client calls search using CORS through the following path:

    CAPS Client -> Search Library -> Search Service

The change made on 15:42PM breaks the part from Search Library to Search Service (CORS), and the issue was not captured by search service’s test cases, since the test cases use a simplified call path (no CORS):

    Search Test -> Search Service

### What did we learn from the event? ###
 1. Each part is working, however the joint part is not covered.
 2. Still there are a lot work for monitoring and alerting.

### What are we going to do? ###

 1. Onboard routing, A-B testing and progressive routing.
 2. Improve testing methodology.
