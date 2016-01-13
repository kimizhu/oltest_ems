---
description: na
keywords: na
title: P0 - Client can not be loaded successfully after ambient config changes
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: b43abf76-2c8e-46b6-aa41-5b362f62ac59
ms.author: penlv@microsoft.com
---
# P0 - Client can not be loaded successfully after ambient config changes
## P0 Client can not be loaded successfully after ambient config changes##
### What happened? ###

**Web Client is not accessible** for 10 minutes across ALL environments (DevInt, Production, Sandbox), getting HTTP 500 error.

### What was the trigger? ###

- **2014-12-24 16:20 PM** From Experience Team's E2E report, Devint caes pass rate was 7% and most cases failed.
- **2014-12-24 16:22 PM** After some investigation Peng Lv found out that the newest commit to ambient config was not a valid JSON. As Client does not have any fault tolerance as API and CR does, Client couldn't load configs from ambient config, which leads to HTTP 500 error.
- **2014-12-24 16:34 PM** Peng Lv push a new commit to amibient config repository and the config was delpoyed to Azure immediately, everything was back to normal.

### What was the "root cause"? ###

- This is not the first time our developers break the ambient config and crash Client. Ambient config is JSON based and Client would parse this config once it is downloaded to users' browser, if the config is not a valid JSON or miss some necessary parts Client/API/CR/XMetaL requires, all these components would break.

### What did we learn from the event? ###
- Treating config as code. Since these configs are all simple JSON, developers might pay less attention to its quality and correctness.
- Sharing the same config between all environments is always a ticking time bomb.
- Since we don't have any build phase before the config is deployed to Azure, we still need to have these configs well tested.

### What are we going to do? ###
- Seperate configs for different configs from local, dev, test and production. Besides, add some fall-back mechanism cin Client code like other C# components do and add pre-lint phase before anyone push new commits to the git repository or these configs are deployed to Azure. [User Story related][1]

### Possible Solutions ###
First, we should add a pre-deploy script to validate all json configurations. Secondly, we need to seperate configs between Prod and other environments, then we can even avoid issues like [Structured Query not working in PROD][2]. Since this is a breaking change, Xinyi would have a discussion with other feature team owners and make the final discion.

  [1]: https://capservice.visualstudio.com/web/wi.aspx?pcguid=3afc7cea-e643-4785-a32c-e3a73e6f08db&id=10634
  [2]: /#/organizations/950e523a33b54538824f57577374d39e/projects/b52b2ce1-048f-441f-92b9-78abb8bf42f5/containers/378d2d59-f457-46c6-8ed3-f72afeececdf/articles/54134655-f91e-4d3a-ad24-915d6fa58f40/locales/en-US