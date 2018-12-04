---
title: Azure Cosmos DB 对 MongoDB 的功能支持 | Azure
description: 了解 Azure Cosmos DB MongoDB API 为 MongoDB 3.4 提供的功能支持。
services: cosmos-db
author: rockboyfor
manager: digimobile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: overview
origin.date: 11/15/2017
ms.date: 09/30/2018
ms.author: v-yeche
ms.openlocfilehash: 7d81539090b106350c2e336589af90c963a39906
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52666855"
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>MongoDB API 对 MongoDB 功能和语法的支持

Azure Cosmos DB 是 21Vianet 提供的多区域分布式多模型数据库服务。 可通过任何开源 MongoDB 客户端[驱动程序](https://docs.mongodb.org/ecosystem/drivers)与数据库的 MongoDB API 通信。 MongoDB API 允许按照 MongoDB [线路协议](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)使用现有的客户端驱动程序。
<!-- Notice: globally to multiple-region -->

通过使用 Azure Cosmos DB MongoDB API，可以像以往一样从 MongoDB API 中受益，并且可使用 Azure Cosmos DB 提供的所有企业功能：[多区域分发](distribute-data-globally.md)、[自动分片](partition-data.md)、可用性和延迟保证、自动编制每个字段的索引、静态加密和备份等。

## <a name="mongodb-protocol-support"></a>MongoDB 协议支持

默认情况下，Azure Cosmos DB MongoDB API 兼容 MongoDB Server 版本 **3.2**。 支持的运算符以及限制或例外已列在下面。 在 MongoDB 版本 **3.4** 中添加的功能或查询运算符目前以预览版功能形式提供。 任何理解这些协议的客户端驱动程序应该都可以使用 MongoDB API 连接到 Cosmos DB。

[MongoDB 聚合管道](#aggregation-pipeline)目前也以单独的预览版功能形式提供。

## <a name="mongodb-query-language-support"></a>MongoDB 查询语言支持

Azure Cosmos DB MongoDB API 为 MongoDB 查询语言构造提供全面的支持。 可以在下面查找当前支持的操作、运算符、阶段、命令和选项的详细列表。

## <a name="database-commands"></a>数据库命令

在所有 MongoDB API 帐户上，Azure Cosmos DB 都支持以下数据库命令。

### <a name="query-and-write-operation-commands"></a>查询和写入操作命令
- 删除
- 查找项
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>身份验证命令
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>管理命令
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>诊断命令
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>聚合管道</a>

Azure Cosmos DB 在公共预览版中支持聚合管道。 请参阅 [Azure 博客](https://aka.ms/mongodb-aggregation)，了解如何载入到公共预览版。

### <a name="aggregation-commands"></a>聚合命令
- aggregate
- 计数
- distinct

### <a name="aggregation-stages"></a>聚合阶段
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>聚合表达式

#### <a name="boolean-expressions"></a>布尔表达式
- $and
- $or
- $not

#### <a name="set-expressions"></a>设置表达式
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>比较表达式
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>算术表达式
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>字符串表达式
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>数组表达式
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>日期表达式
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>条件表达式
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>聚合累加器
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>运算符

以下运算符可以在相应的示例中使用。 考虑一下在下面的查询中使用的此示例文档：

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

运算符 | 示例 |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>注释

在 $regex 查询中，左定位表达式允许索引搜索。 但是，使用“i”修饰符（不区分大小写）和“m”修饰符（多行）会导致在所有表达式中进行集合扫描。
需要包括“$”或“|”时，最好是创建两个（或两个以上）正则表达式查询。 例如，如果原始查询为 ```find({x:{$regex: /^abc$/})```，则必须将其修改为 ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```。
第一部分会使用索引将搜索限制为以 ^abc 开头的那些文档，第二部分会匹配确切的条目。 竖条运算符“|”充当“or”函数 - 查询 ```find({x:{$regex: /^abc|^def/})``` 匹配字段“x”的值以“abc”或“def”开头的文档。 若要利用该索引，建议将该查询拆分成两个不同的查询，再通过 $or 运算符联接到一起：```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```。

### <a name="update-operators"></a>更新运算符

#### <a name="field-update-operators"></a>字段更新运算符
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>数组更新运算符
- $addToSet
- $pop
- $pullAll
- $pull（注意：不支持带条件的 $pull）
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>位更新运算符
- $bit

### <a name="geospatial-operators"></a>地理空间运算符

运算符 | 示例 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | 是
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | 是
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | 是
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | 是
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | 是
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | 是
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | 是
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是

## <a name="additional-operators"></a>其他运算符

运算符 | 示例 | 注释 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | 不支持。 改为使用 $regex。

## <a name="unsupported-operators"></a>不支持的运算符

```$where``` 和 ```$eval``` 运算符不受 Azure Cosmos DB 支持。

### <a name="methods"></a>方法

支持以下方法：

#### <a name="cursor-methods"></a>游标方法

方法 | 示例 | 注释 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | 不返回没有排序关键字的文档

## <a name="unique-indexes"></a>唯一索引

Azure Cosmos DB 为默认情况下写入到数据库的文档中的每个字段设置索引。 唯一索引确保一个集合中所有文档的特定字段没有重复值，类似于在默认的“_id”键上保持唯一性的方式。 现在可以在 Azure Cosmos DB 中创建自定义索引，方法是使用 createIndex 命令（包括“unique”约束）。

唯一索引适用于所有 MongoDB API 帐户。

## <a name="time-to-live-ttl"></a>生存时间 (TTL)

Azure Cosmos DB 支持基于文档时间戳的相对生存时间 (TTL)。 可以通过 [Azure 门户](https://portal.azure.cn)为 MongoDB API 集合启用 TTL。

## <a name="user-and-role-management"></a>用户和角色管理

Azure Cosmos 数据库尚不支持用户和角色。 Azure Cosmos DB 支持基于角色的访问控制 (RBAC) 以及读写型和只读型密码/密钥，后者可以通过 [Azure 门户](https://portal.azure.cn)（“连接字符串”页）获取。

## <a name="replication"></a>复制

Azure Cosmos DB 支持在最低层进行自动的本机复制。 此逻辑还可以扩展，实现低延迟的多区域复制。 Azure Cosmos DB 不支持手动复制命令。

## <a name="write-concern"></a>写关注

某些 MongoDB API 支持指定[写关注](https://docs.mongodb.com/manual/reference/write-concern/)，后者指定写入操作期间需要的响应数。 考虑到 Cosmos DB 在后台处理复制的方式，所有写入默认情况下都自动成为仲裁。 由客户端代码指定的任何写关注都会被系统忽略。 有关详细信息，请参阅[使用一致性级别最大化可用性和性能](consistency-levels.md)。

## <a name="sharding"></a>分片

Azure Cosmos DB 支持服务器端自动分片。 Azure Cosmos DB 不支持手动分片命令。

## <a name="next-steps"></a>后续步骤

- 了解如何配合 MongoDB 数据库 API 来[使用 Studio 3T](mongodb-mongochef.md)。
- 了解如何配合 MongoDB 数据库 API 来[使用 Robo 3T](mongodb-robomongo.md)。
- 浏览具有 MongoDB 协议支持的 Azure Cosmos DB [示例](mongodb-samples.md)。

<!-- Update_Description: update meta properties -->