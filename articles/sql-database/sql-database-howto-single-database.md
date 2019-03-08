---
title: 如何配置 Azure SQL 数据库 - 单一 | Microsoft Docs
description: 了解如何配置和管理 Azure SQL 数据库 - 单一数据库
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: WenJason
ms.author: v-jay
ms.reviewer: carlr
manager: digimobile
origin.date: 02/08/2019
ms.date: 03/11/2019
ms.openlocfilehash: 6f6d732ba183b51e738d470b36d8992d2ea66252
ms.sourcegitcommit: 0ccbf718e90bc4e374df83b1460585d3b17239ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57347195"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>如何在 Azure SQL 数据库中使用单一数据库

本部分提供可帮助你在 Azure SQL 数据库中管理和配置单一数据库的各种指南、脚本和说明

## <a name="migrate"></a>迁移

- [迁移到 SQL 数据库](sql-database-single-database-migrate.md) - 了解建议使用的迁移过程和迁移工具。
- 了解如何[在迁移后管理 SQL 数据库](sql-database-manage-after-migration.md)。

## <a name="configure-features"></a>配置功能

- [配置事务性复制](replication-to-sql-database.md)以在数据库之间复制数据。
- [配置威胁检测](sql-database-threat-detection.md)来让 Azure SQL 数据库识别可疑活动，例如 SQL 注入或来自可疑位置的访问。
- [配置动态数据掩码](sql-database-dynamic-data-masking-get-started-portal.md)来保护敏感数据。
- 为数据库[配置备份保留](sql-database-long-term-backup-retention-configure.md)以在 Azure Blob 存储上保留备份。 作为替代方法，还提供了[使用 Azure 保管库配置备份保留（已弃用）](sql-database-long-term-backup-retention-configure-vault.md)方法。
- [配置异地复制](sql-database-geo-replication-portal.md)以将数据库副本保留在另一个区域中。
- [为异地副本配置安全性](sql-database-geo-replication-security-config.md)。

## <a name="monitor-and-tune-your-database"></a>监视和优化数据库

- [启用自动优化](sql-database-automatic-tuning-enable.md)来让 Azure SQL 数据库优化工作负荷的性能。
- [应用性能建议](sql-database-advisor-portal.md)并优化数据库。
- [创建警报](sql-database-insights-alerts-portal.md)以从 Azure SQL 数据库获取通知。
- [排查连接问题](sql-database-troubleshoot-common-connection-issues.md)：如果发现应用程序与数据库之间存在某些连接问题。 还可以[使用“资源运行状况”来排查连接问题](sql-database-resource-health.md)。
- [管理文件空间](sql-database-file-space-management.md)来监视数据库中的存储使用情况。

## <a name="query-distributed-data"></a>查询分布式数据

- 跨多个数据库[查询垂直分区的数据](sql-database-elastic-query-getting-started-vertical.md)。
- [跨横向扩展的数据层进行报告](sql-database-elastic-query-horizontal-partitioning.md)。
- [在具有不同架构的表中进行查询](sql-database-elastic-query-vertical-partitioning.md)。

## <a name="elastic-database-jobs"></a>弹性数据库作业

- 使用 PowerShell [创建和管理](elastic-jobs-powershell.md)弹性数据库作业。
- 使用 Transact-SQL [创建和管理](elastic-jobs-tsql.md)弹性数据库作业。

## <a name="database-sharding"></a>数据库分片

- [升级弹性数据库客户端库](sql-database-elastic-scale-upgrade-client-library.md)。
- [创建分片应用](sql-database-elastic-scale-get-started.md)。
- [查询水平分片的数据](sql-database-elastic-query-getting-started.md)。
- 运行[多分片查询](sql-database-elastic-scale-multishard-querying.md)。
- [移动分片数据](sql-database-elastic-scale-configure-deploy-split-and-merge.md)。
- 在数据库分片中[配置安全性](sql-database-elastic-scale-split-merge-security-configuration.md)。
- 向当前的数据库分片集[添加分片](sql-database-elastic-scale-add-a-shard.md)。
- [解决分片映射问题](sql-database-elastic-database-recovery-manager.md)。
- [迁移分片 DB](sql-database-elastic-convert-to-use-elastic-tools.md)。
- [创建计数器](sql-database-elastic-database-perf-counters.md)。
- [使用实体框架](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)查询分片的数据。
- [使用 Dapper 框架](sql-database-elastic-scale-working-with-dapper.md)查询分片的数据。

