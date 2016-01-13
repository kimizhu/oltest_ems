---
description: na
keywords: na
title: P0 - Client S76 deployment failure analysis
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: a836eddc-3457-48a7-888a-999b76931411
ms.author: sushi@microsoft.com
ms.createdat: 1/5/2015 2:56:01 AM
---
# P0 - Client S76 deployment failure analysis
## P0 Client S76 deployment failure analysis##
### What happened? ###
- PROD site was down due to run-time error.

### What was the trigger? ###

- **2014-12-29 11:53 AM** Jenkins CI failed at the first time.
- **2014-12-29 12:02 PM** Jenkins CI failed at the second time.
- **2014-12-29 12:43 PM** Prod Test Azure 20141229.28: 0%.
- **2014-12-29 12:47 PM** Jeff reported the PROD site was down.
- **2014-12-29 12:48 PM** Client team cleaned the workspace on Jenkins and re-triggered the build.
- **2014-12-29 12:55 PM** Jenkins CI successed.
- **2014-12-29  1:03 PM** Jeff reported that PROD was back.


### What was the "root cause"? ###
Client’s Build Job is consist of: **Clean, 3rd Party Package Restore, Compile, LINT, Package, UT, Deploy**. In general, the **Grunt** task system will keep running the task in predefined list sequentially until one of them fails. But on 12/29/2014, failed **UT** task didn’t block the execution of **Deploy** task due to incompatible legacy 3rd party packages made the **Grunt** system in a mess, that’s why we see Client Build Job failed but deployment was triggered and successfully on Azure Website Portal.

As we can see the [Client PROD deployment process](http://capsdoc.azurewebsites.net/client/prod/deploy.html), it will only merge the code from **release** to **master** when Client code base completely works with PROD/DEVINT endpoints. Since the “sign off” is on source code level instead of deployment bits level, so if there’s any difference between environments of **release** and  **master**, there will be potential risk(considering that we have so many dependencies on 3rd party packages).


### What did we learn from the event? ###
- Always do clean build without cached packages(npm/bower/typings).
- Strict the version management of 3rd party packages.
- Not take any live site traffic before complete validation, aka, Routing.
- Keep use the same bits as DEVINT for PROD deployment.
### What are we going to do? ###
- **Short Term**:
 -	More strict version management on 3rd party packages instead of automatic hotfix upgrade, this will only be useful for 1st level dependencies.
 -	Make sure that PROD deployment is always from deployment bits level instead of source code level, aka, share the same deployment bits with DEVINT.

- **Long Term**:
 -	Routing based deployment. Sign-off at website level, once it’s done, put it into the routing pool.
