---
title: Azure PowerShell 脚本 - Azure Cosmos DB 创建 MongoDB API 数据库和集合
description: Azure PowerShell 脚本 - Azure Cosmos DB 创建 MongoDB API 数据库和集合
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
origin.date: 05/18/2019
ms.date: 10/28/2019
ms.author: v-yeche
ms.openlocfilehash: ea1c71851bb5c38db0196db91d4fc31b116067be
ms.sourcegitcommit: 73f07c008336204bd69b1e0ee188286d0962c1d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72913271"
---
# <a name="create-a-database-and-collection-for-azure-cosmos-db---mongodb-api"></a>为 Azure Cosmos DB 创建数据库和集合 - MongoDB API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Create an Azure Cosmos Account for MongoDB API with multi-master enabled,
# shared database throughput and last writer wins conflict policy with custom resolver path

#generate a random 10 character alphanumeric string to ensure unique resource names
$uniqueId=$(-join ((97..122) + (48..57) | Get-Random -Count 15 | % {[char]$_}))

$apiVersion = "2015-04-08"
$location = "China North 2"
$resourceGroupName = "MyResourceGroup"
$accountName = "mycosmosaccount-$uniqueId" # must be lower case.
$apiType = "MongoDB"
$accountResourceType = "Microsoft.DocumentDb/databaseAccounts"
$databaseName = "database1"
$databaseResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases"
$databaseResourceName = $accountName + "/mongodb/" + $databaseName
$collectionName = "collection2"
$collectionResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/collections"
$collectionResourceName = $accountName + "/mongodb/" + $databaseName + "/" + $collectionName

# Create account
$locations = @(
    @{ "locationName"="China North 2"; "failoverPriority"=0 },
    @{ "locationName"="China East 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType $accountResourceType `
    -ApiVersion $apiVersion -ResourceGroupName $resourceGroupName -Location $location `
    -Kind $apiType -Name $accountName -PropertyObject $accountProperties -Force

# Create database with shared throughput
$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"= 400 }
} 
New-AzResource -ResourceType $databaseResourceType `
    -ApiVersion $apiVersion -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties -Force

# Create Collection with a partition key of user_id and unique key index with 
# last writer wins conflict resolution policy and custom path and a TTL of one week
$collectionProperties = @{
    "resource"=@{
        "id"=$collectionName; 
        "shardKey"= @{ "user_id"="Hash" };
        "indexes"= @(
            @{
                "key"= @{ "keys"=@("user_id", "user_address") };
                "options"= @{ "unique"= "true" }
            };
            @{
                "key"= @{ "keys"=@("_ts") };
                "options"= @{ "expireAfterSeconds"= 604800 }
            }
        );
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins"; 
            "conflictResolutionPath"="myResolutionPath"
        }
    }
} 
New-AzResource -ResourceType $collectionResourceType `
    -ApiVersion $apiVersion -ResourceGroupName $resourceGroupName `
    -Name $collectionResourceName -PropertyObject $collectionProperties -Force

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
