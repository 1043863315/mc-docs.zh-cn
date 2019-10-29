---
title: Azure Cosmos DB 的 SQL API Python 示例
description: 在 GitHub 上查找用于 Azure Cosmos DB 中常见任务的 Python 示例，包括 CRUD 操作。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
origin.date: 07/23/2019
ms.date: 10/28/2019
ms.author: v-yeche
ms.openlocfilehash: b981c906ff9e99ff0a2d79f3bfaaca1d32ff7aa9
ms.sourcegitcommit: 73f07c008336204bd69b1e0ee188286d0962c1d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72913211"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python 示例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 示例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 示例](sql-api-dotnet-v3sdk-samples.md)
> * [Java 示例](sql-api-java-samples.md)
> * [异步 Java 示例](sql-api-async-java-samples.md)
> * [Node.js 示例](sql-api-nodejs-samples.md)
> * [Python 示例](sql-api-python-samples.md)
> * [Azure 代码示例库](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[azure-documentdb-python](https://github.com/Azure/azure-documentdb-python) GitHub 存储库中包含对 Azure Cosmos DB 资源执行 CRUD 操作和其他常见操作的示例解决方案。 本文提供：

* 每个 Python 示例项目文件中的任务链接。 
* 指向相关的 API 参考内容的链接。

**先决条件**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- 可以[激活 Visual Studio 订阅者权益](https://www.azure.cn/support/legal/offer-rate-plans/)：Visual Studio 订阅每月提供可用来试用付费版 Azure 服务的信用额度。

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

还需要 [Python SDK](sql-api-sdk-python.md)。 

   > [!NOTE]
   > 每个示例都是独立的，自行对自身进行设置并在完成后自行进行清理。 这些示例对 [CosmosClient.CreateContainer](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-) 发出多个调用。 每次调用完成后，你的订阅将按使用一小时计费。 有关 Azure Cosmos DB 计费的详细信息，请参阅 [Azure Cosmos DB 定价](https://www.azure.cn/pricing/details/cosmos-db/)。
   > 
   > 

## <a name="database-examples"></a>数据库示例
[DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) 项目的 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) 文件演示了如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 数据库，请参阅概念文章：[使用数据库、容器和项](databases-containers-items.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建数据库](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createdatabase-database--options-none-) |
| [按 ID 读取数据库](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabase-database-link--options-none-) |
| [列出帐户的数据库](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readdatabases-options-none-) |
| [删除数据库](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>集合示例
[CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) 项目的 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) 文件演示了如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 集合，请参阅概念文章：[使用数据库、容器和项](databases-containers-items.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建集合](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createcontainer-database-link--collection--options-none-) |
| [读取数据库中所有集合的列表](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainers-database-link--options-none-) |
| [按 ID 获取集合](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readcontainer-collection-link--options-none-) |
| [更改集合的吞吐量](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceoffer-offer-link--offer-)|
| [删除集合](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>文档示例
[DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) 项目的 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) 文件演示了如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 文档，请参阅概念文章：[使用数据库、容器和项](databases-containers-items.md)。 

| 任务 | API 参考 |
| --- | --- |
| [创建文档](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [创建文档的集合](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#createitem-database-or-container-link--document--options-none-) |
| [按 ID 读取文档](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitem-document-link--options-none-) |
| [Read all the documents in a collection](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#readitems-collection-link--feed-options-none-) |
| [使用条件 ETag 检查替换文档](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>索引示例
[IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) 项目的 [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) 文件演示了如何执行以下任务。  若要在运行以下示例之前了解如何在 Azure Cosmos DB 中进行索引编制，请参阅概念文章：[索引策略](index-policy.md)、[索引类型](index-types.md)和[索引路径](index-paths.md)。 

| 任务 | API 参考 |
| --- | --- |
| [使用手动（而非自动）索引](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | 自动索引编制策略 |
| [从索引中排除指定的文档路径](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | 基于排除的路径的索引编制策略|
| [从索引中排除文档](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [设置索引模式](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [对字符串使用范围索引](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | 基于包括的路径的索引编制策略|
| [执行索引转换](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>查询示例
示例项目还演示了如何执行以下查询任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的 SQL 查询引用，请参阅概念文章：[SQL 查询示例](how-to-sql-query.md)。 若要在运行以下示例之前了解 Azure Cosmos DB 中的 SQL 查询引用，请参阅概念文章：[SQL 查询示例](how-to-sql-query.md)。 

| 任务 | API 参考 |
| --- | --- |
| [查询数据库帐户](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#querydatabases-query--options-none-) |
| [查询文档](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient?view=azure-python#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [对哈希索引路径强制执行范围扫描操作](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

<!-- Update_Description: update meta properties, wording update -->
