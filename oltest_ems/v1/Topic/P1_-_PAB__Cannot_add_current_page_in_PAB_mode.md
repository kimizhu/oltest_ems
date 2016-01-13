---
description: na
keywords: na
title: P1 - PAB: Cannot add current page in PAB mode
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: d9e80cba-70f0-4c4b-92a7-a4401f96e249
ms.author: alex.qiu@microsoft.com
---
# P1 - PAB: Cannot add current page in PAB mode
## P1 - PAB: Cannot add current page in PAB mode
### What happened? ### 

In S91, a new instrumentation feature was added to the WEDCS.js script in order to add tracking to user clicks at a specific control. After deployment it was detected that one of the JS function in the change has conflicting logic with an existing logic for content exporting logic in rendering code base, which will result in the functionality of PAB(Publish a book) broken.   

### What was the trigger? ### 

MSDN library visitors will have issues (as described in bugs above) if they use the Export functionality to export the content to PDF or Html. Per web log, in last 14 days there were in total 799 MSDN library visits to the export functionality. There is no user complain about being blocked by now, although if there is it will become an live site. We decided to fix this soon considering the low cost and risk.

### What was the "root cause"? ### 

In the javascript change in WEDCS.js for this new instrumentation, Array.prototype was changed, which created a conflicting logic to exporting functionality which is existing in rendering code base.   

### What did we learn from the event? ### 

- We should avoid doing any prototype change in javascript, which will cause wide impact.
- We were not doing enough code review when making WEDCS.js change in frontend. Need to do more cautious code review from both Inisght and frontend team. 
- Test validation could be improved as well. In addition to validating the added feature is working, if the code change has common changes with wider impact, need to work with frontend team to identify impacted areas and do validation as well. (Of course avoiding common change as possible is still the first choice.) 


### What are we going to do? ### 
- Remove the prototype change from WEDCS.js, update the file by an OOB deployment.