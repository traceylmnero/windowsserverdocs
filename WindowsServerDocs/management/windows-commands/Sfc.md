---
title: Sfc
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: c58c25da-e028-42a6-9e10-973484a4b953
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Sfc
Scans and verifies the integrity of all protected system files and replaces incorrect versions with correct versions.
For examples of how to use this command, see [Examples](#BKMK_examples).
## Syntax
```
sfc [/scannow] [/verifyonly] [/scanfile=<file>] [/verifyfile=<file>] [/offwindir=<offline windows directory> /offbootdir=<offline boot directory>]
```
### Parameters
|Parameter|Description|
|-------------|---------------|
|/scannow|Scans the integrity of all protected system files and repairs files with problems when possible.|
|/verifyonly|Scans integrity of all protected system files. No repair operation is performed.|
|/scanfile|Scans integrity of the specified file and repairs the file if problems are detected, when possible.|
|<file>|Specified full path and filename|
|/verifyfile|Verifies the integrity of the specified file. No repair operation is performed.|
|/offwindir|Specifies the location of the offline windows directory, for offline repair.|
|/offbootdir|Specifies the location of the offline boot directory for offline|
|/?|Displays Help at the command prompt.|
## Remarks
-   You must be logged on as a member of the Administrators group to run **sfc.exe**.
-   If **sfc** discovers that a protected file has been overwritten, it retrieves the correct version of the file from the **systemroot\system32\dllcache** folder, and then replaces the incorrect file.
-   There are functional differences between **sfc** on Windows Server® 2003,  Windows Server® 2008 , and  Windows Server® 2008 R2 :
-   For more information about **sfc** on Windows Server 2003, see [article 310747](http://go.microsoft.com/fwlink/?LinkId=227069) in the Microsoft Knowledge Base.
-   For more information about **sfc** on  Windows Server® 2008 , and  Windows Server® 2008 R2 , see [System File Checker](http://go.microsoft.com/fwlink/?LinkId=227071).
## <a name="BKMK_examples"></a>Examples
To verify the **kernel32.dll file**, type:
```
sfc /verifyfile=c:\windows\system32\kernel32.dll
```
To setup offline repair of the **kernel32.dll** file with an offline boot directory set to **d:** and offline windows directory set to **d:\windows**, type:
```
sfc /scanfile=d:\windows\system32\kernel32.dll /offbootdir=d:\ /offwindir=d:\windows
```
## Additional references
-   [Command-Line Syntax Key](Command-Line-Syntax-Key.md)
-   [Command-Line Reference_1](Command-Line-Reference_1.md)