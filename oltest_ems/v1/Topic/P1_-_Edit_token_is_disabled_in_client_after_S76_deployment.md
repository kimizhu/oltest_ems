---
description: na
keywords: na
title: P1 - Edit token is disabled in client after S76 deployment
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 5f7f7472-e5cf-461e-88f1-2910e5a95a8a
ms.author: qisun@microsoft.com
ms.createdat: 12/30/2014 2:43:35 AM
---
# P1 - Edit token is disabled in client after S76 deployment
## P1 Edit token is disabled in client after s76 deployment##
### What happened? ###

User can not edit token in client. Edit button is disabled after s76 deployment.

### What was the trigger? ###

- **2014-12-29 14:02 PM** From Experience Team's E2E report, Devint caes pass rate was 88.89%, case [EditNewTokenE2E] failed.
- **2014-12-29 14:10 PM** Issue 10814 was created to track the problem.
- **2014-12-29 14:20 PM** Qing found that the token edit was determined by both the entity state and authoring config.
- **2014-12-29 14:30 PM** A new commit pushed to client repository hotfix branch to fix the problem. After hotfix e2e and master e2e passed and the new deployment, the button is back to normal.

### What was the "root cause"? ###

- Article and token are two kinds of editable entities. Currently, authoring ddue kind of topic is disabled in production environment, but not token or other kinds of articles. Whether edit button enabled or not is controlled by the authoring config, entity state and entity type. Token can be edited in all environments, and the edit button should only be controlled by the entity state.

### What did we learn from the event? ###
- Any change to the config should be tested in all kinds of entites. After separating the config for different kinds of articles, article work fine in all environemnt. But token's test is missed.
- Scenarios of differences between environments should be covered.

### What are we going to do? ###
- A separate environment to simulate prod environment
- Test cases to cover the scenarios