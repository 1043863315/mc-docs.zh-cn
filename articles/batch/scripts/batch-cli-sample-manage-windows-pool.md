---
title: Azure CLI 脚本示例 - Batch 中的 Windows 池 | Microsoft Docs
description: Azure CLI 脚本示例 - 在 Batch 中创建和管理 Windows 池
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
origin.date: 01/29/2018
ms.date: 09/07/2018
ms.author: v-junlch
ms.openlocfilehash: 213a34986c049460864fbcc1b0dd7d00a628e8cc
ms.sourcegitcommit: d828857e3408e90845c14f0324e6eafa7aacd512
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44068021"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI 示例：在 Azure Batch 中创建和管理 Windows 池

此脚本演示了 Azure Batch 中一些可用于在 Azure CLI 中创建和管理 Windows 计算节点池的命令。 可通过两种方式配置 Windows 池：使用云服务配置，或使用虚拟机配置。 此示例介绍如何使用云服务配置来创建 Windows 池。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/install-azure-cli)。 

## <a name="example-script"></a>示例脚本
```azurecli
#!/bin/bash

# Create a resource group.
az group create --name myResourceGroup --location chinanorth

# Create a Batch account.
az batch account create \
    --resource-group myResourceGroup \
    --name mybatchaccount \
    --location chinanorth

# Authenticate Batch account CLI session.
az batch account login \
    --resource-group myResourceGroup \
    --name mybatchaccount
    --shared-key-auth

# Create a new Windows cloud service platform pool with 3 Standard A1 VMs.
# The pool has a start task that runs a basic shell command. Typically a 
# start task copies application files to the pool nodes.
az batch pool create \
    --id mypool-windows \
    --os-family 4 \
    --target-dedicated 3 \
    --vm-size small \
    --start-task-command-line "cmd /c dir /s" \
    --start-task-wait-for-success \
    --application-package-references myapp

# Add some metadata to the pool.
az batch pool set --pool-id mypool-windows --metadata IsWindows=true VMSize=StandardA1

# Change the pool to enable automatic scaling of compute nodes.
# This autoscale formula specifies that the number of nodes should be adjusted according
# to the number of active tasks, up to a maximum of 10 compute nodes.
az batch pool autoscale enable \
    --pool-id mypool-windows \
    --auto-scale-formula "$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));$TargetDedicated = min(10, $averageActiveTaskCount);"

# Monitor the resizing of the pool.
az batch pool show --pool-id mypool-windows

# Disable autoscaling when we no longer require the pool to automatically scale.
az batch pool autoscale disable \
    --pool-id mypool-windows
```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [az group create](/cli/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az batch account create](/cli/batch/account#az-batch-account-create) | 创建批处理帐户。 |
| [az batch account login](/cli/batch/account#az-batch-account-login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。 |
| [az batch pool create](/cli/batch/pool#az-batch-pool-create) | 创建计算节点池。  |
| [az batch pool set](/cli/batch/pool#az-batch-pool-set) | 更新池的属性。  |
| [az batch pool autoscale enable](/cli/batch/pool/autoscale#az-batch-pool-autoscale-enable) | 对池启用自动缩放并应用公式。  |
| [az batch pool show](/cli/batch/pool#az-batch-pool-show) | 显示池的属性。  |
| [az batch pool autoscale disable](/cli/batch/pool/autoscale#az-batch-pool-autoscale-disable) | 对池禁用自动缩放。 |
| [az group delete](/cli/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。

<!-- Update_Description: link update -->