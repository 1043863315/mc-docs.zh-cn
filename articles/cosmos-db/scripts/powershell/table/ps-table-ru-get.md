---
title: Azure PowerShell 脚本 - Azure Cosmos DB 获取吞吐量（RU/秒）- 表 API
description: Azure PowerShell 脚本 - Azure Cosmos DB 获取吞吐量（RU/秒）- 表 API
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
origin.date: 05/18/2019
ms.date: 10/28/2019
ms.author: v-yeche
ms.openlocfilehash: c74c69f82e1b187f83b19b30f0d3d5b7d3cfa722
ms.sourcegitcommit: 73f07c008336204bd69b1e0ee188286d0962c1d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72913250"
---
# <a name="get-throughput-rus-for-a-table-for-azure-cosmos-db---table-api"></a>获取 Azure Cosmos DB 的表的吞吐量（RU/秒）- 表 API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Get RU for an Azure Cosmos Table API table

$apiVersion = "2015-04-08"
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tableName = "table1"
$tableThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/tables/settings"
$tableThroughputResourceName = $accountName + "/table/" + $tableName + "/throughput"

# Get the throughput for the table
Get-AzResource -ResourceType $tableThroughputResourceType `
    -ApiVersion $apiVersion -ResourceGroupName $resourceGroupName `
    -Name $tableThroughputResourceName | Select-Object Properties

```

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
|**Azure 资源**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | 创建资源。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure Cosmos DB PowerShell 脚本](../../../powershell-samples.md)中找到其他 Azure Cosmos DB PowerShell 脚本示例。

<!-- Update_Description: wording update-->
