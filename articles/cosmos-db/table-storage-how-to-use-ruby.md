---
title: 如何配合使用 Ruby 和 Azure 表存储 | Azure
description: 使用 Azure 表存储将结构化数据存储在云中。
services: cosmos-db
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
origin.date: 04/05/2018
ms.date: 01/21/2019
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 27cc342af33021f3ec6109f8b0681794b2d49c91
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58627475"
---
# <a name="how-to-use-azure-table-storage-with-ruby"></a>如何配合使用 Ruby 和 Azure 表存储
<!-- Not Available on Azure Cosmos DB Table API -->
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>概述
本指南演示如何使用 Azure 表服务执行常见任务。 示例是采用 Ruby 编写的，并使用了[用于 Ruby 的 Azure 存储表客户端库](https://github.com/azure/azure-storage-ruby/tree/master/table)。 涉及的方案包括创建和删除表、在表中插入和查询条目。
<!-- Not Available on Azure Cosmos DB Table API -->

## <a name="create-an-azure-service-account"></a>创建 Azure 服务帐户
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

<!-- Not Avaiable on ### Create an Azure Cosmos DB Table API account -->

## <a name="add-access-to-storage"></a>添加对存储的访问权限
<!-- Not Available on Azure Cosmos DB -->
若要使用 Azure 存储，必须下载和使用 Ruby Azure 包，其中包括一组便于与表 REST 服务进行通信的库。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 获取该程序包
1. 使用命令行接口，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。
2. 在命令窗口中键入“gem install azure-storage-blob”以安装 gem 和依赖项。

### <a name="import-the-package"></a>导入包
使用常用的文本编辑器将以下内容添加到要在其中使用存储的 Ruby 文件的顶部：

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>添加 Azure 存储连接
Azure 存储模块读取环境变量 AZURE_STORAGE_ACCOUNT 和 AZURE_STORAGE_ACCESS_KEY 以获取连接到 Azure 存储器帐户所需的信息。 如果未设置这些环境变量，则在使用 Azure::Storage::Table::TableService 之前必须通过以下代码指定帐户信息：

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
Azure.config.storage_endpoint_suffix = "core.chinacloudapi.cn"
```
<!-- Add Azure.config.strage_endpoint_suffix configuration -->

从 Azure 门户中的经典或 Resource Manager 存储帐户中获取这些值：

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 导航到要使用的存储帐户。
3. 在右侧的“设置”边栏选项卡中，单击“访问密钥” 。
4. 在显示的“访问密钥”边栏选项卡中，可看到访问密钥 1 和访问密钥 2。 可以使用其中任意一个密钥。
5. 单击复制图标以将密钥复制到剪贴板。

<!-- Not Available on ## Add an Azure Cosmos DB connection -->

## <a name="create-a-table"></a>创建表
使用 Azure::Storage::Table::TableService 对象可以对表和实体进行操作。 要创建表，请使用 create_table() 方法。 以下示例创建表或输出存在的错误。

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>向表中添加条目
若要添加条目，应首先创建定义条目属性的哈希对象。 请注意，必须为每个实体指定 PartitionKey 和 RowKey。 这些值是实体的唯一标识符，并且查询它们比查询其他属性快很多。 Azure 存储使用 **PartitionKey** 将表的实体自动分发到多个存储节点。 具有相同 **PartitionKey** 的条目存储在同一个节点上。 **RowKey** 是条目在其所属分区内的唯一 ID。

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>更新条目
可使用多种方法来更新现有条目：

* **update_entity()：** 通过替换更新现有实体。
* **merge_entity()：** 通过将新属性值合并到现有实体来更新现有实体。
* **insert_or_merge_entity()：** 通过替换现有实体来更新现有实体。 如果不存在条目，则插入新条目：
* **insert_or_replace_entity()：** 通过将新属性值合并到现有实体来更新现有实体。 如果不存在条目，则插入新条目。

以下示例演示使用 update_entity() 更新实体：

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

对于 update_entity() 和 merge_entity()，如果要更新的实体不存在，更新操作会失败。 因此，如果想要存储某个实体而不考虑它是否已存在，则应改用 insert_or_replace_entity() 或 insert_or_merge_entity()。

## <a name="work-with-groups-of-entities"></a>使用实体组
有时，有必要成批地同时提交多项操作以确保通过服务器进行原子处理。 若要完成此操作，首先要创建一个 Batch 对象，然后对 TableService 使用 execute_batch() 方法。 以下示例演示在一个批次中提交 RowKey 为 2 和 3 的两个条目。 请注意，此操作仅适用于具有相同 PartitionKey 的条目。

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>查询条目
若要查询表中的实体，请传递表名称、PartitionKey 和 RowKey使用 get_entity() 方法。

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>查询一组条目
若要查询表中的一组实体，请创建查询哈希对象并使用 query_entities() 方法。 以下示例演示如何获取具有相同 **PartitionKey**的所有条目：

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> 如果结果集太大，一个查询无法全部返回，则返回一个继续标记，可以使用该标记检索后续页面。
>
>

## <a name="query-a-subset-of-entity-properties"></a>查询一部分实体属性
对表的查询可以只检索条目的几个属性。 这种技术称为“投影”，可减少带宽并提高查询性能，尤其适用于大型条目。 请使用 select 子句并传递希望显示给客户端的属性的名称。

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>删除条目
若要删除实体，请使用 delete_entity() 方法。 传入包含该实体的表的名称、实体的 PartitionKey 和 RowKey。

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>删除表
若要删除表，请使用 delete_table() 方法并传入要删除的表的名称。

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>后续步骤

* [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
  <!-- Notice: Remove from Microsoft -->
* [Ruby 开发人员中心](https://docs.azure.cn/zh-cn/develop/ruby)
* [适用于 Ruby 的 Azure 存储表客户端库](https://github.com/azure/azure-storage-ruby/tree/master/table)

<!--Update_Description: update meta properties, wording update, update link -->