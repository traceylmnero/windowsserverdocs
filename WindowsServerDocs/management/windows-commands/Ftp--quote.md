---
title: Ftp: quote
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 4500a1d3-c091-42c7-a909-f61df7f2e993 vhorne
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Ftp: quote
Sends verbatim arguments to the remote FTP server. A single FTP reply code is returned. For examples of how this command can be used, see [Examples](assetId:///c6d43992-8243-4f0a-8605-3152c8a8fe9a#BKMK_Examples).  
## Syntax  
```  
quote <Argument>[…]  
```  
### Parameters  
|Parameter|Description|  
|-------------|---------------|  
|<Argument>|Specifies the argument to send to the FTP server.|  
## Remarks  
The **quote** command is identical to the **literal** command.  
## <a name="BKMK_Examples"></a>Examples  
Send a **quit** command to the remote FTP server.  
```  
quote quit  
```  
## Additional references  
-   [Ftp: literal_1](Ftp--literal_1.md)  
-   [Command-Line Syntax Key](Command-Line-Syntax-Key.md)  