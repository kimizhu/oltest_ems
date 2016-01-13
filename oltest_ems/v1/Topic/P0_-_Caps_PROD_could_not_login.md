---
description: na
keywords: na
title: P0 - Caps PROD could not login
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: bf133355-5a36-464e-b03c-b494e2e43bc5
ms.author: sushi@microsoft.com
---
# P0 - Caps PROD could not login
## P0 Caps PROD could not login

### What happened? ###
User could not access caps-web-prod.azurewebsites.net.

### What was the trigger? ###
(Redmond time)
- **2015-6-2 2:51 PM ** GW reported this issue through email.
- **2015-6-2 4:29 PM ** Su joined the bridge, found the root cause(self-signed cert expiration), and suggested to downgrade the priority of this issue and use official domain as mitigation plan.
- **2015-6-2 5:40 PM ** TED reported the testing failure issue on PROD environemnt.
- **2015-6-2 6:06 PM ** Su discussed the solutions to resolve this issue with TED and team members.
- **2015-6-2 8:32 PM ** Su's team successfully applied the new cert through SSL admin tool, replaced the certs on ACS for related Client azure websites, and started to update the configuration for Sandbox, DEVINT and PROD environemnt of API service.
- **2015-6-2 9:31 PM ** capsweb-devint-wus.azurewebsites.net went to normal.
- **2015-6-2 9:42 PM ** caps-web-prod.azurewebsites.net went to normal. 

### What was the "root cause"? ###
Non-official engineering site used self-signed cert as authentication token encryption/decrytion solution solution among azure website, ACS, and API service, and it got expired on 2015-06-02, which caused users failed to login through ACS on all azure websites(*.azurewebsites.net).

While for official domain(*.microsoftonedoc.com), we used SSL admin issued cert(owned by E&I team) instead, that's why it is not impacted. 


### What did we learn from the event? ###
* Always use cert issued from SSL Admin tool for site that take live traffic, so that there will be reminding email sent out to owner 30 days before the expiration time.
* Do not expose internal websites directly to external customer, instead, give them simplest, official URL so that everyone can be on the same page.
* Put cert related configuration on Azure portal for API service instead of local web.config, so that we can apply change much faster.


### What are we going to do? ###
* Engineering team will work with Ted closely to move users to official domains([18044](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/18044) and [18045](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/18045)) in next 3 months.
* Engineering team(PoC is Su/Rome) will own the SSL admin cert for *.azurewebsites.net, and E&I team(PoC is Ted/GW) will own the SSL admin cert for *.microsoftonedoc.com.
* Improve the cert management on API service side([18037](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/18037) is created for Andy).
