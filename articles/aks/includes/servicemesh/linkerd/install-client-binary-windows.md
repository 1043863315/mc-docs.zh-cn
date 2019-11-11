---
author: rockboyfor
ms.service: container-service
ms.topic: include
origin.date: 10/09/2019
ms.date: 10/28/2019
ms.author: v-yeche
ms.openlocfilehash: 2968b73fbbc0d3b9f5846bcb239bca78bf9ad4b9
ms.sourcegitcommit: 642a4ad454db5631e4d4a43555abd9773cae8891
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426395"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下载并安装 Linkerd linkerd 客户端二进制文件

在 Windows 上基于 PowerShell 的 shell 中，使用 `Invoke-WebRequest` 下载 Linkerd 发行版，如下所示：

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

`linkerd` 客户端二进制文件在客户端计算机上运行，用来与 Linkerd 服务网格交互。 在 Windows 上基于 PowerShell 的 shell 中使用以下命令安装 Linkerd `linkerd` 客户端二进制文件。 这些命令可将 `linkerd` 客户端二进制文件复制到某个 Linkerd 文件夹，然后你就可以通过 `PATH` 将其设置为立即可用（在当前 shell 中）和永久可用（跨 shell 重启）。 不需要提升的（管理员）特权即可运行这些命令，不需重启 shell。

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

<!--Update_Description: new articles on linkerd install client binary windows -->
<!--New.date: 11/04/2019-->