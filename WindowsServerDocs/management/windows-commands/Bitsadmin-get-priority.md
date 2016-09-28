---
title: Bitsadmin get priority
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: ff958003-fcc8-4ad8-a9c7-868a48d9bdeb
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Bitsadmin get priority
Retrieves the priority of the specified job.
## Syntax
```
bitsadmin /GetPriority <Job>
```
## Parameters
|Parameter|Description|
|-------------|---------------|
|Job|The job's display name or GUID|
## Remarks
-   The priority is either FOREGROUND, HIGH, NORMAL, LOW, or UNKNOWN.
## <a name="BKMK_examples"></a>Examples
The following example retrieves the priority for the job named *myDownloadJob*.
```
C:\>bitsadmin /GetPriority myDownloadJob
```
## Additional references
[Command-Line Syntax Key](Command-Line-Syntax-Key.md)