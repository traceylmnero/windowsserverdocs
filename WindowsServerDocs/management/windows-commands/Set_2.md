---
title: Set_2
ms.custom: na
ms.prod: windows-server-2012
ms.reviewer: na
ms.suite: na
ms.technology: 
  - techgroup-storage
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: acf24663-1a50-4321-b48d-1717655e9476 JasonGerend
---
# Set_2
Sets the context, options, verbose mode, and metadata file for shadow copy creation. If used without parameters, **set** lists all current settings.  
  
## Syntax  
  
```  
set  
set context {clientaccessible | persistent [nowriters] | volatile [nowriters]}  
set option {[differential | plex] [transportable] [[rollbackrecover] [txfrecover] | [noautorecover]]}  
set verbose {on|off}  
set metadata <MetaData.cab>  
```  
  
## Set sub\-commands  
  
|Sub\-command|Description|  
|----------------|---------------|  
|context|Sets the context for shadow copy creation. See [Set context](Set-context.md) for syntax and parameters.|  
|option|Sets options for shadow copy creation. See [Set option](Set-option.md) for syntax and parameters.|  
|verbose|Turns the verbose output mode on or off. See [Set verbose](Set-verbose.md) for syntax and parameters.|  
|metadata|Sets the name and location of the shadow creation metadata file. See [Set metadata](Set-metadata.md) for syntax and parameters.|  
|\/?|Displays help at the command prompt.|  
  
#### Additional references  
[Command-Line Syntax Key](Command-Line-Syntax-Key.md)  
  
