---
description: na
keywords: na
title: P0 - SQL 14 publishing to live on prod tenant by developer
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 62de2ad6-2b4b-45c8-8374-dc3fc6308470
ms.author: yaozheng@microsoft.com
ms.createdat: 6/1/2015 2:32:59 AM
---
# P0 - SQL 14 publishing to live on prod tenant by developer
## P0 SQL 14's docset was publishing to live on production by engineer

### What happened? ###
In the production environment, Yaohai did a live publish on the portfolio [SQL 14](https://caps-web-prod.azurewebsites.net/#/organizations/e6f6a65cf14f462597b64ac058dbe1d0/projects/acb1e239-07de-4739-ad90-d32720c7417b/containers/883fd973-a815-4b1d-9c39-aeb708b3ba5c/articles/0ed3caf1-249d-4307-b588-d8a0ef315faa/locales/en-US) at 9PM, 28th May, Redmond time. This made the Books Online docset for SQL 14 broken. Ideally, developers should not modify the content of production environment.    

### What was the trigger? ###
(Shanghai time)
- **2015-5-29 12:01 PM ** [Bug#17856](https://capservice.visualstudio.com/web/wi.aspx?pcguid=3afc7cea-e643-4785-a32c-e3a73e6f08db&id=17856) was routed to Yaohai for CAPS UI side investigation. From the email thread, this bug was about the failure of publishing to live.
- **2015-5-29 12:30 PM ** Following the reproduce steps, Yaohai queried the problematic [topic](https://caps-web-prod.azurewebsites.net/#/organizations/e6f6a65cf14f462597b64ac058dbe1d0/projects/acb1e239-07de-4739-ad90-d32720c7417b/containers/65401ef7-9ac2-4efc-9cec-200671035e56/articles/431ab2d2-5517-4372-9980-142b05427c08/locales//general) on the docset view.
- **2015-5-29 12:31 PM ** Yaohai click the publishing to live button of the topic in order to collect some diagnostic data.
- **2015-5-29 12:32 PM ** This triggered the whole Books Online TOC to be published to live, thus the TOC broken. 

### What was the "root cause"? ###
* The admin permission was granted to Yaohai on the product environment in an unnoticed situation. Normally, the engineers should not be granted this modification permission on the production environment.  
* UI client had a [Bug#17872](https://capservice.visualstudio.com/DefaultCollection/CAPS/CAPS%20Client%20and%20API/_workitems#_a=edit&id=17872&triage=true) on the query result from docset view. This bug will trigger publishing the selected TOC nodes instead of topics from the query result. In this case, the Books Online was selected for publishing, but not the [topic](https://caps-web-prod.azurewebsites.net/#/organizations/e6f6a65cf14f462597b64ac058dbe1d0/projects/acb1e239-07de-4739-ad90-d32720c7417b/containers/65401ef7-9ac2-4efc-9cec-200671035e56/articles/431ab2d2-5517-4372-9980-142b05427c08/locales//general).    
* Engineer wasn't aware of the production environment tenant switch. 

### What did we learn from the event? ###
* Refrain engineers permission on production to avoid ruining the production environment. 
       
* We need to have an applicable methods for engineers to reproduce production issues or verify the fix.

### What are we going to do? ###
* Refrain engineer's permission on the production environment([17972](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/17972)).
* Increase the priority of audit history of permission as a backlog feature([17971](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/17971)).
* When permission grant is necessary for some cases, it should be notified to the related parties: the org admin, the people who grants permission and whom the permission was assigned to.  
* Figure out better ways(DEVINT environment, testing tenant, temporary permission),  to reproduce production issues or verify fix without modifying the production environment. 
