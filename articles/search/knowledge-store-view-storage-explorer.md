---
title: 使用存储资源管理器查看知识存储（预览版）
titleSuffix: Azure Cognitive Search
description: 使用 Azure 门户的存储资源管理器查看和分析 Azure 认知搜索知识存储。 知识存储目前为公开预览版。
manager: nitinme
author: HeidiSteen
ms.author: v-tawe
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 12/30/2019
ms.date: 03/16/2020
ms.openlocfilehash: f3d6ae2d2638ccf76fa0a4edd6c37edbb185c991
ms.sourcegitcommit: d5eca3c6b03b206e441b599e5b138bd687a91361
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78934784"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>使用存储资源管理器查看知识存储

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。
> [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

在本文中，你将通过示例来了解如何使用 Azure 门户中的存储资源管理器来连接和浏览知识存储。

## <a name="prerequisites"></a>先决条件

+ 按照[在 Azure 门户中创建知识存储](knowledge-store-create-portal.md)中的步骤创建本演练中使用的示例知识存储。

+ 还需要用于创建知识存储的 Azure 存储帐户的名称，以及从 Azure 门户获得的其访问密钥。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>在存储资源管理器中查看、编辑和查询知识存储

1. 在 Azure 门户中，[打开用于创建知识存储的存储帐户](https://portal.azure.cn/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. 在存储帐户的左侧导航窗格中，单击“存储资源管理器”  。

1. 展开**表**列表以显示对酒店评论示例数据运行“导入数据”  向导时创建的 Azure 表投影列表。

选择任何表可查看已扩充的数据，包括关键短语和情绪评分。

   ![在存储资源管理器中查看表](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "在存储资源管理器中查看表")

若要更改任何表值的数据类型或更改表中的各个值，请单击“编辑”  。 更改一个表行中任何列的数据类型时，它将应用于所有行。

   ![在存储资源管理器中编辑表](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "在存储资源管理器中编辑表")

若要运行查询，请单击命令栏上的“查询”  并输入条件。  

   ![在存储资源管理器中查询表](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "在存储资源管理器中查询表")

## <a name="clean-up"></a>清理

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接   ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

## <a name="next-steps"></a>后续步骤

将此知识存储连接到 Power BI 以进行更深入的分析，或继续编写代码，使用 REST API 和 Postman 创建不同的知识存储。

> [!div class="nextstepaction"]
> [通过 Power BI 进行连接](knowledge-store-connect-power-bi.md)
> [在 REST 中创建知识存储](knowledge-store-create-rest.md)
