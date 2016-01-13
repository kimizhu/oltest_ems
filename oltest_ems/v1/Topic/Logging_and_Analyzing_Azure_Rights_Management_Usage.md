---
description: na
keywords: na
title: Logging and Analyzing Azure Rights Management Usage
search: na
ms.date: na
ms.service: rights-management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: a735f3f7-6eb2-4901-9084-8c3cd3a9087e
ms.createdat: 1/5/2016 7:53:23 AM
---
# Logging and Analyzing Azure Rights Management Usage
Use the information in this topic to help you understand how you can use usage logging with Azure Rights Management (Azure RMS). The Azure Rights Management service can log every request that it makes for your organization, which includes requests from users, actions performed by Rights Management administrators in your organization, and actions performed by Microsoft operators to support your Azure Rights Management deployment.

You can then use these Azure Rights Management logs to support the following business scenarios:

-   Analyze for business insights.

    Azure Rights Management writes logs in W3C extended log format into an Azure storage account that you provide. You can then direct these logs into a repository of your choice (such as a database, an online analytical processing (OLAP) system, or a map-reduce system) to analyze the information and produce reports. As an example, you could identify who is accessing your RMS-protected data. You can determine what RMS-protected data people are accessing, and from what devices and from where. You can find out whether people can successfully read protected content. You can also identify which people have read an important document that was protected.

-   Monitor for abuse.

    Azure Rights Management logging information is available to you in near-real time, so that you can continuously monitor your company’s use of Rights Management . 99.9% of logs are available within 15 minutes of an RMS-initiated action.

    For example, you might want to be alerted if there is a sudden increase of people reading RMS-protected data outside standard working hours, which could indicate that a malicious user is collecting information to sell to competitors. Or, if the same user apparently accesses data from two different IP addresses within a short time frame, which could indicate that a user account has been compromised.

-   Perform forensic analysis.

    If you have an information leak, you are likely to be asked who recently accessed specific documents and what information did a suspected person access recently. You can answer these type of questions when you use Azure Rights Management and logging because people who use protected content must always get a Rights Management license to open documents and pictures that are protected by Azure Rights Management, even if these files are moved by email or copied to USB drives or other storage devices. This means that you can use Azure Rights Management logs as a definitive source of information for forensic analysis when you protect your data by using Azure Rights Management.

