---
title: Telnet: unset
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: da9a0d99-1930-4858-93c7-0e9c3797ee09 vhorne
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Telnet: unset
Turns off previously set options. For examples of how this command can be used, see [Examples](assetId:///c6d43992-8243-4f0a-8605-3152c8a8fe9a#BKMK_Examples).  
## Syntax  
```  
u[nset] {bsasdel | crlf | delasbs | escape | localecho | logging | ntlm} [?]  
```  
### Parameters  
|Parameter|Description|  
|-------------|---------------|  
|bsasdel|Sends **Backspace** as a **Backspace**.|  
|crlf|Sends the **Enter** key as a CR. Also known as line feed mode.|  
|delasbs|Sends **Delete** as **Delete**.|  
|escape|Removes the escape character setting.|  
|localecho|Turns off localecho.|  
|logging|Turns off logging.|  
|ntlm|Turns off NTLM authentication.|  
|?|Displays Help for this command.|  
## <a name="BKMK_Examples"></a>Examples  
Turn off logging.  
```  
u logging  
```  
## Additional references  
-   [Command-Line Syntax Key](Command-Line-Syntax-Key.md)  