---
description: na
keywords: na
title: P1 - API Service production was deployed connecting to wrong CR endpoint
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 3905b6aa-0eed-4847-9050-1b1586e785f3
ms.author: andxu@microsoft.com
---
# P1 - API Service production was deployed connecting to wrong CR endpoint
## P1 - API Service production was deployed connecting to WRONG CR endpoint ##

### What happened? ###
The impact was **all the prod APIs return sandbox data**.

### What was the trigger? ###

**Start** (Shanghai time)
 - **2015-05-27 3:12 PM** Caps Jenkins deploy server started the #3 deploy: http://caps-api-deploy.cloudapp.net:8000/job/api_service_prod/3/
 - **2015-05-27 3:52 PM** Catherine reported that production is not available.
 - **2015-05-27 4:00 PM** Sushi found that search API reported index_not_found ERROR.
 - **2015-05-27 4:02 PM** [Prod] CAPS E2E Experience Hourly Report reported that: Total Passing Rate: 0.00 %
 - **2015-05-27 4:07 PM** Zhen Jiao reported that he cannot see any projects under csiprod1.
 
**Recovery**
 - **2015-05-27 4:10 PM** Andy submits the configuration fix for production deploy.
 - **2015-05-27 4:12 PM** Caps Jenkins deploy server started the #6 deploy: http://caps-api-deploy.cloudapp.net:8000/job/api_service_prod/6/

**Verification**

 - **2015-05-27 4:18 PM** Received confirmation from Catherine the production is available.
 - **2015-05-27 5:03 PM** Received normal from CAPS E2E Experience Hourly Report:Total Passing Rate: 90.91 %

### What was the "root cause"? ###

API Service web.config uses an risk way of regex replacing in configuration file during deployment: the CR endpoint will be decided by a configuration value in web.config, in different environments, this configuration value should be different, this is done by one step named "config_patch" which uses a configuration mapping to regex replace web.config. Andy was to initiate new production deploy definition in Jenkins to archive the API service jenkins-autodeploy feature, but he forgot to change the configuration mapping which has missed the prod configurations.

### What did we learn from the event? ###
 
 1. We must to ensure a safe deploy using some verify step: like e2e. Currently API deploy will first deploy and then verify the result.
 2. We should also need to build a verify mechanisms based the response content of the API rather than status code and ping/pong.     
 3. Configuration is critical, sometimes it could hurts the whole system.

### What are we going to do? ###

 1. Change the API deploy process to add verifications to block such mistakes come into production.
 2. Add insight monitors to monitor API results.
 3. Use Azure website portal to set environment info, and centralize the configuration info on Azure blob instead of web.config.
 

