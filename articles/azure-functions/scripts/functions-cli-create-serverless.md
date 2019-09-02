---
title: Azure CLI 脚本示例 - 创建可无服务器执行的 Function App | Microsoft Docs
description: Azure CLI 脚本示例 - 创建可无服务器执行的 Function App
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
origin.date: 07/03/2018
ms.date: 04/26/2019
ms.author: v-junlch
ms.custom: mvc
ms.openlocfilehash: 8f71094b01e47684299adede440ebb17115be302
ms.sourcegitcommit: 05aa4e4870839a3145c1a3835b88cf5279ea9b32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64529977"
---
# <a name="create-a-function-app-for-serverless-code-execution"></a>创建适合无服务器代码执行的函数应用

此 Azure Functions 示例脚本将创建一个函数应用，作为函数的容器。 将使用[消耗计划](../functions-scale.md#consumption-plan)创建最适合事件驱动无服务器工作负荷的函数应用。

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此脚本使用[消耗计划](../functions-scale.md#consumption-plan)创建 Azure Function app。

```cli
#!/bin/bash

# Function app and storage account names must be unique.
storageName=mystorageaccount$RANDOM
functionAppName=myserverlessfunc$RANDOM

# Create a resource group.
az group create --name myResourceGroup --location chinanorth

# Create an Azure storage account in the resource group.
az storage account create `
  --name $storageName `
  --location chinanorth `
  --resource-group myResourceGroup `
  --sku Standard_LRS

# Create a serverless function app in the resource group.
az functionapp create `
  --name $functionAppName `
  --storage-account $storageName `
  --consumption-plan-location chinanorth `
  --resource-group myResourceGroup
```

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 注释 |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/storage/account#az-storage-account-create) | 创建 Azure 存储帐户。 |
| [az functionapp create](/cli/functionapp#az-functionapp-create) | 创建 Function App。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。

