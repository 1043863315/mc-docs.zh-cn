---
author: rockboyfor
ms.service: container-service
ms.topic: include
origin.date: 10/09/2019
ms.date: 10/28/2019
ms.author: v-yeche
ms.openlocfilehash: f57c786b71b7dcf8a232a6a0335a3ceb871a7652
ms.sourcegitcommit: 642a4ad454db5631e4d4a43555abd9773cae8891
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426401"
---
在 Linux 或[适用于 Linux 的 Windows 子系统][install-wsl]或 MacOS 上的基于 bash 的 shell 中，使用 `curl` 下载 Consul Helm 图表版本，如下所示：

```bash
# Specify the Consul Helm chart version that will be leveraged throughout these instructions
CONSUL_HELM_VERSION=0.10.0

curl -sL "https://github.com/hashicorp/consul-helm/archive/v$CONSUL_HELM_VERSION.tar.gz" | tar xz
mv consul-helm-$CONSUL_HELM_VERSION consul-helm
```

<!-- LINKS - external -->

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

<!--Update_Description: new articles on consul download bash -->
<!--New.date: 11/04/2019-->