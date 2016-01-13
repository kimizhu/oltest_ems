---
description: na
keywords: na
title: P1 - Markdown Conversion: Production contents were converted by accident
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 67f37bd6-b88a-45ce-a453-9948065b03b7
ms.author: tianzh@microsoft.com
---
# P1 - Markdown Conversion: Production contents were converted by accident
### What happened? ### 

Migration team has this self-service Markdown conversion tool:
\\rr1dx2tpehost01\MigrationToolsDrop\CAPSMDConvertTool_ClickOnce
On Dec 8th, during bug investigation about 2200 topics in a production portfolio were converted to Markdown by mistake.    

### What was the trigger? ### 
(Shanghai Time)
- **12/3,2015 11:17 AM** Partner reports the issue of the conversion tool throwing exceptions.
- **12/4,2015 1:43 AM** Partner provided the portfolio url.
- **12/8/2015 2:00 PM** Local debugging started with the url partner provided, during which "Report Only" mode was not turned on, 2200 topics were actually converted.

### What was the "root cause"? ### 

- **Missed information in communication**.
In the conversion tool there is a "Report Only" mode, with which user can simulate the conversion and get the report. In the original email partner mentioned *"I was trying to run the MD report against the Azure_Ref portfolio"*, but this information was missed in the following communication.

- **Lack of vigilance with "Prod" keyword**
The url partner provided starts with "prod.microsoftonedoc.com", however the "prod" keyword did not bring enough attention.

- **Conversion tool did not have permission control**
Any tool that can touch prod data should have permission control.  

### What did we learn from the event? ### 

- Pay attention to details in communication with partners
- Always be alerted when performing tasks that can touch CAPS data, prod or not.
- Any tool that can touch prod data should have permission control.

### What are we going to do? ### 
- Revert the converted topics to where they were.**[done]**
- Add permission control and alert/confirmation to conversion tool.
- Get the list of backend tools that can touch prod data, evaluate the cost of sharing the same permission control library  