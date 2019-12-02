---
title: 预览版数据查询 - Azure 时序见解 | Microsoft Docs
description: 了解 Azure 时序见解预览版数据查询。
author: deepakpalled
ms.author: v-yiso
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
origin.date: 10/21/2019
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: 5377a085bfcb1a1234043b2f49390fc5dd98cd80
ms.sourcegitcommit: 9e92bcf6aa02fc9e7b3a29abadf6b6d1a8ece8c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74389597"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的数据查询

Azure 时序见解预览版允许通过公共 Surface API 对存储在环境中的事件和元数据进行数据查询。 这些 API 也用于[时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。

时序见解中提供三个主要 API 类别：

* **环境 API**：这些 API 允许查询时序见解环境本身。 查询示例包括调用方有权访问的环境和环境元数据的列表。
* **时序模型-查询 (TSM-Q) API**：允许对存储在时序模型的环境部分中的元数据执行创建、读取、更新和删除 (CRUD) 操作。 示例包括实例、类型和层次结构。
* **时序查询 (TSQ) API**：允许检索从源提供程序记录的遥测或事件数据，或者通过使用变量的标量和聚合函数存储部分来减少数据。 这些 API 可通过执行操作，对时序数据进行转换、合并和应用计算。

时序见解使用丰富的基于字符串的表述语言[时序表达式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) 来表述计算。

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure 时序见解预览版核心 API

支持以下核心 API。

[![时序查询概述](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>环境 API

提供以下环境 API：

* [获取环境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api)：返回调用方有权访问的环境的列表。
* [获取环境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api)：返回事件时间戳 `$ts` 中事件计数的分布。 此 API 通过返回事件计数（如果存在）来帮助确定时间戳中是否有任何事件。
* [获取事件架构 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api)：返回给定搜索范围的事件架构元数据。 此 API 可帮助检索给定搜索范围的架构中可用的所有元数据和属性。

## <a name="time-series-model-query-tsm-q-apis"></a>时序模型-查询 (TSM-Q) API

提供以下时序模型-查询 API。 这些 API 中的大多数支持批量执行操作，可以在多个时序模型实体上启用批量 CRUD 操作：

* [模型设置 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)：允许对环境的默认类型和模型名称执行 *GET* 和 *PATCH* 操作。
* [类型 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)：允许对时序类型及其关联变量执行 CRUD。
* [层次结构 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)：允许对时序层次结构及其关联的字段路径执行 CRUD。
* [实例 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)：允许对时序实例及其关联的实例字段执行 CRUD。 另外，实例 API 支持以下操作：
  * [搜索](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search)：检索在搜索基于实例属性的时序实例时获得的结果的部分列表。
  * [建议](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest)：搜索并建议在搜索基于实例属性的时序实例时获得的结果的部分列表。

## <a name="time-series-query-tsq-apis"></a>时序查询 (TSQ) API

提供以下时序查询 API。 这些 API 在时序见解中所有支持的多层存储上均可用。 查询 URL 参数用于指定查询应该在其上执行的[存储类型](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)：

* [获取事件 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api)：对于在时序见解中记录的来自源提供程序的事件，允许从其中查询和检索时序见解数据。

* [获取时序 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api)：允许使用网络上记录的数据从捕获的事件中查询和检索时序见解数据。 返回的值基于模型中定义的变量或以内联方式提供的变量。

    >[!NOTE]
    > 即使在模型中指定或以内联方式提供 Aggregation 子句，也会忽略该子句。

  获取时序 API 为每个时间间隔的每个变量返回一个时序值。 时序值是时序见解对查询的输出 JSON 所用的格式。 返回的值基于所提供的时序 ID 和变量集。

* [聚合时序 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api)：允许通过对记录的数据进行采样和聚合，从捕获的事件中查询和检索时序见解数据。

  聚合时序 API 为每个时间间隔的每个变量返回一个时序值。 这些值基于所提供的时序 ID 和变量集。 聚合时序 API 使用存储在时序模型中或以内联方式提供的变量对数据进行聚合或采样，以实现缩减操作。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 时序见解预览版中的[存储和流入量](./time-series-insights-update-storage-ingress.md)。
- 阅读 Azure 时序见解预览版[数据建模](./time-series-insights-update-tsm.md)一文。

- 发现[选择时序 ID 的最佳做法](./time-series-insights-update-how-to-id.md)。
<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
