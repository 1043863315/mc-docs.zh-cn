---
title: 创建用于连接到 Azure 存储的 Azure Function | Microsoft Docs
description: Azure CLI 脚本示例 - 创建用于连接到 Azure 存储的 Azure Function
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
origin.date: 04/20/2017
ms.date: 04/26/2019
ms.author: v-junlch
ms.custom: mvc
ms.openlocfilehash: 016e3e68bc16db76e7144961292c0030438d151a
ms.sourcegitcommit: 5fc46672ae90b6598130069f10efeeb634e9a5af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2019
ms.locfileid: "67236518"
---
# <a name="create-a-function-app-that-connects-to-an-azure-storage-account"></a>创建一个可连接到 Azure 存储帐户的函数应用

此 Azure Functions 示例脚本先创建一个函数应用，然后将该函数连接到 Azure 存储帐户。 创建的应用设置（包含连接）可以与[存储触发器或绑定](../functions-bindings-storage-blob.md)配合使用。 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

如果在本地使用 CLI，请确保运行 Azure CLI 2.0 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此示例创建 Azure Function app，并将存储连接字符串添加到应用设置。

```azurecli
#!/bin/bash

# create a resource group with location
az group create `
  --name myResourceGroup `
  --location chinanorth

# create a storage account 
az storage account create `
  --name myfuncstore `
  --location chinanorth `
  --resource-group myResourceGroup `
  --sku Standard_LRS

# Create an App Service plan
az appservice plan create `
  --name myappserviceplan `
  --resource-group myResourceGroup `
  --location chinanorth

# create a new function app, assign it to the resource group you have just created
az functionapp create `
  --name myfuncstorage  `
  --resource-group myResourceGroup `
  --storage-account myfuncstore `
  --plan myappserviceplan

# Retreive the Storage Account connection string 
$connstr=az storage account show-connection-string --name myfuncstore --resource-group myResourceGroup --query connectionString --output tsv

# update function app settings to connect to storage account
az functionapp config appsettings set `
  --name myfuncstorage `
  --resource-group myResourceGroup `
  --settings StorageConStr=$connstr
```


## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，请运行以下命令删除资源组和所有相关资源：

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](/cli/group#az-group-create) | 使用相关位置创建资源组。 |
| [az storage account create](/cli/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | 在无服务器[消耗计划](../functions-scale.md#consumption-plan)中创建函数应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。


<!-- Update_Description: wording update -->