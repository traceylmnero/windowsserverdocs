---
title: Showmount
ms.custom: na
ms.prod: windows-server-2012
ms.reviewer: na
ms.suite: na
ms.technology: 
  - techgroup-storage
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: a6dd562e-e3bd-4ee6-be3b-6d29e29fd20e JasonGerend
---
# Showmount
You can use **showmount** to display mounted directories.  
  
## Syntax  
**showmount** `{`\-e `|` \-a `|` \-d`} [`*Server*`]`  
  
## Description  
The **showmount** command\-line utility displays information about mounted file systems exported by Server for NFS on the computer specified by *Server*. If *Server* is not provided, **showmount** displays information about the computer on which the **showmount** command is run.  
  
You must provide one of the following options:  
  
**\-e**  
Displays all file systems exported on the server.  
  
**\-a**  
Displays all Network File System \(NFS\) clients and the directories on the server each has mounted.  
  
**\-d**  
Displays all directories on the server that are currently mounted by NFS clients.  
  
## See Also  
[Services for Network File System Command Reference](Services-for-Network-File-System-Command-Reference.md)  
  
