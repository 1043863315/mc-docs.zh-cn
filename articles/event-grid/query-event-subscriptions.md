---
title: 查询 Azure 事件网格订阅
description: 介绍了如何列出 Azure 事件网格订阅。
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
origin.date: 01/04/2019
ms.date: 06/03/2019
ms.author: v-yiso
ms.openlocfilehash: 5e998bd1884ab6f5299b495a76692bf4d5534e04
ms.sourcegitcommit: 5a57f99d978b78c1986c251724b1b04178c12d8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66195069"
---
# <a name="query-event-grid-subscriptions"></a>查询事件网格订阅 

本文介绍了如何列出 Azure 订阅中的事件网格订阅。 在查询现有事件网格订阅时，了解订阅的各种类型非常重要。 你需要根据要获取的订阅的类型提供不同的参数。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>资源组和 Azure 订阅

Azure 订阅和资源组不是 Azure 资源。 因此，对资源组或 Azure 订阅的事件网格订阅不具有与对 Azure 资源的事件网格订阅相同的属性。 对资源组或 Azure 订阅的事件网格订阅被视为全局的。

若要获取对 Azure 订阅及其资源组的事件网格订阅，不需要提供任何参数。 请确保已选择要查询的 Azure 订阅。 以下示例不会获取对自定义主题或 Azure 资源的事件网格订阅。

对于 Azure CLI，请使用：

```azurecli
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

对于 PowerShell，请使用：

```azurepowershell
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

若要获取对 Azure 订阅的事件网格订阅，请提供主题类型 **Microsoft.Resources.Subscriptions**。

对于 Azure CLI，请使用：

```azurecli
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

对于 PowerShell，请使用：

```azurepowershell
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

若要获取对 Azure 订阅内的所有资源组的事件网格订阅，请提供主题类型 **Microsoft.Resources.ResourceGroups**。

对于 Azure CLI，请使用：

```azurecli
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

对于 PowerShell，请使用：

```azurepowershell
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

若要获取对特定资源组的事件网格订阅，请将资源组的名称作为参数提供。

对于 Azure CLI，请使用：

```azurecli
az eventgrid event-subscription list --resource-group myResourceGroup
```

对于 PowerShell，请使用：

```azurepowershell
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>自定义主题和 Azure 资源

事件网格自定义主题是 Azure 资源。 因此，可以采用相同的方式查询对自定义主题和其他资源（例如 Blob 存储帐户）的事件网格订阅。 若要获取对自定义主题的事件网格订阅，必须提供用来标识资源或标识资源位置的参数。 不能在整个 Azure 订阅中广泛地查询对资源的事件网格订阅。

若要获取对某个位置中的自定义主题和其他资源的事件网格订阅，请提供该位置的名称。

对于 Azure CLI，请使用：

```azurecli
az eventgrid event-subscription list --location chinaeast
```

对于 PowerShell，请使用：

```azurepowershell
Get-AzEventGridSubscription -Location chinaeast
```

若要为某个位置获取对自定义主题的订阅，请提供该位置和主题类型 **Microsoft.EventGrid.Topics**。

对于 Azure CLI，请使用：

```azurecli
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "chinaeast"
```

对于 PowerShell，请使用：

```azurepowershell
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location chinaeast
```

若要为某个位置获取对存储帐户的订阅，请提供该位置和主题类型 **Microsoft.Storage.StorageAccounts**。

对于 Azure CLI，请使用：

```azurecli
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location chinaeast
```

对于 PowerShell，请使用：

```azurepowershell
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location chinaeast
```

若要获取对某个自定义主题的事件网格订阅，请提供该自定义主题的名称及其资源组的名称。

对于 Azure CLI，请使用：

```azurecli
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

对于 PowerShell，请使用：

```azurepowershell
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

若要获取对特定资源的事件网格订阅，请提供资源 ID。

对于 Azure CLI，请使用：

```azurecli
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

对于 PowerShell，请使用：

```azurepowershell
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>后续步骤

* 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