> [!NOTE]
> If you are interested only in the logging of administrative tasks for Azure Rights Management, and do not want to track how users are using Rights Management, you can use the [Get-AadrmAdminLog](https://msdn.microsoft.com/library/azure/dn629430.aspx) Windows PowerShell cmdlet for Azure Rights Management.
> 
> You can also use the Azure classic portal for high-level usage reports that include **RMS usage**, **Most active RMS users**, **RMS device usage**, and **RMS enabled application usage**. To access these reports from the Azure classic portal, click **Active Directory**, select and open a directory, and then click **REPORTS**,

Use the following sections for more information about Azure Rights Management usage logging.

-   [How to enable Azure Rights Management usage logging](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_EnableRMSLogging)

-   [How to access and use your Azure Rights Management usage logs](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_AccesAndUseLogs)

-   [How to manage your Azure Rights Management log storage](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_ManageStorage)

-   [How to delegate access to your Azure Rights Management usage logs](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_Delegate)

-   [How to interpret your Azure Rights Management usage logs](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_Interpret)

-   [Windows PowerShell reference](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_PowerShell)

## <a name="BKMK_EnableRMSLogging"></a>How to enable Azure Rights Management usage logging
Azure Rights Management usage logging is optional, so if you want to use it, you must take specific steps. When you use Azure Rights Management usage logging, there is no change in how  Rights Management works and the logging process itself is free. However, you must provide an Azure storage account for the logs and you will be charged for this storage.

Before you begin, make sure that you meet the following prerequisites to use Azure Rights Management usage logging:

|Requirement|More information|
|---------------|--------------------|
|An IT-managed subscription that includes Azure Rights Management|You must have a Microsoft Azure Rights Management subscription that is managed by your organization. Organizations that use RMS for individuals cannot use Azure Rights Management usage logging.<br /><br />If your organization has users who use RMS for individuals, Azure Rights Management usage logging provides a very compelling business reason to convert RMS for individuals into a Microsoft Azure Rights Management subscription.<br /><br />For more information about the subscriptions that include Azure RMS, see the [Cloud subscriptions that support Azure RMS](../Topic/Requirements_for_Azure_Rights_Management.md#BKMK_SupportedSubscriptions) section in the [Requirements for Azure Rights Management](../Topic/Requirements_for_Azure_Rights_Management.md) topic.<br /><br />For more information about RMS for individuals, see [RMS for Individuals and Azure Rights Management](../Topic/RMS_for_Individuals_and_Azure_Rights_Management.md)|
|Azure subscription|You must have a subscription to Azure and sufficient storage on Azure for your Azure Rights Management logs.|
|Windows PowerShell for Azure Rights Management|If you haven’t already done so, download and install the Windows PowerShell module for Azure Rights Management. You will use Windows PowerShell cmdlets to configure and manage your Azure Rights Management usage logs.<br /><br />For more information, see [Installing Windows PowerShell for Azure Rights Management](../Topic/Installing_Windows_PowerShell_for_Azure_Rights_Management.md).|
Use the following procedure to enable Azure Rights Management usage logging, which includes steps to create an Azure storage account and then configure Azure to use this storage account for your  Rights Management logs.

> [!NOTE]
> This procedure assumes that you have an Azure account. Azure Rights Management usage logging supports individual accounts but as a best practice, use work or school accounts. In addition, we recommend that you create a dedicated storage account for your Rights Management logs. You must share the storage access keys with Azure Rights Management, and potentially with other people if they will also use the log files.
> 
> For more information about Azure storage, see the [Azure documentation for Storage](http://azure.microsoft.com/documentation/services/storage/).

#### How to create your storage account and enable Azure Rights Management usage logging

1.  Sign in to the [Azure portal](https://portal.azure.com/).

2.  On the Hub menu, select **New** -&gt; **Data + Storage** -&gt; **Storage account**.

    > [!TIP]
    > If you do not see this option, check that you have an Azure subscription in addition to your subscription for Rights Management.

3.  Keep the default deployment model of **Classic**, and then click **Create**.

    Specify the name for your storage account, and if necessary, change the selected options for the **Pricing tier**, **Resource Group**, **Subscription**, and whether to **Pin to dashboard**. and then click **CREATE**. Wait for the **Creating Storage Account** activity to complete.

4.  Click the newly created storage account, and then click **Settings**.

5.  In the **Settings** blade, click the **Keys** icon.

6.  In the **Manage keys** blade, copy the value of the  **PRIMARY ACCESS KEY** and close the blade.

7.  Start Windows PowerShell with the **Run as administrator** option. Run the [Connect-AadrmService](https://msdn.microsoft.com/library/azure/dn629415.aspx) command to connect to the Azure Rights Management service:

    ```
    Connect-AadrmService
    ```

8.  Use the [Set-AadrmUsageLogStorageAccount](https://msdn.microsoft.com/library/azure/dn629426.aspx) command to specify where you want to keep your Azure Rights Management usage logs, replacing *&lt;Access_Key&gt;* with the primary access key that you copied in step 6 and *&lt;StorageAccount&gt;* with the name of the storage account that you created in step 3:

    ```
    $accesskey = convertto-securestring -asplaintext -force –string <Access_Key>
    Set-AadrmUsageLogStorageAccount -AccessKey $accesskey -StorageAccount <StorageAccount>
    ```

9. Use the [Enable-AadrmUsageLogFeature](https://msdn.microsoft.com/library/azure/dn629421.aspx) command to enable Azure Rights Management usage logging:

    ```
    Enable-AadrmUsageLogFeature
    ```
    You should see the message: **The usage log feature is enabled for the Rights management service.**

Now that usage logging is enabled, Azure Rights Management starts to log all actions for your organization and saves this information to your storage account. Logging information is not available before this point.

For more information about how to create storage accounts, see the  [Create a storage account](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) section from the [About Azure storage accounts](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) in the Azure documentation.

## <a name="BKMK_AccesAndUseLogs"></a>How to access and use your Azure Rights Management usage logs
Azure Rights Management writes logs to your Azure storage account as a series of blobs. Each blob contains one or more log records, in W3C extended log format. The blob names are numbers, in the order in which they were created. The [How to interpret your Azure Rights Management usage logs](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_Interpret) section later in this document contains more information about the log contents and their creation.

It can take a while for logs to appear in your storage account after an Azure Rights Management action. Most logs appear within 15 minutes.

The storage account that you created for your Azure Rights Management usage logs is like a mailbox and supports reading directly from the storage account, but it is not optimized to be used in this way. Instead, for best performance and to help reduce costs, we recommend that you download the logs to local storage, such as a local folder, a database, or a map-reduce repository.

You can download your usage logs in two ways:

-   Use the Windows PowerShell cmdlet [Get-AadrmUsageLog](https://msdn.microsoft.com/library/azure/dn629401.aspx).

    This is the simplest way to access your usage logs. This cmdlet downloads logs to your computer, downloading each blob as a file to a location that you specify.

-   Use the [Azure Storage SDK](http://www.windowsazure.com/en-us/develop/net/) to write your own custom application to download the logs.

    A custom application can provide more flexibility than the Get-AadrmUsageLogs cmdlet. For example, you might want to delegate the download of logs to somebody or a process that must not use your Azure Rights Management administrative credentials. Or, you might want to poll the logs in real time because you want to monitor for misuse.

#### To download your usage logs by using PowerShell

-   Start Windows PowerShell with the **Run as administrator** option and run **Get-AadrmUsageLog –Path &lt;location&gt;**. For example, after creating a folder named **logs** on your E drive:

    -   To download all available logs to your E:\logs folder: `Get-AadrmUsageLog -Path "e:\logs"`

    -   To download a specific range of blobs: `Get-AadrmUsageLog –Path "e:\logs" –FromCounter 1024 –ToCounter 2047`

When you run these cmdlets, Windows PowerShell displays the name of the last blob that was downloaded. You can assign this name to a variable, which lets you run Get-AadrmUsageLog in a loop or a schedule job, downloading only the incremental logs each time.

For example:

**PS C:\&gt; $LastBlobName = Get-AadrmUsageLog –Path "e:\logs"**

**1527**

**PS C:\&gt; $LastBlobName**

**1527**

> [!TIP]
> You can aggregate all your downloaded log files into a CSV format by using [Microsoft’s Log Parser](http://www.microsoft.com/download/details.aspx?id=24659), which is a tool to convert between various well-known log formats. You can also use this tool to convert data to SYSLOG format, or import it into a database. After you have installed the tool, run **LogParser.exe /?** for help and information to use this tool. For example, you might run the following command to import all information into a .log file format: **logparser –i:w3c –o:csv “SELECT &#42; INTO AllLogs.csv FROM &#42;.log”**.

You can suspend and resume usage logging. When you suspend logging, Azure Rights Management retains your storage account information, so that you can easily resume logging again.

#### To suspend and resume usage logging

-   To suspend logging, use the following cmdlet: [Disable-AadrmUsageLogFeature](https://msdn.microsoft.com/library/azure/dn629404.aspx)

-   To resume logging, use the following cmdlet: [Enable-AadrmUsageLogFeature](https://msdn.microsoft.com/library/azure/dn629421.aspx)

-   To check whether logging is enabled or disabled, use the following cmdlet: [Get-AadrmUsageLogFeature](https://msdn.microsoft.com/library/azure/dn629425.aspx)

    A value of **True** means that usage logging is enabled for Azure Rights Management and a value of **False** means that usage logging is disabled.

## <a name="BKMK_ManageStorage"></a>How to manage your Azure Rights Management log storage
You are charged for the storage space that is used to keep your Azure Rights Management logs.

Azure Rights Management does no automatic management of your usage log files. If you take no action, they will remain in your storage account. However, to conserve space and reduce storage costs, you might want to delete them after you have downloaded them. Or you can choose which files to delete. With one exception, Azure Rights Management does not use these log files, so there are no restrictions about when you can delete them.

The file that you must not delete (or modify) is named **metadata** that is in the **rms-metadata** container. Azure Rights Management uses this blob to keep track of the last blob number that it used. If this file is deleted, Azure Rights Management starts a new container for logs, with a blob number that starts from 1, and all future downloads that use the Get-AadrmUsageLog cmdlet use this new container to download log files. As a result, any logs in the original container are retained, but orphaned. The only way to download these orphaned logs is to use the Azure storage SDK.

> [!TIP]
> Instead of managing your Azure Rights Management log storage yourself, you can delegate this management function to another company by sharing your storage account name and access key. For more information, see the [How to delegate access to your Azure Rights Management usage logs](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_Delegate) section later in this topic.

In some circumstances, you might want to regenerate your storage access keys. For example:

-   You change the company that manages your Azure Rights Management usage logs.

-   You suspect that your storage access key is compromised.

If this happens to you, this is when you use the secondary access key (on the assumption that you were previously using the primary access key) to ensure service continuity. When you regenerate the access key that you were not previously using, you then configure Azure Rights Management to use the new key. Use the following procedure to regenerate the secondary access key and configure Azure Rights Management to use it:

#### To regenerate the secondary access key

1.  Sign in to the [Azure  portal](https://portal.azure.com/).

2.  Click **All Resources**, and search for your storage by typing the storage name in the text box..

3.  Select your storage, and click **Settings**.

4.  Click the **Keys** icon.

5.  In the **Manage  keys** section, click **Regenerate secondary key** click Yes to confirm that you want to regenerate secondary access key, and copy the new secondary access key to the clipboard.

    Do not close the Azure portal.

6.  Start Windows PowerShell with the **Run as administrator** option and use the [Set-AadrmUsageLogStorageAccount](https://msdn.microsoft.com/library/azure/dn629426.aspx) cmdlet to configure Azure Rights Management to use this new access key, replacing *&lt;StorageAccount&gt;* with the name of your storage account and *&lt;Access_Key&gt;* with the regenerated secondary access key that you just copied:

    ```
    Set-AadrmUsageLogStorageAccount -StorageAccount <StorageAccount>-AccessKey <Access_Key>
    ```
    > [!WARNING]
    > Although you can switch to another storage account when you run this command, this action orphans your previous logs and they will no longer be accessible to the Set-AadrmUsageLogStorageAccount cmdlet or similar management commands and functions.

7.  Back in the Azure portal, **Manage  Keys** section, regenerate your primary access key.

For more information about how to manage your storage access keys, see the  [Manage your storage access keys](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) section from the [About Azure storage accounts](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/) in the Azure documentation.

## <a name="BKMK_Delegate"></a>How to delegate access to your Azure Rights Management usage logs
You can delegate access to your RMS logs by sharing your storage account name and access key. You might want to delegate access to another administrative user, to a developer within your own organization, or to an independent software vendor (ISV). Because they will not have your RMS administrator credentials, they cannot use the Get-AardrmUsageLog cmdlet to download the RMS logs. Instead, they must use the Windows Storage SDK to download the logs. Alternatively, they can write an application to read the logs directly from Azure Storage.

It is safe to share your storage account name and access key in this way when the storage account is dedicated to your RMS logs. Even though other people have your access key, they cannot use this to access any other storage account or use your RMS tenant account.

## <a name="BKMK_Interpret"></a>How to interpret your Azure Rights Management usage  logs
Use the following information to help you interpret the Azure Rights Management usage  logs.

### The storage account layout
The first time Azure Rights Management writes logs to your storage account, it creates the following two containers:

-   **Rms-metadata**: This container is reserved for Azure Rights Management . Do not modify or delete this container.

-   **Rms-logs-&lt;guid&gt;**: This container is where Azure Rights Management creates and saves the logs. You can safely delete any files in this container if you no longer want the logging information that they contain.

Over time, Azure Rights Management might create additional **Rms-logs-&lt;guid&gt;** containers. For example, if the **Rms-metadata** container becomes corrupted or it is accidentally deleted, Azure Rights Management resets its contents and creates a new **Rms-logs-&lt;guid&gt;** container for all future logs. The old logs in the old container are not deleted but are orphaned.

### The log sequence
Azure Rights Management writes the logs as a series of blobs. Each blob contains one or more log records, in extended W3C log format.

The name of each blob is a number. Within each log container the first blob is named 000000001. Each blob is named sequentially in the order in which it was created. Each blob contains one or more log records. Each log record has a UTC time stamp that indicates when the corresponding request was served by Azure Rights Management.

> [!IMPORTANT]
> The Azure Rights Management logging system is optimized to provide you with logs quickly, rather than in strict sequential order. The order of the blobs, as well as the order of log records within a blob, might be out of chronological sequence. The only reason the blobs are named sequentially is to enable you to efficiently download them incrementally.
> 
> Two examples of potential log sequence results:
> 
> -   The log records in blob 000000004 might overlap chronologically with the log records in blob 000000003. In an extreme case, all log records in blob 000000004 might have been generated before all log records in blob 000000003.
> -   The second log record in a blob might have been generated before the first log record, but was written to storage in reverse order.

Before you analyze your Azure Rights Management usage logs, we recommend that you download and import the log into a repository where you can sort the logs based on their timestamp. For more information about to download the logs, see the [How to access and use your Azure Rights Management usage logs](../Topic/Logging_and_Analyzing_Azure_Rights_Management_Usage.md#BKMK_AccesAndUseLogs) section in this topic.

Because the logs are not necessarily chronological but the majority of them are written within 15 minutes of the request, when you identify the logs that you want by using their timestamp , add 15 minutes to the time that you are interested in. Then download these logs. This strategy should ensure that you get almost all logs.

One other thing to remember is that the timestamp on each log record is the local time of the Azure Rights Management service that processed the request. Because Azure Rights Management runs on multiple servers across multiple data center, sometimes the logs might seem to be out of sequence, even when they are sorted by their timestamp. However, the different is small and usually within a minute. In most cases, this is not an issue that would be a problem for log analysis.

### The blob format
Each blob is in W3C extended log format. It starts with the following two lines:

**#Software: RMS**

**#Version: 1.1**

The first line identifies that these are Azure Rights Management logs. The second line identifies that the rest of the blob follows the version 1.1 specification. We recommend that any applications that parse these logs verify these two lines before continuing to parse the rest of the blob.

The third line enumerates a list of field names that are separated by tabs:

**#Fields: date            time            row-id        request-type           user-id       result          correlation-id          content-id                owner-email           issuer                     template-id             file-name                  date-published      c-info         c-ip**

Each of the subsequent lines is a log record. The values of the fields are in the same order as the preceding line, and are separated by tabs. Use the following table to interpret the fields.

|Field name|W3C data type|Description|Example value|
|--------------|-----------------|---------------|-----------------|
|date|Date|UTC date when the request was served.<br /><br />The source is the local clock on the server that served the request.|2013-06-25|
|time|Time|UTC time in 24-hour format when the request was served.<br /><br />The source is the local clock on the server that served the request.|21:59:28|
|row-id|Text|Unique GUID for this log record.<br /><br />This value is useful when you aggregate logs or copy logs into another format.|1c3fe7a9-d9e0-4654-97b7-14fafa72ea63|
|request-type|Name|Name of the RMS API that was requested.|AcquireLicense|
|user-id|String|The user who made the request.<br /><br />The value is enclosed in single quotation marks. Some request types are anonymous, in which case the value is ”.|‘joe@contoso.com’|
|result|String|‘Success’ if the request was served successful.<br /><br />The error type in single quotation marks if the request failed.|‘Success’|
|correlation-id|Text|GUID that is common between the RMS client log and server log for a given request.<br /><br />This value can be useful to help troubleshooting client issues.|cab52088-8925-4371-be34-4b71a3112356|
|content-id|Text|GUID, enclosed in curly braces that identifies the protected content (for example, a document).<br /><br />This field has a value only if request-type is AcquireLicense and is blank for all other request types.|{bb4af47b-cfed-4719-831d-71b98191a4f2}|
|owner-email|String|Email address of the owner of the document.|alice@contoso.com|
|issuer|String|Email address of the document issuer.|alice@contoso.com (or) FederatedEmail.4c1f4d-93bf-00a95fa1e042@contoso.onmicrosoft.com'|
|Template-id|String|ID of the template used to protect the document.|{6d9371a6-4e2d-4e97-9a38-202233fed26e}|
|File-name|String|File name of the document that was protected.|TopSecretDocument.docx|
|Date-published|Date|Date when the document was protected.|2015-10-15T21:37:00|
|c-info|String|Information about the client platform that is making the request.<br /><br />The specific string varies, depending on the application (for example, the operating system or the browser).|'MSIPC;version=1.0.623.47;AppName=WINWORD.EXE;AppVersion=15.0.4753.1000;AppArch=x86;OSName=Windows;OSVersion=6.1.7601;OSArch=amd64'|
|c-ip|Address|IP address of the client that makes the request.|64.51.202.144|

#### Exceptions for the user-id field
Although the user-id field usually indicates the user who made the request, there are two exceptions where the value does not map to a real user:

-   The value **'microsoftrmsonline@&lt;YourTenantID&gt;.rms.&lt;region&gt;.aadrm.com'**.

    This indicates an Office 365 service, such as Exchange Online or Sharepoint Online, is making the request. In the string, *&lt;YourTenantID&gt;* is the GUID for your tenant and *&lt;region&gt;* is the region where your tenant is registered. For example, **na** represents North America, **eu** represents Europe, and **ap** represents Asia.

-   If you are using the RMS connector.

    Requests from this connector are logged with the service principal name that RMS automatically generates when you install the RMS connector.

#### Typical request types
There are many request types for Azure Rights Management but the following table identifies some of the most typically used request types.

|Request type|Description|
|----------------|---------------|
|AcquireLicense|A client from a Windows based machine is requesting a license for RMS-protected content.|
|AcquirePreLicense|A client, on behalf of the user, is requesting for a license for RMS-protected content.|
|AcquireTemplates|A call was made to acquires templates based on template IDs|
|AcquireTemplateInformation|A call was made to get the IDs of the template from the service.|
|AddTemplate|A call is  made from the Azure classic portal to add a template.|
|BECreateEndUserLicenseV1|A call is  made from a mobile device to create an end-user license.|
|BEGetAllTemplatesV1|A call is  made from a mobile device (back-end) to get all the templates.|
|Certify|The client is certifying the content for protection.|
|Decrypt|The client is attempting to decrypt the RMS-protected content.|
|DeleteTemplateById|A call is  made from the Azure classic portal, to delete a template by template  ID.|
|ExportTemplateById|A call is  made from the Azure classic portal to export a template based on a template ID.|
|FECreateEndUserLicenseV1|Similar to the AcquireLicense request but from mobile devices.|
|FECreatePublishingLicenseV1|The same as Certify and GetClientLicensorCert combined, from mobile clients.|
|FEGetAllTemplates|A call is  made, from a mobile device (front-end) to get the templates.|
|GetAllTemplates|A call is  made from the Azure classic portal, to get all the templates.|
|GetClientLicensorCert|The client is requesting a publishing certificate (that is later used to protect content) from a Windows-based computer.|
|GetConfiguration|An Azure PowerShell cmdlet is called to get the configuration of the Azure RMS tenant.|
|GetConnectorAuthorizations|A call  is made from the RMS connectors to get their configuration from the cloud.|
|GetTenantFunctionalState|The Azure classic portal is checking whether Azure RMS is activated.|
|GetTemplateById|A call is  made from the Azure classic portal to get a template by specifying a template ID.|
|ExportTemplateById|A call is being made from the Azure classic portal to export a template by specifying a template ID.|
|FindServiceLocationsForUser|A call is made to query for URLs, which is used to call Certify or AcquireLicense.|
|ImportTemplate|A call is  made from the Azure classic portal to import a template.|
|ServerCertify|A call is  made from an RMS-enabled client (such as SharePoint) to certify the server.|
|SetUsageLogFeatureState|A call is  made to enable usage logging.|
|SetUsageLogStorageAccount|A call is  made to specify the location of the Azure RMS logs.|
|SignDigest|A call is made when a key is used for signing purposes. This is called typically once per AcquireLicence (or FECreateEndUserLicenseV1), Certify, and GetClientLicensorCert (or FECreatePublishingLicenseV1).|
|UpdateTemplate|A call is  made from the Azure classic portal to update an existing template.|

## <a name="BKMK_PowerShell"></a>Windows PowerShell reference
Use the following Windows PowerShell cmdlets to help you configure and use Azure Rights Management usage logging:

-   [Disable-AadrmUsageLogFeature](https://msdn.microsoft.com/library/azure/dn629404.aspx)

-   [Enable-AadrmUsageLogFeature](https://msdn.microsoft.com/library/azure/dn629421.aspx)

-   [Get-AadrmUsageLog](https://msdn.microsoft.com/library/azure/dn629401.aspx)

-   [Get-AadrmUsageLogFeature](https://msdn.microsoft.com/library/azure/dn629425.aspx)

-   [Get-AadrmUsageLogLastCounterValue](https://msdn.microsoft.com/library/azure/dn629423.aspx)

-   [Get-AadrmUsageLogStorageAccount](https://msdn.microsoft.com/library/azure/dn629419.aspx)

-   [Set-AadrmUsageLogStorageAccount](https://msdn.microsoft.com/library/azure/dn629426.aspx)

For more information about using Windows PowerShell for Azure Rights Management, see [Administering Azure Rights Management by Using Windows PowerShell](../Topic/Administering_Azure_Rights_Management_by_Using_Windows_PowerShell.md).

## See Also
[Using Azure Rights Management](../Topic/Using_Azure_Rights_Management.md)

