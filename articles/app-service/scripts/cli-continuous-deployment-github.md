---
title: Azure CLI 脚本示例 - 从 GitHub 通过持续部署创建应用 | Azure
description: Azure CLI 脚本示例 - 从 GitHub 创建应用并持续部署
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
origin.date: 12/11/2017
ms.date: 11/25/2019
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: 325525d7f68d10bf91ff65efe79887691f00152a
ms.sourcegitcommit: e7dd37e60d0a4a9f458961b6525f99fa0e372c66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555938"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>使用 CLI 从 GitHub 通过持续部署创建应用服务应用

此示例脚本使用其相关资源在应用服务中创建应用，并从 GitHub 存储库设置持续部署。 有关不进行持续部署的 GitHub 部署，请参阅[从 GitHub 创建应用并部署代码](cli-deploy-github.md)。 在此示例中，需要以下项：

* 包含应用程序代码且你对其拥有管理权限的 GitHub 存储库。
* GitHub 帐户的[个人访问令牌(PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

如果选择在本地安装并使用 CLI，则需使用 Azure CLI 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli?view=azure-cli-lastest)。

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

gitrepo=<replace-with-URL-of-your-own-GitHub-repo>
token=<replace-with-a-GitHub-access-token>
webappname=mywebapp$RANDOM

# Create a resource group.
az group create --location chinaeast --name myResourceGroup

# Create an App Service plan in `FREE` tier.
az appservice plan create --name $webappname --resource-group myResourceGroup --sku FREE

# Create a web app.
az webapp create --name $webappname --resource-group myResourceGroup --plan $webappname

# Configure continuous deployment from GitHub. 
# --git-token parameter is required only once per Azure account (Azure remembers token).
az webapp deployment source config --name $webappname --resource-group myResourceGroup \
--repo-url $gitrepo --branch master --git-token $token

# Copy the result of the following command into a browser to see the web app.
echo http://$webappname.chinacloudsites.cn
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [`az group create`](/cli/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/webapp?view=azure-cli-latest#az-webapp-create) | 创建应用服务应用。 |
| [`az webapp deployment source config`](/cli/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | 将应用服务应用与 Git 或 Mercurial 存储库相关联。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/overview?view=azure-cli-lastest)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。