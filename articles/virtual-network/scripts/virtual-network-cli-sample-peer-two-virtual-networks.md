---
title: Azure CLI 脚本示例 - 对等互连两个虚拟网络 | Azure
description: Azure CLI 脚本示例 - 对等互连两个虚拟网络。
services: virtual-network
documentationcenter: virtual-network
author: rockboyfor
manager: digimobile
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
origin.date: 05/07/2018
ms.date: 05/07/2018
ms.author: v-yeche
ms.openlocfilehash: 00c6727172af3dedf1077dd237e060393f2b8747
ms.sourcegitcommit: 0b63440e7722942ee1cdabf5245ca78759012500
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33815487"
---
# <a name="peer-two-virtual-networks-script-sample"></a>将两个虚拟网络脚本示例对等互连

此脚本示例通过 Azure 网络在同一区域创建并连接两个虚拟网络。 运行脚本后，会在两个虚拟网络之间创建对等互连。

可以通过本地 Azure CLI 安装来执行脚本。 如果在本地使用 CLI，此脚本要求运行版本 2.0.28 或更高版本。 要查找已安装的版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。 如果在本地运行 CLI，则还需运行 `az login` 以创建与 Azure 的连接。
<!-- Not Available on [Cloud Shell](https://shell.azure.com/bash) -->

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
#!/bin/bash

RgName="MyResourceGroup"
Location="chinaeast"

# Create a resource group.
az group create \
  --name $RgName \
  --location $Location

# Create virtual network 1.
az network vnet create \
  --name Vnet1 \
  --resource-group $RgName \
  --location $Location \
  --address-prefix 10.0.0.0/16

# Create virtual network 2.
az network vnet create \
  --name Vnet2 \
  --resource-group $RgName \
  --location $Location \
  --address-prefix 10.1.0.0/16

# Get the id for VNet1.
VNet1Id=$(az network vnet show \
  --resource-group $RgName \
  --name Vnet1 \
  --query id --out tsv)

# Get the id for VNet2.
VNet2Id=$(az network vnet show \
  --resource-group $RgName \
  --name Vnet2 \
  --query id \
  --out tsv)

# Peer VNet1 to VNet2.
az network vnet peering create \
  --name LinkVnet1ToVnet2 \
  --resource-group $RgName \
  --vnet-name VNet1 \
  --remote-vnet-id $VNet2Id \
  --allow-vnet-access

# Peer VNet2 to VNet1.
az network vnet peering create \
  --name LinkVnet2ToVnet1 \
  --resource-group $RgName \
  --vnet-name VNet2 \
  --remote-vnet-id $VNet1Id \
  --allow-vnet-access

```

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源：

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 下表中的每条命令均链接到特定于命令的文档：

| 命令 | 注释 |
|---|---|
| [az group create](https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](https://docs.azure.cn/zh-cn/cli/network/vnet?view=azure-cli-latest#az-network-vnet-create) | 创建 Azure 虚拟网络和子网。 |
| [az network vnet peering create](https://docs.azure.cn/zh-cn/cli/network/vnet/peering?view=azure-cli-latest#az-network-vnet-peering-create) | 创建两个虚拟网络之间的对等互连。  |
| [az group delete](https://docs.azure.cn/zh-cn/cli/vm/extension?view=azure-cli-latest#az-vm-extension-set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.azure.cn/zh-cn/cli/overview?view=azure-cli-latest)。
可在[虚拟网络 CLI 示例](../cli-samples.md)中查找其他虚拟网络 CLI 脚本示例。

<!-- Update_Description: new articles on virtual network cli sample peer two virtual networks script -->
<!--ms.date: 05/07/2018-->