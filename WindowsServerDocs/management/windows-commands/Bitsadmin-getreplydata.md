---
title: Bitsadmin getreplydata
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 819f97d5-b255-4b2d-9f63-0daa73915434
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Bitsadmin getreplydata
Retrieves the server's reply data in hexadecimal format.
## Syntax
```
bitsadmin /GetReplyData <Job>
```
## Parameters
|Parameter|Description|
|-------------|---------------|
|Job|The job's display name or GUID|
## Remarks
Valid only for upload-reply jobs.
## <a name="BKMK_examples"></a>Examples
The following example retrieves the reply data for the job named *myDownloadJob*.
```
C:\>bitsadmin /GetReplyData myDownloadJob
```
## Additional references
[Command-Line Syntax Key](Command-Line-Syntax-Key.md)