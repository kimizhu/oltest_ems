---
description: na
keywords: na
title: P1 - WebRole trace stopped once folder size reaches 4GB
search: na
ms.date: na
ms.tgt_pltfrm: na
ms.assetid: 479ef2f7-5106-4987-8ae8-05cb3f66a996
---
# P1 - WebRole trace stopped once folder size reaches 4GB
P1 - WebRole trace stopped once folder size reaches 4GB
==================================================================

What happened?
--------------

CR WebRole didn't write traces any more since 2014/12/26, including all types of logs. That means WAD on that WebRole was stopped.

What was the trigger?
---------------------

After our initial investigation, we found that the trace cache folder of that Role had just reached 3.99GB. It ran again after we manually deleted some files until next time being filled.
Based on this discovery, we believe it is the full of cache that causes WAD stopped.

What was the "root cause"?
--------------------------

It appears we might be running into one of our common issues with Azure Diagnostics. The specific issue is discussed in the following article: 

Commonly reported issues while leveraging Windows Azure Diagnostic configurations and Best Practices
http://blogs.msdn.com/b/cie/archive/2013/04/16/commonly-reported-issues-while-leveraging-windows-azure-diagnostic-configurations-and-best-practices.aspx 

To sum up the problem, there are two specific settings of Azure diagnostics affecting this issue: one is OverallQuotaInMB in code like we are doing:

         Trace.Listeners.Add(new DiagnosticMonitorTraceListener());
         DiagnosticMonitorConfiguration diagnosticMonitorConfiguration = DiagnosticMonitor.GetDefaultInitialConfiguration();
         diagnosticMonitorConfiguration.OverallQuotaInMB = 4096; 
         diagnosticMonitorConfiguration.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(periodMinute);
         diagnosticMonitorConfiguration.Logs.ScheduledTransferLogLevelFilter = filter;
         diagnosticMonitorConfiguration.Logs.BufferQuotaInMB = 1000;
         DiagnosticMonitor.StartWithConnectionString(account, diagnosticMonitorConfiguration);

…and the other setting is sizeInMB found in the ServiceDefinition.csdef file of our cloud service project:

     <LocalResources>
      <LocalStorage name="DiagnosticStore" sizeInMB="4096" cleanOnRoleRecycle="false" />
    </LocalResources>

The OverallQuotaInMB setting only controls the garbage collection algorithm of diagnostics, it doesn’t actually set any cap on how large the folder “diagnosticstore” in Azure virtual machine can grow.  
The DiagnosticStore value (sizeInMB) controls the size of the folder “diagnosticstore” in Azure virtual machine, and the OverallQuotaInMB controls the WAD garbage collection. 

The problem happens whenever the diagnostics store folder grows larger than the OverallQuotaInMB, and at the same time reaches the limit of the DiagnosticStore LocalStorage element (sizeInMB).  
In order for WAD to do garbage collection of the diagnostics folder it needs a small amount of free space.  If both settings are set to the same value then we can get into the situation where both limits have been reached and WAD does not have any additional space in order to perform garbage collection.  At that point no garbage collection can occur, and no additional logs can be written.

Action Plan
==================== 
We need to make sure that the OverallQuotaInMB is at least 500MB smaller than the configuration for the DiagnosticStore local storage resource. Simply resolve this by changing the sizeInMB value within the ServiceDefinition.csdef file to 8192 and then change code to set OverallQuotaInMB to 7692. Then re-deploy the updated service. 


What did we learn from the event?
---------------------------------

1.	Need more considerate tests before releasing a new feature, especially for some wide-impact components, just like Trace.
2.	Jump out of own code scope, seek to get help from partners, don’t be too confidence of basal layer. 

What are we going to do?
------------------------

1.	Keep monitoring the behavior of Sandbox where our updates has already been applied.
2.	Expand the fix to Devint and PROD after verified.

