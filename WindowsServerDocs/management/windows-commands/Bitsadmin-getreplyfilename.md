---
title: Bitsadmin getreplyfilename
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 85447184-1732-4816-a365-2e3599551bf8
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Bitsadmin getreplyfilename
Gets the path of the file that contains the server reply.
## Syntax
```
bitsadmin /GetReplyFileName <Job>
```
## Parameters
|Parameter|Description|
|-------------|---------------|
|Job|The job's display name or GUID|
## Remarks
Valid only for upload-reply jobs.
## <a name="BKMK_examples"></a>Examples
The following example retrieves the reply filename for the job named *myDownloadJob*.
```
C:\>bitsadmin /GetReplyFileName myDownloadJob
```
## Additional references
[Command-Line Syntax Key](Command-Line-Syntax-Key.md)