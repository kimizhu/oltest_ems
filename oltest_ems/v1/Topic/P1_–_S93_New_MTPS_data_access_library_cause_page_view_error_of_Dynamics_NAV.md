---
description: na
keywords: na
title: P1 – S93 New MTPS data access library cause page view error of Dynamics NAV
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 42a7232e-3496-4d12-a63b-04b6ec3ddb48
ms.author: renhe.li@microsoft.com
---
# P1 – S93 New MTPS data access library cause page view error of Dynamics NAV
## P1 – S93 New MTPS data access library cause page view error of Dynamics NAV ## 


### What happened? ###

When Customer visited the following [page](https://msdn.microsoft.com/en-us/library/aa155268.aspx), it opens with a "Error. Sorry this page is unavailable. Please try again later”.  A couple of child contents and TOCs under this root page were also impacted.

This issue lasts for 53 hours in total. The time between the issue raised by partner team and issue fixed in production was 50 hours.

### What was the trigger? ###
    
•	**12/15/2015 13:00 PM PST**: Ops Team finished S93 Rendering deployment.

•	**12/15/2015 16:08 PM PST**: Ops team got a LSI that customer saw requested page is not available when they navigated to Dynamics NAV pages.

•	**12/16/2015 14:03 PM PST**: Ops team escalated the issue to rendering team and engineer team acknowledged.

•	**12/16/2015 18:28 PM PST**: The issue was escalated to Mtps backend team and after some investigations, Engineer team found it's an issue in MTPS data access library which was released in the S93 Rendering deployment. 

•	**12/17/2015 01:35 AM PST**: To mitigate the issue, MTPS team updated the stored procedure of ‘dbo.usp_getArchivedNodes’ and provided a new data access library. Rendering team generated new bits based on library. 

•	**12/17/2015 17:18 PM PST**: Ops deployed the stored procedure and Web platform of rendering.

•	**12/17/2015 18:12 PM PST**: Engineer team verified the result on production.


### What was the "root cause"? ###

The root cause is that in the implementation our recently Archive improvement, we assume all the asset id within one TOC would be unique and wrote code based on this assumption. However, the Toc of Dynamics NAV (below) contains a couple of items with the same assetId but different versions.

  <toc:Node toc:Title="Microsoft Dynamics NAV 2016" toc:SubTree="AssetId:NAV%7cnav_master%7c%24%5cnav_master.hxt%400" toc:SubTreeVersion="nav.90" toc:SubTreeLocale="en-us" toc:Target="**AssetId:08bb7c4d-11e3-40e1-be3a-3378f0a826a4**" toc:TargetLocale="en-us" toc:TargetVersion="nav.90" toc:IsPhantom="true" />
  <toc:Node toc:Title="Microsoft Dynamics NAV 2015" toc:SubTree="AssetId:NAV%7cnav_master%7c%24%5cnav_master.hxt%400" toc:SubTreeVersion="nav.80" toc:SubTreeLocale="en-us" toc:Target="**AssetId:08bb7c4d-11e3-40e1-be3a-3378f0a826a4**" toc:TargetLocale="en-us" toc:TargetVersion="nav.80" toc:IsPhantom="true" />

We did not handle this case which caused data issue.
We checked other products including SQL, Visual Studio and Office, we only found the Tocs in Dynamics NAV has this issue.

### What did we learn from the event? ###

•	We should not take assumptions about what the content data looks like in our production storage. There are always exceptions considering we maintains more than 60 million contents.

•	The code we wrote should be robust to handle unexpected situations instead of crash the page view. 

•	The data in PPE and production are out of sync. The problem does not occur in PPE.

•	Enhance our monitoring system to find out issues more quickly on our PPE and production.



### What are we going to do? ###

•	The content data is not consistent between production and PPE and it caused a lot of problems. We will work out a process to synchronize data between PPE and production.

•	Add more test cases to handle special cases.

•	Take this as a sample in developer’s best practice to avoid the similar mistake in the future.



