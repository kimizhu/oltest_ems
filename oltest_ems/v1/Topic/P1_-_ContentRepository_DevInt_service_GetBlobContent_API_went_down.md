---
description: na
keywords: na
title: P1 - ContentRepository DevInt service GetBlobContent API went down
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 43d0ff3d-4037-4014-8358-1ba4804c7b0a
ms.author: dfchen@microsoft.com
ms.createdat: 11/17/2014 12:38:31 PM
---
# P1 - ContentRepository DevInt service GetBlobContent API went down
P1 - ContentRepositroy DevInt service GetBlobContent API went down
==================================================================

What happened?
--------------

The GetBlobContent, Entity.Create and Entity.Get APIs failed on instance 3 in CR DevInt with error code 500. Total instance down time was 5 hours 23 mins. 
(Note: there's only 1 instance down out of 6 instances)

Below statistic data was based on chip.

GetBlobContent failed count: 5308,   Entity.Create failed count: 56,   Entity.Get failed count: 59  

What was the trigger?
---------------------

**Start (Shanghai time)**

  * **2014-11-15 09:25 AM** CR DevInt service instance #3 performed scheduled application pool recycle done by IIS every 1740 minutes (default IIS config value).
  * **2014-11-15 09:35 AM** CR team received API System Error alert email.
  * **2014-11-15 09:54 AM** Dflying dumped the stack trace and re-issued the requests but cannot reproduce it (since only one out of six instances was in this unhealthy state). A bug #9137 was created for further tracking.
  * **2014-11-15 01:57 PM** Wilson reported the GetBlobContent API still failed.
  * **2014-11-15 02:29 PM** Jason and Simon started to investigate.
  * **2014-11-15 02:40 PM** Jason found all failed calls were served by instance #3.

**Recovery**

  * **2014-11-15 02:47 PM** Jason rebooted instance #3 and the GetBlobContent API on this instance went back to normal.

What was the "root cause"?
--------------------------

There’s a piece of code, which is to convert a DataTimeOffset object to a DateTime object. And the code was written as converting the DateTimeOffset to a formatted string first and then back to DateTime via DataTime.Parse(), as following:

    DateTime.Parse(dateTimeOffsetObject.ToString()).ToUniversalTime();

Noticed that the convertions to and from strings are both using the default (current) culture.

Based on Kain’s detailed research, after an application pool recycle event, there’s a rare chance that the AMDesignator and/or PMDesignator property(ies) of DateTimeFormatInfo class initialized from the current culture will be set to null in the newly created application pool, thus  DataTime.Parse() (which is using the current culture format) cannot understand the datetime string produced by DateTimeOffset.ToString(), and a FormatException will be thrown. We are still investigating details about this behavior and any new updates will be posted in this RCA.

Many others in the community are reporting the same issue and the recommended solutions are:

 1. Use DateTimeOffset.ToString() and DateTime.ParseExact() with exact format string if possible
 2. Recycle the application pool if this rare case happens

Digging into more of code, we’ve found that there's no need to convert the DataTimeOffset to string then back to DateTime, simply retrieving the DateTime object directly via DateTimeOffset.UtcDateTime property should just do the job. By this way we can then save the object -> string -> object roundtrip and mitigate this issue.

**References**

  * http://stackoverflow.com/questions/1579814/convert-todatetime-causes-formatexception-on-afternoon-date-time-values
  * https://social.msdn.microsoft.com/Forums/en-US/ecebc17f-bb2f-48a7-9bb0-8687bb916fab/datetimeparse-behavior-in-iis-app-pool
  * http://support2.microsoft.com/default.aspx?scid=kb;EN-US;2404988

What did we learn from the event?
---------------------------------

 1. Write culture invariant code more carefully, especially for datetime conversions and string comparisons.
 2. Dig into the details whenever we found an error from any sources and identify impacts as early as possible.

What are we going to do?
------------------------

 1. Use DateTimeOffset.UtcDateTime to get the DateTime value, which will be on DevInt in early S75.
 2. Find a way to specify the instance which will be used to process a request, which will make it easier to locate and verify any further related live site issues. This will be done before the end of S76.