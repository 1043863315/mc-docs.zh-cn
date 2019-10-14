---
author: aahill
ms.service: cognitive-services
ms.topic: include
origin.date: 06/24/2019
ms.date: 10/11/2019
ms.author: v-tawe
ms.openlocfilehash: 637575304c14b56bfe772ade7e264de36f29a9c7
ms.sourcegitcommit: aea45739ba114a6b069f782074a70e5dded8a490
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72275544"
---
## <a name="configure-an-environment-variable-for-authentication"></a>配置用于身份验证的环境变量

应用程序需要对它们使用的认知服务的访问进行身份验证。 若要进行身份验证，我们建议创建一个环境变量来存储 Azure 资源的密钥。 

获得密钥后，将其写入运行应用程序的本地计算机上的新环境变量。 若要设置环境变量，请打开控制台窗口，并遵照适用于操作系统的说明。 将 `your-key` 替换为资源的密钥之一。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

添加环境变量后，可能需要重启任何正在运行的、需要读取环境变量的程序（包括控制台窗口）。 例如，如果使用 Visual Studio 作为编辑器，请在运行示例之前重启 Visual Studio。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

编辑 .bash_profile，然后添加环境变量：

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。

***