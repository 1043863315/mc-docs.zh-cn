---
title: 模式：策略定义中的逻辑运算符
description: 此 Azure Policy 模式通过示例介绍了如何使用策略定义中的逻辑运算符。
origin.date: 01/31/2020
ms.date: 03/09/2020
ms.author: v-tawe
ms.topic: sample
ms.openlocfilehash: 5a74fb69dfb85038c985a04d7e3ba1d263eab300
ms.sourcegitcommit: 892137d117bcaf9d88aec0eb7ca756fe39613344
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78048861"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy 模式：逻辑运算符

策略定义可以包含多个条件语句。 可能需要每个语句都为 true，或者只需其中一些语句为 true。 为了满足这些需求，语言提供与 **not**、**allOf** 和 **anyOf** 相对应的[逻辑运算符](../concepts/definition-structure.md#logical-operators)。 它们是可选的，可以通过嵌套来创建复杂的方案。

## <a name="sample-1-one-logical-operator"></a>示例 1：一个逻辑运算符

此策略定义会评估 CosmosDB 帐户，看是否配置了自动故障转移和多个写入位置。 如果尚未配置上述项目，则当创建或更新不合规的资源时，[审核](../concepts/effects.md#audit)会触发并创建一个日志条目。

```json
{
 "properties": {
   "mode": "all",
   "displayName": "Audit Automatic Failover for CosmosDB accounts",
   "description": "This policy audits Automatic Failover for CosmosDB accounts",
   "policyRule": {
     "if": {
       "allOf": [{
           "field": "type",
           "equals": "Microsoft.DocumentDB/databaseAccounts"
         },
         {
           "field": "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
           "equals": "false"
         },
         {
           "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
           "equals": "false"
         }
       ]
     },
     "then": {
       "effect": "audit"
     }
   },
   "parameters": {},
   "metadata": {}
 }
}
```

### <a name="sample-1-explanation"></a>示例 1：说明

```json
"policyRule": {
 "if": {
   "allOf": [{
       "field": "type",
       "equals": "Microsoft.DocumentDB/databaseAccounts"
     },
     {
       "field": "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
       "equals": "false"
     },
     {
       "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
       "equals": "false"
     }
   ]
 },
 "then": {
```

**policyRule.if** 块使用单个 **allOf** 来确保所有三个条件都为 true。
仅当所有这些条件的评估结果都为 true 时，才会执行**审核**效果触发器。

## <a name="sample-2-multiple-logical-operators"></a>示例 2：多个逻辑运算符

此策略定义用于评估命名模式的资源。 如果资源不匹配，系统会[拒绝](../concepts/effects.md#deny)它。

```json
{
   "properties": {
       "displayName": "Match multiple name patterns.",
       "description": "Allows one of multiple naming patterns for resources.",
       "mode": "Indexed",
       "policyRule": {
           "if": {
               "allOf": [{
                       "not": {
                           "field": "name",
                           "match": "contoso??????"
                       }
                   },
                   {
                       "not": {
                           "field": "name",
                           "match": "contoso-???-##"
                       }
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
}
```

### <a name="sample-2-explanation"></a>示例 2：说明

```json
"if": {
   "allOf": [{
           "not": {
               "field": "name",
               "match": "contoso??????"
           }
       },
       {
           "not": {
               "field": "name",
               "match": "contoso-???-##"
           }
       }
   ]
},
```

此 **policyRule.if** 块也包含单个 **allOf**，但每个条件都使用 **not** 逻辑运算符进行包装。 系统会先评估 **not** 逻辑运算符中的条件，然后评估该 **not**，以便确定整个子句是 true 还是 false。 如果 **not** 逻辑运算符的评估结果为 true，则会触发策略效果。

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。