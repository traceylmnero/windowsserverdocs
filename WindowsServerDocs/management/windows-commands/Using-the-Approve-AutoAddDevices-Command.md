---
title: Using the Approve-AutoAddDevices Command
ms.custom: na
ms.prod: windows-server-threshold
ms.reviewer: na
ms.suite: na
ms.technology: 
  - management
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 8d76e8d3-ab35-429c-be7b-904f95d0782d
author: coreyp
ms.author: coreyp-at-msft
ms.date: 09/14/2016
---
# Using the Approve-AutoAddDevices Command
Approves computers that are pending administrative approval. When the Auto-Add policy is enabled, administrative approval is required before unknown computers (those that are not prestaged) can install an image. You can enable this policy using the **PXE Response** tab of the server’s properties page.
## Syntax
```
WDSUTIL [Options] /Approve-AutoAddDevices [/Server:<Server name>] /RequestId:{<Request ID>| ALL} [/MachineName:<Device name>] [/OU:<DN of OU>] 
[/User:<Domain\User | User@Domain>] [/JoinRights:{JoinOnly | Full}] [/JoinDomain:{Yes | No}] [/ReferralServer:<Server name>] [/BootProgram:<Relative path>] [/WdsClientUnattend:<Relative path>] [/BootImagePath:<Relative path>]
```
## Parameters
|Parameter|Description|
|-------------|---------------|
|[/Server:<Server name>]|Specifies the name of the server. This can be either the NetBIOS name or the fully qualified domain name (FQDN). If a server name is not specified, the local server will be used.|
|/RequestId:{Request ID &#124; ALL}|Specifies the request ID assigned to the pending computer. Specify **ALL** to approve all pending computers.|
|[/MachineName:<Device name>]|Specifies the name of the computer to be added. You cannot use this option when approving all computers.|
|[/OU:<DN of OU>]|Specifies the distinguished name of the organizational unit (OU) where the computer account object should be created. For example: **OU=MyOU,CN=Test,DC=Domain,DC=com**. The default location is the container of the default computer.|
|[/User:<Domain\User &#124; User@Domain>]|Sets permissions on the computer account object to assign the specified user the necessary rights.|
|[/JoinRights:{JoinOnly &#124; Full}]|Specifies the type of rights to be assigned to the specified user.<br /><br />-   **JoinOnly** requires the administrator to reset the computer account before the user can join the computer to the domain.<br />-   **Full** gives full access to the user, which includes the right to join the computer to the domain.|
|[/JoinDomain:{Yes &#124; No}]|Specifies whether or not the computer should be joined to the domain as this computer account during operating system installation. The default value is **Yes**.|
|[/ReferralServer:<Server name>]|Specifies the name of the server to be contacted to download the network boot program and boot image by using Trivial File Transfer Protocol (TFTP).|
|[/BootProgram:<Relative path>]|Specifies the relative path from the RemoteInstall folder to the network boot program that this computer should receive. For example: **boot\x86\pxeboot.com**.|
|[/WdsClientUnattend:<Relative path>]|Specifies the relative path from the RemoteInstall folder to the unattend file that automates the Windows Deployment Services client.|
|[/BootImagePath:<Relative path>]|Specifies the relative path from the RemoteInstall folder to the boot image that this computer should receive.|
## <a name="BKMK_examples"></a>Examples
To approve the computer with a RequestId of 12, type:
```
WDSUTIL /Approve-AutoAddDevices /RequestId:12
```
To approve the computer with a RequestID of 20 and deploy the image with the specified settings, type:
```
WDSUTIL /Approve-AutoAddDevices /RequestId:20 /MachineName:Computer1 /OU:"OU=Test,CN=Company,DC=Domain,DC=Com" /User:Domain\User1 
/JoinRights:Full /ReferralServer:MyWDSServer /BootProgram:boot\x86\pxeboot.n12 /WdsClientUnattend:WDSClientUnattend\Unattend.xml /BootImagePath:boot\x86\images\boot.wim
```
To approve all pending computers, type:
```
WDSUTIL /Verbose /Approve-AutoAddDevices /RequestId:ALL
```
#### Additional references
[Command-Line Syntax Key](Command-Line-Syntax-Key.md)
[Using the delete-AutoAddDevices Command](Using-the-delete-AutoAddDevices-Command.md)
[Using the get-AutoAddDevices Command](Using-the-get-AutoAddDevices-Command.md)
[Using The Reject-AutoAddDevices Command](Using-The-Reject-AutoAddDevices-Command.md)