---
title: Azure Cosmos DB 查询语言中的 POWER
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 POWER。
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 09/13/2019
ms.date: 10/28/2019
ms.author: v-yeche
ms.custom: query-reference
ms.openlocfilehash: 17cc9d8c123d9de82f542373eacc438bc29c401c
ms.sourcegitcommit: 73f07c008336204bd69b1e0ee188286d0962c1d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72914688"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
 返回指定表达式的指定幂的值。  

## <a name="syntax"></a>语法

```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  

## <a name="arguments"></a>参数

*numeric_expr1*  
  是一个数值表达式。  

*numeric_expr2*  
  是要将 *numeric_expr1* 提升到的幂次。  

## <a name="return-types"></a>返回类型

  返回一个数值表达式。  

## <a name="examples"></a>示例

  以下数字演示了如何求某个数字的 3 次幂（数字的立方）。  

```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  

 下面是结果集。  

```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)

<!--Update_Description: new articles on sql query power  -->
<!--New.date: 10/28/2019-->