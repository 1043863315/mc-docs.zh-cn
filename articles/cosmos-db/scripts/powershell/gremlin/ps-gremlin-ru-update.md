---
title: Azure PowerShell 脚本 - Azure Cosmos DB 更新 Gremlin API 的 RU/秒
description: Azure PowerShell 脚本 - Azure Cosmos DB 更新 Gremlin API 的 RU/秒
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
origin.date: 05/18/2019
ms.date: 10/28/2019
ms.author: v-yeche
ms.openlocfilehash: a3956effa44fd6d2999997b00056e927d21b01c2
ms.sourcegitcommit: 73f07c008336204bd69b1e0ee188286d0962c1d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72913273"
---
# <a name="update-rus-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>更新 Azure Cosmos DB 的数据库或图的 RU/秒 - Gremlin API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Update RU for an Azure Cosmos SQL Gremlin API database or graph

$apiVersion = "2015-04-08"
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$databaseThroughputResourceName = $accountName + "/gremlin/" + $databaseName + "/throughput"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$graphName = "graph1"
$graphThroughputResourceName = $accountName + "/gremlin/" + $databaseName + "/" + $graphName + "/throughput"
$graphThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/graphs/settings"
$throughput = 500
$updateResource = "database" # or "graph"

$properties = @{
    "resource"=@{"throughput"=$throughput}
}

if($updateResource -eq "database"){
Set-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion $apiVersion -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName -PropertyObject $properties
}
elseif($updateResource -eq "graph"){
Set-AzResource -ResourceType $graphThroughputResourceType `
    -ApiVersion $apiVersion -ResourceGroupName $resourceGroupName `
    -Name $graphThroughputResourceName -PropertyObject $properties
}
else {
    Write-Host("Must select database or graph")    
}
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

<!--Update_Description: update meta properties -->
