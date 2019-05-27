---
title: 有关排查使用 Azure 数据库迁移服务时出现的常见已知问题/错误的文章 | Microsoft Docs
description: 了解有关如何排查使用 Azure 数据库迁移服务时出现的常见已知问题/错误。
services: database-migration
author: WenJason
ms.author: v-jay
manager: digimobile
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
origin.date: 05/09/2019
ms.date: 05/20/2019
ms.openlocfilehash: 89c7c438573ffae2c824e821a8c604cb02109de2
ms.sourcegitcommit: 99ef971eb118e3c86a6c5299c7b4020e215409b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65830273"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>排查常见的 Azure 数据库迁移服务问题和错误

本文描述了 Azure 数据库迁移服务用户可能遇到的一些常见问题和错误。 本文还包含有关如何解决这些问题和错误的信息。

## <a name="migration-activity-in-queued-state"></a>迁移活动处于排队状态

在 Azure 数据库迁移服务项目中创建新活动时，活动保持为排队状态。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果 Azure 数据库迁移服务实例达到正在运行的任务的最大容量，则会出现此问题。 任何新活动将排队到容量可用为止。 | 验证数据迁移服务实例是否在运行各个项目中的活动。 可以继续创建新活动，这些活动会自动添加到队列中等待执行。 任何一个正在运行的现有活动完成后，下一个排队的活动会立即开始运行，其状态会自动更改为正在运行。 无需采取任何额外的措施即可开始迁移排队的活动。<br> |

## <a name="max-number-of-databases-selected-for-migration"></a>选择迁移的数据库数目上限

为要转移到 Azure SQL 数据库的数据库迁移项目创建活动时发生以下错误：

* **错误**：迁移设置验证错误: "errorDetail": 选择迁移的“数据库”对象数目超过最大数目 '4'。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果为单个迁移活动选择的数据库数目超过 4 个，则会显示此错误。 目前，每个迁移活动限制为 4 个数据库。 | 为每个迁移活动选择 4 个或更少的数据库。 如果需要同时迁移 4 个以上数据库，请预配 Azure 数据库迁移服务的另一个实例。 目前，每个订阅最多支持两个 Azure 数据库迁移服务实例。<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>从 MySQL 迁移到 Azure MySQL 出错并发生恢复失败

使用 Azure 数据库迁移服务从 MySQL 迁移到 Azure Database for MySQL 时，迁移活动失败并出现以下错误：

* **错误**：错误：数据库迁移错误 - 由于 [n] 次连续恢复失败，任务 'TaskID' 已挂起。

