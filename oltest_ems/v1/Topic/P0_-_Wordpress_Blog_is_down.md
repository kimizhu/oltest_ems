---
description: na
keywords: na
title: P0 - Wordpress Blog is down
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: b3aa66cb-6c7b-4436-888e-5a1e73bcc142
ms.author: xinyi.zhang@microsoft.com
ms.createdat: 10/10/2015 2:46:24 AM
---
# P0 - Wordpress Blog is down
P0 - Wordpress Blog is down
====

**What happened?**

User cannot visit https://blogs.msdn.microsoft.com. It will show runtime error as following:

*Server Error in '/' Application.*

**What was the trigger?**

* 2015-10-9 19:11 (CST): Pravalika Vajjala reported this issue via email 

* 2015-10-9 19:53 (CST): Dev team was aware of this issue and started investigation

* 2015-10-9 19:55 (CST): Issue has been fixed and website is back to normal


**What was the "root cause"?**

When dev team was fixing a bug about uploaded attachment size limitation, we configured the wrong size to be 2GB, which should be 2GB - 1 byte. This configuration change caused IIS down.


**What are we going to do?**

We need to improve checkin and deployment process for Wordpress blog. Previously when WDS team was working on it, the codes are checked in and deployed to PROD directly. Currently we just follow the current way and in the meanwhile we are working on improving this process and here is the plan already discussed but haven't been done yet:
1. Codes checked into Git first and it will auto deploy to PPE env
2. After PPE is verified, we will manually deploy to PROD

The action items are:
1. Migrate C+E blogs to PPE env as a lot of changes are data related
2. Write deployment script
3. Setup auto deployment to PPE

After phase 1 launch, we will setup test env as well as another code branch for daily development. It will go with sprint model then.



  
