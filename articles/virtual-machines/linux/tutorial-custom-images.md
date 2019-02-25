---
title: 教程 - 使用 Azure CLI 创建自定义 VM 映像 | Azure
description: 本教程介绍如何使用 Azure CLI 在 Azure 中创建自定义虚拟机映像
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 12/13/2017
ms.date: 02/18/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 7e37fa4fc2a4b9b04e4429528c68b4a11c0c8f3a
ms.sourcegitcommit: dd6cee8483c02c18fd46417d5d3bcc2cfdaf7db4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56666036"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>教程：使用 Azure CLI 创建 Azure VM 的自定义映像

自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 在本教程中，你将创建自己的 Azure 虚拟机自定义映像。 你将学习如何执行以下操作：

> [!div class="checklist"]
> * 取消预配和通用化 VM
> * 创建自定义映像
> * 从自定义映像创建 VM
> * 列出订阅中的所有映像
> * 删除映像

[!INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>准备阶段

下列步骤详细说明如何将现有 VM 转换为可重用自定义映像，以便将其用于创建新 VM 实例。

若要完成本教程中的示例，必须现有一个虚拟机。 如果需要，此[脚本示例](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md)可为你创建一个虚拟机。 按照教程进行操作时，请根据需要替换资源组和 VM 名称。

## <a name="create-a-custom-image"></a>创建自定义映像

若要创建虚拟机的映像，需通过以下方式准备 VM：取消源 VM 的预配，解除其分配，然后将其标记为通用化。 准备好 VM 后，可以创建映像。

### <a name="deprovision-the-vm"></a>取消预配 VM 

取消预配可通过删除特定于计算机的信息来通用化 VM。 实现此通用化后，即可从单个映像部署多个 VM。 在取消预配期间，主机名将重置为“localhost.localdomain”。 还会删除 SSH 主机密钥、名称服务器配置、根密码和缓存的 DHCP 租约。

若要取消预配 VM，请使用 Azure VM 代理 (waagent)。 Azure VM 代理安装在 VM 上，用于管理预配及其与 Azure 结构控制器的交互。 有关详细信息，请参阅 [Azure Linux 代理用户指南](../extensions/agent-linux.md)。

使用 SSH 连接到 VM 并运行命令以取消预配 VM。 使用 `+user` 参数还会删除上次预配的用户帐户以及任何关联的数据。 将示例 IP 地址替换为 VM 的公共 IP 地址。

通过 SSH 连接到 VM。
```bash
ssh azureuser@52.174.34.95
```
取消预配 VM。

```bash
sudo waagent -deprovision+user -force
```
关闭 SSH 会话。

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>解除分配 VM 并将其标记为通用化

若要创建映像，需要解除分配 VM。 使用 [az vm deallocate](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-deallocate) 解除分配 VM。 

```azurecli 
az vm deallocate --resource-group myResourceGroup --name myVM
```

最后，使用 [az vm generalize](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-generalize) 将 VM 的状态设置为“通用化”，以便 Azure 平台知道 VM 已通用化。 只能从通用化 VM 创建映像。

```azurecli 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>创建映像

现在，可使用 [az image create](https://docs.azure.cn/zh-cn/cli/image?view=azure-cli-latest#az-image-create) 创建 VM 的映像。 以下示例从名为 myVM 的 VM 创建名为 myImage 的映像。

```azurecli 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```

## <a name="create-vms-from-the-image"></a>从映像创建 VM

现在，你已有了一个映像，可以使用 [az vm create](https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-create) 从该映像创建一个或多个新 VM。 以下示例从名为 myImage 的映像创建名为 myVMfromImage 的 VM。

```azurecli 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>映像管理 

下面是一些常见映像管理任务的示例，说明了如何使用 Azure CLI 完成这些任务。

以表格格式按名称列出所有映像。

```azurecli 
az image list \
    --resource-group myResourceGroup
```

删除映像。 此示例将从 myResourceGroup 中删除名为 myOldImage 的映像。

```azurecli 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建了一个自定义 VM 映像。 你已了解如何：

> [!div class="checklist"]
> * 预配和通用化 VM
> * 创建自定义映像
> * 从自定义映像创建 VM
> * 列出订阅中的所有映像
> * 删除映像

请转到下一教程，了解高度可用的虚拟机。

> [!div class="nextstepaction"]
> [创建高度可用的 VM](tutorial-availability-sets.md)。

<!--Update_Description: update link, wording update -->