| 原因         | 解决方法 |
| ------------- | ------------- |
| 当执行迁移的用户缺少 ReplicationAdmin 角色和/或 REPLICATION CLIENT、REPLICATION REPLICA 和 SUPER（低于 MySQL 5.6.6 的版本）的特权时，可能会发生此错误。<br> <br><br><br> <br> <br> <br> <br> <br> <br> | 确保在 Azure MySQL 实例中为用户帐户准确配置[必备特权](/dms/tutorial-mysql-azure-mysql-online#prerequisites)。 例如，可以遵循以下步骤创建具有所需特权的名为“migrateuser”的用户：<br>1.CREATE USER migrateuser@'%' IDENTIFIED BY 'secret'; <br>2. 将针对 db_name.* 的所有特权授予 'secret' 'migrateuser'@'%'；// 重复此步骤以授予针对其他数据库的访问权限 <br>3. 将针对 *.* 的 replication slave 权限授予 'secret' 标识的 'migrateuser'@'%'；<br>4. 将针对 *.* 的 replication client 权限授予 'secret' 标识的 'migrateuser'@'%'；<br>5. 刷新特权。 |

## <a name="error-when-attempting-to-stop-the-azure-database-migration-service-instance"></a>尝试停止 Azure 数据库迁移服务实例时出错

停止 Azure 数据库迁移服务实例时收到以下错误：

* **错误**：服务无法停止。 错误: {'error':{'code':'InvalidRequest','message': '一个或多个活动当前正在运行。 若要停止服务，请等到这些活动完成，或手动停止这些活动，然后重试。'}}

| 原因         | 解决方法 |
| ------------- | ------------- |
| 如果尝试停止的服务实例包含仍在运行的或者存在于迁移项目中的活动，则会显示此错误。 <br><br><br><br><br><br> | 确保尝试停止的 Azure 数据库迁移服务实例中没有任何活动正在运行。 在尝试停止该服务之前，还可以删除活动或项目。 以下步骤演示如何通过删除所有正在运行的任务来删除项目，以清理迁移服务实例：<br>1.Install-Module -Name AzureRM.DataMigration <br>2.Login-AzureRmAccount <br>3.Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4.Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>删除与 Azure 数据库迁移服务关联的 NIC 时出错

尝试删除与 Azure 数据库迁移服务关联的网络接口卡时，该操作失败并出现以下错误：

* **错误**：无法删除与 Azure 数据库迁移服务关联的 NIC，因为 DMS 服务正在使用该 NIC

| 原因         | 解决方法    |
| ------------- | ------------- |
| 如果 Azure 数据库迁移服务实例仍然存在并在使用该 NIC，则会出现此问题。 <br><br><br><br><br><br> | 若要删除此 NIC，请删除 DMS 服务实例，这会自动删除该服务使用的 NIC。<br><br> **重要说明**：确保要删除的 Azure 数据库迁移服务实例中没有任何正在运行的活动。<br><br> 删除与 Azure 数据库迁移服务实例关联的所有项目和活动后，可以删除服务实例。 在删除服务的过程中，会自动清理服务实例使用的 NIC。 |

## <a name="connection-error-when-using-expressroute"></a>使用 ExpressRoute 时出现连接错误

尝试在 Azure 数据库迁移服务项目向导中连接到源时，如果源使用 ExpressRoute 进行连接，在长时间的超时后，连接将会失败。

| 原因         | 解决方法    |
| ------------- | ------------- |
| 使用 [ExpressRoute](/expressroute/) 时，Azure 数据库迁移服务[要求](/dms/tutorial-sql-server-azure-sql-online)在与它关联的虚拟网络子网中预配三个服务终结点：<br> -- 服务总线终结点<br> -- 存储终结点<br> -- 目标数据库终结点（例如 SQL 终结点、Cosmos DB 终结点）<br><br><br><br> | 请[启用](/dms/tutorial-sql-server-azure-sql-online)所需的服务终结点，以便在源与 Azure 数据库迁移服务之间建立 ExpressRoute 连接。 <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-database-for-mysql"></a>将 MySQL 数据库迁移到 Azure Database for MySQL 时发生超时错误

通过 Azure 数据库迁移服务将 MySQL 数据库迁移到 Azure Database for MySQL 实例时，迁移失败并出现以下超时错误：

    * **错误**：错误：数据库迁移错误 - 无法加载文件 - 无法针对文件 'n' RetCode 启动加载进程:SQL_ERROR SqlState:HY000 NativeError:1205 消息: [MySQL][ODBC Driver][mysqld] 锁定等待超时；请尝试重启事务

| 原因         | 解决方法    |
| ------------- | ------------- |
| 之所以在迁移失败时发生此错误，是因为迁移期间发生锁定等待超时。<br><br> | 考虑增大服务器参数 **'innodb_lock_wait_timeout'** 的值。 允许的最大值为 1073741824。 |

## <a name="additional-known-issues"></a>其他已知问题

* [联机迁移到 Azure SQL 数据库时存在的已知问题/迁移限制](/dms/known-issues-azure-sql-online)
* [联机迁移到 Azure DB for MySQL 时存在的已知问题/迁移限制](/dms/known-issues-azure-mysql-online)
* [联机迁移到 Azure DB for PostgreSQL 时存在的已知问题/迁移限制](/dms/known-issues-azure-postgresql-online)

## <a name="additional-resources"></a>其他资源

* [Azure 数据库迁移服务 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)
* [如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置服务器参数](/mysql/howto-server-parameters)
* [使用 Azure 数据库迁移服务的先决条件概述](/dms/pre-reqs)
* [有关使用 Azure 数据库迁移服务的常见问题解答](/dms/faq)
