---
title: Bitsadmin setpriority
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 90788363-01a2-4d7c-a560-a3eba45b5e9e
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Bitsadmin setpriority
Sets the priority of the specified job.
## Syntax
```
bitsadmin /SetPriority <Job> <Priority>
```
## Parameters
|Parameter|Description|
|-------------|---------------|
|Job|The job's display name or GUID|
|Priority|One of the following values:<br /><br />-   FOREGROUND<br />-   HIGH<br />-   NORMAL<br />-   LOW|
## <a name="BKMK_examples"></a>Examples
The following example sets the priority for the job named *myDownloadJob* to normal.
```
C:\>bitsadmin /SetPriority myDownloadJob NORMAL
```
## Additional references
[Command-Line Syntax Key](Command-Line-Syntax-Key.md)