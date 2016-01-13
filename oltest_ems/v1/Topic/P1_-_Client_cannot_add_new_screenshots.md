---
description: na
keywords: na
title: P1 - Client cannot add new screenshots
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 6c62d763-ec6b-4bc2-a90a-ef54de675ed2
ms.author: yufeih@microsoft.com
ms.createdat: 3/23/2015 2:31:59 AM
---
# P1 - Client cannot add new screenshots
## P1 Client cannot add new screenshots
### What happened? ### 

CAPS users are **unable to create new screenshots** on Production starting from *3/19/2015 2:09PM*. They are still able to create icons and conceptual images, existing screenshots can also be edited.

This issue lasts for *20* hours in total. The time between the issue raised by partner team and issue fixed in production was *2.5* hours.   

### What was the trigger? ### 

- **3/19/2015 2:09 PM** The new image workflow is picked by client daily build and deployed to production.
- **3/20/2015 9:53 AM** CSI team reported a [bug](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems#_a=edit&id=14324) indicating that they cannot create a new images. 
- **3/20/2015 10:06 AM** E&I team engaged and cannot find a reproduce. 
- **3/20/2015 10:58 AM** Team reproduced the bug and narrowed it down to be specific to *Screenshots*.
- **3/20/2015 11:25 AM** Dev team engaged and found the root cause.
- **3/20/2015 12:24 PM** The fix was live on producation and the issue was resolved.  

### What was the "root cause"? ### 

On S80 there's a [user story](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems#_a=edit&id=12988) to simplify the workflow for adding images and edit images. The new implementation was pushed to DEVINT environment after passing all existing Client E2E cases. The new workflow was demostrated at scrum meeting and gained good feedbacks, after the changes passed all Client backward compatibility E2E cases, it was deployed to production.  

However, a legacy code that *validates the repro step* field of screenshots triggered an undefined reference error when creating new screenshot, causing the browser failed to continue. This issue should be avoided if our image E2E case covers screenshot part in main workflow of image creation, currently, we only cover conceptual part since we thought it's the most important one.

### What did we learn from the event? ### 

- The existing Client E2E cases need to align with the P0/P1 scenarios defined by PM for each feature, which can help us to make sure the correct rolling out criteria for each feature.
- Extra care must be taken when migrating existing codebase.
- For complete new/redesigned features that will change the legacy behavior, we need to either have feature flag(DocSet/ListView) prepared or slow down the deployment frequency.
- For PROD deployment, we need to think about doing deployment per tenant based.


### What are we going to do? ### 
- Short term:
	- During the planning phase of each sprint, we will decide how fast we will do the deployment on PROD, whether we need to have feature flags for complete redesigned features, and engineering team should work with PM to define the main workflows that will be converred in Client E2E cases.
	- Image E2E cases will be enhanced to include more dimensions by [Task 14365](https://capservice.visualstudio.com/DefaultCollection/CAPS/CAPS%20Client/_workitems#id=14365&triage=true&_a=edit).
	- Work with Jingmin/Reiley/Ted for better prioritizing production bugs.
- Long term:
	- Move to **per tenant pre-flight feature** routing strategy rather than a complete flip over for all users under all tenants. This helps us expose new features in a gradual, adoptive and progressive way.
