---
title: 使用 PowerShell 创建和管理 Azure Cosmos DB
description: 使用 Azure Powershell 管理 Azure Cosmos DB 帐户、数据库、容器和吞吐量。
author: rockboyfor
ms.service: cosmos-db
ms.topic: sample
origin.date: 08/05/2019
ms.date: 09/09/2019
ms.author: v-yeche
ms.custom: seodec18
ms.openlocfilehash: 703d7e1c22ba5cc0f8ae7a92bc097760a6836c16
ms.sourcegitcommit: 66192c23d7e5bf83d32311ae8fbb83e876e73534
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70254415"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>使用 PowerShell 管理 Azure Cosmos DB SQL API 资源

以下指南介绍了如何使用 Powershell 通过脚本来自动管理 Azure Cosmos DB 资源，其中包括帐户、数据库、容器和吞吐量。 管理 Azure Cosmos DB 是直接使用 AzResource cmdlet 通过 Azure Cosmos DB 资源提供程序进行的。

<!--Not Available on To view all of the properties that can be managed using PowerShell for the Azure Cosmos DB resource provider, see Azure Cosmos DB resource provider schema-->
<!--Not Available on [Azure Cosmos DB resource provider schema](/templates/microsoft.documentdb/allversions)-->

若要对 Azure Cosmos DB 进行跨平台管理，可以使用 [Azure CLI](manage-with-cli.md)、[REST API][rp-rest-api] 或 [Azure 门户](create-sql-api-dotnet.md#create-account)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>入门

请按照[如何安装和配置 Azure PowerShell][powershell-install-configure] 中的说明安装 PowerShell 并在其中登录 Azure 帐户。

* 如果想要执行以下命令而无需用户确认，请向命令追加 `-Force` 标志。
* 以下所有命令都是同步执行的。

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 帐户

以下部分演示如何管理 Azure Cosmos 帐户，包括：

* [创建 Azure Cosmos 帐户](#create-account)
* [更新 Azure Cosmos 帐户](#update-account)
* [列出订阅中的所有 Azure Cosmos 帐户](#list-accounts)
* [获取 Azure Cosmos 帐户](#get-account)
* [删除 Azure Cosmos 帐户](#delete-account)
* [更新 Azure Cosmos 帐户标记](#update-tags)
* [列出 Azure Cosmos 帐户的密钥](#list-keys)
* [重新生成 Azure Cosmos 帐户的密钥](#regenerate-keys)
* [列出 Azure Cosmos 帐户的连接字符串](#list-connection-strings)
* [修改 Azure Cosmos 帐户的故障转移优先级](#modify-failover-priority)

<a name="create-account"></a>
### <a name="create-an-azure-cosmos-account"></a>创建 Azure Cosmos 帐户

此命令创建一个 Azure Cosmos 数据库帐户，该帐户使用[多区域][distribute-data-multiple-regionally]、有限过期[一致性策略](consistency-levels.md)。

```powershell
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "China North"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="China North"; "failoverPriority"=0 },
    @{ "locationName"="China East"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName`：Azure Cosmos 帐户的名称。 必须为小写，接受字母数字和“-”字符，长度必须为 3 到 31 个字符。
* `$location` Azure Cosmos 帐户资源的位置。
* `$locations` 数据库帐户的副本区域。 每个数据库帐户必须有一个故障转移优先级值为 0 的写入区域。
* `$consistencyPolicy`：Azure Cosmos 帐户的默认一致性级别。 有关详细信息，请参阅 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)。
* `$CosmosDBProperties`：传递给 Cosmos DB Azure 资源管理器提供程序的、用于预配帐户的属性值。

可以使用 IP 防火墙以及虚拟网络服务终结点配置 Azure Cosmos 帐户。 若要了解如何为 Azure Cosmos DB 配置 IP 防火墙，请参阅[配置 IP 防火墙](how-to-configure-firewall.md)。  若要详细了解如何为 Azure Cosmos DB 启用服务终结点，请参阅[配置从虚拟网络进行访问的权限](how-to-configure-vnet-service-endpoint.md)。

<a name="list-accounts"></a>
### <a name="list-all-azure-cosmos-accounts-in-a-subscription"></a>列出订阅中的所有 Azure Cosmos 帐户

可以使用此命令列出订阅中的所有 Azure Cosmos 帐户。

```powershell
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

<a name="get-account"></a>
### <a name="get-the-properties-of-an-azure-cosmos-account"></a>获取 Azure Cosmos 帐户的属性

使用此命令可以获取现有 Azure Cosmos 帐户的属性。

```powershell
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

<a name="update-account"></a>
### <a name="update-an-azure-cosmos-account"></a>更新 Azure Cosmos 帐户

此命令可更新 Azure Cosmos 数据库帐户属性。 可更新的属性包括：

* 添加或删除区域
* 更改默认的一致性策略
* 更改 IP 范围筛选器
* 更改虚拟网络配置
* 启用多主数据库

> [!NOTE]
> 此命令可添加和删除区域，但不可使用 `failoverPriority=0` 修改故障转移优先级或更改区域。 若要修改故障转移优先级，请参阅[修改 Azure Cosmos 帐户的故障转移优先级](#modify-failover-priority)。

```powershell
# Get an Azure Cosmos Account (assume it has two regions currently China North 2 and China East 2) and add a third region

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @(
    @{ "locationName"="China North 2"; "failoverPriority"=0 },
    @{ "locationName"="China East 2"; "failoverPriority"=1 },
    @{ "locationName"="China East"; "failoverPriority"=2 }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

<a name="delete-account"></a>
### <a name="delete-an-azure-cosmos-account"></a>删除 Azure Cosmos 帐户

使用此命令可以删除现有的 Azure Cosmos 帐户。

```powershell
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

<a name="update-tags"></a>
### <a name="update-tags-of-an-azure-cosmos-account"></a>更新 Azure Cosmos 帐户的标记

下面的示例介绍如何设置 Azure Cosmos 帐户的 [Azure 资源标记][azure-resource-tags]。

> [!NOTE]
> 通过此将 `-Tags` 标志追加到相应的参数，可将此命令与创建或更新命令组合。

```powershell
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

<a name="list-keys"></a>
### <a name="list-account-keys"></a>列出帐户密钥

创建 Azure Cosmos DB 帐户时，该服务会生成两个主访问密钥，用于访问 Azure Cosmos DB 帐户时的身份验证。 Azure Cosmos DB 提供有两个访问密钥，因此可在不中断 Azure Cosmos DB 帐户连接的情况下重新生成密钥。 还提供用于对只读操作进行身份验证的只读密钥。 有两个读写密钥（主密钥和辅助密钥）和两个只读密钥（主密钥和辅助密钥）。

```powershell
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

<a name="list-connection-strings"></a>
### <a name="list-connection-strings"></a>列出连接字符串

对于 MongoDB 帐户，可以使用以下命令检索将 MongoDB 应用连接到数据库帐户的连接字符串。

```powershell
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

<a name="regenerate-keys"></a>
### <a name="regenerate-account-keys"></a>重新生成帐户密钥

应定期重新生成 Azure Cosmos 帐户的访问密钥，以帮助提高连接安全性。 系统会向帐户分配主要和辅助访问密钥。 这样，在重新生成其中的一个密钥时，客户端仍可以保持访问。 Azure Cosmos 帐户有四种类型的密钥（Primary、Secondary、PrimaryReadonly 和 SecondaryReadonly）

```powershell
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

<a name="modify-failover-priority"></a>
### <a name="modify-failover-priority"></a>修改故障转移优先级

对于多区域数据库帐户，可以更改在主写入副本上发生区域性故障转移的情况下，Cosmos 提升辅助只读副本权限的顺序。 修改 `failoverPriority=0` 还可用于启动灾难恢复演练，以测试灾难恢复规划。

在下面的示例中，假设帐户的当前故障转移优先级为 `China North 2 = 0` 和 `China East 2 = 1`，然后将区域互换。

> [!CAUTION]
> 在 `failoverPriority=0` 的情况下更改 `locationName` 会触发 Azure Cosmos 帐户的手动故障转移。 任何其他的优先级更改不会触发故障转移。

```powershell
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "China North 2" = 0 and "China East 2" = 1

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="China East 2"; "failoverPriority"=0 },
    @{ "locationName"="China North 2"; "failoverPriority"=1 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos 数据库

以下部分演示如何管理 Azure Cosmos 数据库，包括：

* [创建 Azure Cosmos 数据库](#create-db)
* [创建共享吞吐量的 Azure Cosmos 数据库](#create-db-ru)
* [获取 Azure Cosmos 数据库的吞吐量](#get-db-ru)
* [列出帐户中的所有 Azure Cosmos 数据库](#list-db)
* [获取单个 Azure Cosmos 数据库](#get-db)
* [删除 Azure Cosmos 数据库](#delete-db)

<a name="create-db"></a>
### <a name="create-an-azure-cosmos-database"></a>创建 Azure Cosmos 数据库

```powershell
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

<a name="create-db-ru"></a>
### <a name="create-an-azure-cosmos-database-with-shared-throughput"></a>创建使用共享吞吐量的 Azure Cosmos 数据库

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

<a name="get-db-ru"></a>
### <a name="get-the-throughput-of-an-azure-cosmos-database"></a>获取 Azure Cosmos 数据库的吞吐量

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

<a name="list-db"></a>
### <a name="get-all-azure-cosmos-databases-in-an-account"></a>获取帐户中的所有 Azure Cosmos 数据库

```powershell
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

<a name="get-db"></a>
### <a name="get-a-single-azure-cosmos-database"></a>获取单个 Azure Cosmos 数据库

```powershell
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

<a name="delete-db"></a>
### <a name="delete-an-azure-cosmos-database"></a>删除 Azure Cosmos 数据库

```powershell
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos 容器

以下部分演示如何管理 Azure Cosmos 容器，包括：

* [创建 Azure Cosmos 容器](#create-container)
* [使用大分区键创建 Azure Cosmos 容器](#create-container-big-pk)
* [获取 Azure Cosmos 容器的吞吐量](#get-container-ru)
* [创建共享吞吐量的 Azure Cosmos 容器](#create-container-ru)
* [创建使用自定义索引的 Azure Cosmos 容器](#create-container-custom-index)
* [创建禁用索引的 Azure Cosmos 容器](#create-container-no-index)
* [创建使用唯一键和 TTL 的 Azure Cosmos 容器](#create-container-unique-key-ttl)
* [创建使用冲突解决策略的 Azure Cosmos 容器](#create-container-lww)
* [列出数据库中的所有 Azure Cosmos 容器](#list-containers)
* [获取数据库中的单个 Azure Cosmos 容器](#get-container)
* [删除 Azure Cosmos 容器](#delete-container)

<a name="create-container"></a>
### <a name="create-an-azure-cosmos-container"></a>创建 Azure Cosmos 容器

```powershell
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

<a name="create-container-big-pk"></a>
### <a name="create-an-azure-cosmos-container-with-a-large-partition-key-size"></a>创建具有大分区键大小的 Azure Cosmos 容器

```powershell
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

<a name="get-container-ru"></a>
### <a name="get-the-throughput-of-an-azure-cosmos-container"></a>获取 Azure Cosmos 容器的吞吐量

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

<a name="create-container-ru"></a>
### <a name="create-an-azure-cosmos-container-with-shared-throughput"></a>创建使用共享吞吐量的 Azure Cosmos 容器

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

<a name="create-container-custom-index"></a>
### <a name="create-an-azure-cosmos-container-with-custom-index-policy"></a>创建使用自定义索引策略的 Azure Cosmos 容器

```powershell
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

<a name="create-container-no-index"></a>
### <a name="create-an-azure-cosmos-container-with-indexing-turned-off"></a>创建禁用索引的 Azure Cosmos 容器

```powershell
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

<a name="create-container-unique-key-ttl"></a>
### <a name="create-an-azure-cosmos-container-with-unique-key-policy-and-ttl"></a>创建使用唯一键策略和 TTL 的 Azure Cosmos 容器

```powershell
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

<a name="create-container-lww"></a>
### <a name="create-an-azure-cosmos-container-with-conflict-resolution"></a>创建使用冲突解决策略的 Azure Cosmos 容器

若要创建冲突解决策略以使用某个存储过程，请设置 `"mode"="custom"`，并将解决策略路径设置为该存储过程的名称：`"conflictResolutionPath"="myResolverStoredProcedure"`。 若要将所有冲突写入 ConflictsFeed 并分开进行处理，请设置 `"mode"="custom"` 和 `"conflictResolutionPath"=""`

```powershell
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

<a name="list-containers"></a>
### <a name="list-all-azure-cosmos-containers-in-a-database"></a>列出数据库中的所有 Azure Cosmos 容器

```powershell
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

<a name="get-container"></a>
### <a name="get-a-single-azure-cosmos-container-in-a-database"></a>获取数据库中的单个 Azure Cosmos 容器

```powershell
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

<a name="delete-container"></a>
### <a name="delete-an-azure-cosmos-container"></a>删除 Azure Cosmos 容器

```powershell
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>后续步骤

* [所有 PowerShell 示例](powershell-samples.md)
* [如何管理 Azure Cosmos 帐户](how-to-manage-database-account.md)
* [创建 Azure Cosmos 容器](how-to-create-container.md)
* [在 Azure Cosmos DB 中配置生存时间](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell-install-configure
[scaling-multiple-regionally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-multiple-regionally]: distribute-data-globally.md
[azure-resource-groups]: /azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: /azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/

<!--Update_Description: update meta properties, wording update -->
