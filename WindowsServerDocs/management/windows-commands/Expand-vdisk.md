---
title: Expand vdisk
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - techgroup-storage
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 3ae547b4-3813-4b86-bacd-bc273c028a2a
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Expand vdisk
Expands a virtual hard disk (VHD) to the size that you specify.
> [!NOTE]
> This command is only applicable to Windows 7 and Windows Server 2008 R2.
## Syntax
```
expand vdisk maximum=<n>
```
## Parameters
|Parameter|Description|
|-------------|---------------|
|maximum=<n>|Specifies the new size for the VHD in megabytes (MB).|
## Remarks
-   A VHD must be selected and detached for this operation to succeed. Use the **select vdisk** command to select a volume and shift the focus to it.
## <a name="BKMK_Examples"></a>Examples
To expand the selected VHD to 20 GB, type:
```
expand vdisk maximum=20000
```
## Additional references
-   [Command-Line Syntax Key](Command-Line-Syntax-Key.md)
-   [Attach vdisk](Attach-vdisk.md)
-   [Compact vdisk](Compact-vdisk.md)
-   [Create vdisk](assetId:///72df30b1-8902-487b-98f6-bcb693610e29)
-   [Detach vdisk](Detach-vdisk.md)
-   [Detail vdisk](Detail-vdisk.md)
-   [Merge vdisk](Merge-vdisk.md)
-   [Select vdisk](Select-vdisk.md)
-   [List_1](List_1.md)