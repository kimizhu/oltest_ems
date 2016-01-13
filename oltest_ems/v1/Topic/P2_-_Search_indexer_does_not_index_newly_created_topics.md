---
description: na
keywords: na
title: P2 - Search indexer does not index newly created topics
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: dff88e71-2543-4ba8-bfb8-b0910d9354aa
ms.author: reyang@microsoft.com
ms.createdat: 8/21/2014 2:13:15 AM
---
# P2 - Search indexer does not index newly created topics
## P2 - Search indexer does not index newly created topics ##

### What happened? ###
The impact was **newly created topics in DevInt dogfooding tenant didn't show in search results after 15 seconds**, the time taken to index newly created topics increase from 3~7 seconds to 5~10 minutes.

### What was the trigger? ###


### What was the "root cause"? ###


### What did we learn from the event? ###
 1. Never trust the data from external
 2. Illegal data should be handled in a way that doesn't impact valid data processing.

### What are we going to do? ###

 1. Detect illegal data and route them to separate poison queue.

