---
title: Azure Cosmos DB 查询语言中的 LOG10
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 LOG10。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 09/13/2019
ms.date: 10/28/2019
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: 080e1895f3b4f53593e5982769fc017e87a95d53
ms.sourcegitcommit: 73f07c008336204bd69b1e0ee188286d0962c1d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72914701"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 返回指定数值表达式以 10 为底的对数。  

## <a name="syntax"></a>语法

```sql
LOG10 (<numeric_expr>)  
```  

## <a name="arguments"></a>参数

*numeric_expression*  
  是一个数值表达式。  

## <a name="return-types"></a>返回类型

  返回一个数值表达式。  

## <a name="remarks"></a>备注

  LOG10 和 POWER 函数互为反函数。 例如，10 ^ LOG10(n) = n。  

## <a name="examples"></a>示例

  以下示例声明一个变量并返回指定变量 (100) 的 LOG10 值。  

```sql
SELECT LOG10(100) AS log10 
```  

 下面是结果集。  

```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!--Update_Description: new articles on sql query log10  -->
<!--New.date: 10/28/2019-->