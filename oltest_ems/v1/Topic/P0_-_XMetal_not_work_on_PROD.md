---
description: na
keywords: na
title: P0 - XMetal not work on PROD
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 587cfb5f-70de-4e25-8de7-fd0d43e631e8
ms.author: sushi@microsoft.com
---
# P0 - XMetal not work on PROD
## P0 - Unable to edit any topic in XMetaL - invalid URL

### What happened? ###
Unable to edit any topic in XMetaL - invalid URL.

### What was the trigger? ###
(Redmond time)
- **2015-07-07 09:35 AM ** OPS team found this issue and started to contact the engineering team.
- **2015-07-07 11:10 AM ** Andy and Zhen joined the bridge, and Andy started to investigate the root cause of XMetal failure.
- **2015-07-07 12:33 PM ** Andy mitigated the issue through packing missed azure DLLs in the deployment bits, both XMetal and CAPS work well under original API endpoint.
  

### What was the "root cause"? ###
The official API service endpoint(**caps-api-prod.azurewebsites.net**) met azure runtime DLL(**msshrtmi.dll, Microsoft.WindowsAzure.Diagnostics.dll, Microsoft.WindowsAzure.ServiceRuntime.dll**) missing issue on some instance of the hosting azure website in the afternoon of July 7th(tracked in [19366](https://capservice.visualstudio.com/DefaultCollection/CAPS/CAPS%20Client%20and%20API/_workitems#_a=edit&id=19366&triage=true), in order to mitigate this issue, we created a replacement endpoint(**caps-api-prod-replace2.azurewebsites.net**), switched the default endpoint used by Client PROD to this one, and E2E monitoring job went back to normal. Unfortunately, none of us was aware that XMetal still connected to the **bad** API endpoint, which caused the P0 issue in Redmond daytime.


### What did we learn from the event? ###
1. Azure is not reliable, we should not rely on it completely, instead, we should take unavailability of azure into consideration while designing the service.
2. E2E monitoring should contain all P0 scenarios.
3. Service insight should mine more system error data from WAD instead of TraceEX only.
4. Do not expose service endpoint directly, instead, use API gateway or library to encapsulate it.

### What are we going to do? ###
1. Andy is working with Azure team to figure out the root cause of GACed DLL missing issue on VM of azure website(Ticket#**115070712917391**).
2. Ted is working with Jun to re-enable XMetal E2E case on hourly monitoring job.
3. Su's team will work with Alex's team to add monitoring on more system error sources like event logs, IIS logs, etc([19426](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/19426?fullScreen=false)).
4. Su's team will upgrade to Azure SDK 2.6 and avoid referencing Azure related DLLs from GAC([19424](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/19424?fullScreen=false) and [19425](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/19425?fullScreen=false)).
5. Su's team will supply a better to manage API endpoint([19445](https://capservice.visualstudio.com/DefaultCollection/CAPS/_workitems/edit/19445)).
