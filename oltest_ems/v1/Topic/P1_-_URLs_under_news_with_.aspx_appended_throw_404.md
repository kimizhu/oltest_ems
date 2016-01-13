---
description: na
keywords: na
title: P1 - URLs under news with .aspx appended throw 404
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: e0e06762-f604-49bd-9cc7-6af8bc398efb
ms.author: jiache@microsoft.com
---
# P1 - URLs under news with .aspx appended throw 404
## P1 - URLs under news with .aspx extension throw 404 ##

### What happened? ###
VisualStudio.com URLs under /news with .aspx extension gives 404 “We're sorry, the page you requested cannot be found.” error.

### What was the trigger? ###

- **5/28/2015 13:36 PST** A bug 330429 was created by Khairun indicating 404 errors through search engine.
- **5/29/2015 15:48 PST** OPS team reported through email that there were in total around 15,000 HTTP 404 errors spread out 15 vs.com/news links. 
- **5/29/2015 23:01 PST** The engineer team found out the iroot for vs.com/news is declared to be OA content but actually all of them are still MTPS pages.
- **6/1/2015 00:46 PST** The continued investigation nailed down the root cause (See What was the "root cause") and checked in the fix.
- **6/2/2015 16:00 PST** The bug was brought up in shiproom and triaged to go with OOB.
- **6/3/2015 20:00 PST** OOB bits deployed to PROD and signed-off by engineering team. The issue was gone. 

### What was the "root cause"? ###

1.	In sprint 84, there was a Bug 263942: [OA]Sign in breaks o365 page rendering, the root cause of this bug is the center relative path of OA URL has folder structure while non-OA URL has not. The origin rendering logic only handles the non-OA case, i.e. returns the last part of the center relative path thus breaks sign in URLs.
2.	Engineer fixed this issue by adding code logic in URL converter to reset the center relative path to include the folder structure.
3.	This fix was designed to apply to only OA pages. It was verified in local test as OA page has no extension like .aspx.
4.	The iroot definition of vs.com/news sets isOpenAuth = true. However, the pages are actually non-OA. By confirming, it is because partners are migrating from non-OA to OA, so there are hybrid pages under this center.
5.	This was causing vs.com/news non-OA pages running into unexpected code flow. The URLs were not converted by URLConvert for non-OA pages so extension is not removed.
6.	When calling to MTPS server with .aspx extension, MTPS couldn't find the page and returns null, 404 error happened. 

### What did we learn from the event? ###

- 	Be extremely careful if the code change is touching the core. In this case, it's URL converter. Make sure the impacts are well evaluated and test coverage is enough if it has to.
- 	Test coverage and page samples are important. We do have test cases to cover canonical URL, but only a few of sample pages are included, vs.com/news somehow does not have any.
- 	Sprint bugs and live site incidents should be on different channels. Web platform is a gate visible to all partners. Even the newly opened bugs team needs to triage every day could be more than 10. Without a separate channel and process, it will be very difficult for the team to make the right judgement call on its priority.  

### What are we going to do? ###

- 	Improve testing coverage. There should be at least 1 or 2 pages picked up as sample pages from each iroot and regress the major scenarios sprint by sprint. [Owner: Richard]

- 	We already have LSI escalation process defined but is not strictly followed. We need to improve communication and education to make sure tracking and reporting issues will be going through the right process [Owner: Lang]
