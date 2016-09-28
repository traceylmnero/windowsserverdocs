---
title: The Verbose Command
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: fcf30ae7-818f-4e7e-a083-a1812682032b
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# The Verbose Command
Displays verbose output for a specified command. You can use **/verbose** with any other WDSUTIL commands that you run. Note that you must specify **/verbose** and **/progress** directly after **WDSUTIL**.
## Syntax
```
WDSUTIL /verbose <commands>
```
## Examples
To delete approved computers from the Auto-Add database and show verbose output, type:
```
WDSUTIL /Verbose /progress /Delete-AutoAddDevices /Server:MyWDSServer /DeviceType:ApprovedDevices
```