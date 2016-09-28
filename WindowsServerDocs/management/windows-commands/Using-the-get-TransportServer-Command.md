---
title: Using the get-TransportServer Command
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: de634123-0179-41b2-9c6f-726508130ff5
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Using the get-TransportServer Command
Displays information about a specified Transport Server.
## Syntax
```
WDSUTIL [Options] /Get-TransportServer [/Server:<Server name>] /Show:{Config}
```
## Parameters
|Parameter|Description|
|-------------|---------------|
|[/Server:<Server name>]|Specifies the name of the server. This can be either the NetBIOS name or the fully qualified domain name (FQDN). If no server name is specified, the local server will be used.|
|/Show:{Config}|Returns configuration information about the specified Transport Server.|
## <a name="BKMK_examples"></a>Examples
To view information about the server, type:
```
WDSUTIL /Get-TransportServer /Show:Config
```
To view configuration information, type:
```
WDSUTIL /Get-TransportServer /Server:MyWDSServer /Show:Config
```
#### Additional references
[Command-Line Syntax Key](Command-Line-Syntax-Key.md)
[Using the disable-TransportServer Command](Using-the-disable-TransportServer-Command.md)
[Using the enable-TransportServer Command](Using-the-enable-TransportServer-Command.md)
[Subcommand: set-TransportServer](Subcommand--set-TransportServer.md)
[Subcommand: start-TransportServer](Subcommand--start-TransportServer.md)
[Subcommand: stop-TransportServer](Subcommand--stop-TransportServer.md)