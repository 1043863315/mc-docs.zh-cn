---
title: SQL 数据库托管实例常见问题解答 | Microsoft Docs
description: SQL 数据库托管实例常见问题解答 (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein, carlrab
manager: digimobile
origin.date: 07/08/2019
ms.date: 08/19/2019
ms.openlocfilehash: 02d75abad2956e38c1df55d7b222bb25b6bae514
ms.sourcegitcommit: 52ce0d62ea704b5dd968885523d54a36d5787f2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544857"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL 数据库托管实例常见问题解答 (FAQ)

本文包含有关 [SQL 数据库托管实例](sql-database-managed-instance.md)的多种最常见问题的解答。

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>在何处可以找到托管实例支持的功能列表？

有关托管实例支持的功能列表，请参阅 [Azure SQL 数据库与 SQL Server](sql-database-features.md)。

有关 Azure SQL 数据库托管实例与本地 SQL Server 之间的语法和行为差异，请参阅 [T-SQL 与 SQL Server 之间的差异](sql-database-managed-instance-transact-sql-information.md)。


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>在何处可以找到托管实例的技术特征和资源限制？

有关可用硬件的代系特征，请参阅[硬件代系的技术差异](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
有关可用的服务层级及其特征，请参阅[服务层级之间的技术差异](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="where-can-i-find-known-issues-and-bugs"></a>在何处可以找到已知问题和 bug？

有关 bug 和已知问题，请参阅[行为变更](sql-database-managed-instance-transact-sql-information.md#Changes)和[已知问题](sql-database-managed-instance-transact-sql-information.md#Issues)。


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>托管实例是否可与本地 SQL Server 同名？

托管实例的名称必须以 *database.chinacloudapi.cn* 结尾。 若要使用其他 DNS 区域而不是默认区域，例如 **mi-another-name**.contoso.com： 
- 请使用 CliConfig 定义别名（该工具只是一个注册表设置包装器，因此也可以使用组策略或脚本完成此操作）。
- 将 *CNAME* 与 *TrustServerCertificate=true* 选项一起使用。


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>如何将数据库从托管实例移回到 SQL Server 或 Azure SQL 数据库？

可[将数据库导出到 bacpac](sql-database-export.md)，然后[导入 bacpac 文件]( sql-database-import.md)。 如果数据库小于 100 GB，我们建议使用此方法。

如果数据库中的所有表具有主键，则可以使用事务复制。

由于托管实例的数据库版本高于 SQL Server，因此无法将从托管实例创建的本机 `COPY_ONLY` 备份还原到 SQL Server。

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>如何将实例数据库迁移到单个 Azure SQL 数据库？

一种做法是[将数据库导出到 bacpac](sql-database-export.md)，然后[导入 bacpac 文件]( sql-database-import.md)。 

如果数据库小于 100 GB，我们建议使用此方法。 如果数据库中的所有表具有主键，则可以使用事务复制。

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>如何在托管实例的第 4 代和第 5 代硬件代系之间进行选择？

这取决于工作负荷，因为某些硬件代系更适合某些类型的工作负荷。 尽管性能话题比较复杂，很难简述，不过，影响工作负荷性能的硬件代系之间存在以下差异：
- 与基于 vCore 处理器的第 5 代相比，基于物理处理器的第 4 代提供更好的计算支持。 对于计算密集型工作负荷，第 4 代可能更有优势。
- 第 5 代支持加速网络，可以提高远程存储的 IO 带宽。 对于常规用途服务层级上的 IO 密集型工作负荷，第 5 代可能更有优势。 与第 4 代相比，第 5 代使用速度更快的 SSD 本地磁盘。 对于业务关键服务层级上的 IO 密集型工作负荷，第 5 代可能更有优势。

建议客户在将用于生产的实际工作负荷投入使用之前先测试其性能，以确定哪种硬件代系更适合自己的用例。

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>能否在第 4 代和第 5 代托管实例硬件代系之间联机切换？ 

如果这两种硬件代系都可以在预配托管实例的同一区域中使用，则可以在硬件代系之间自动联机切换。 在这种情况下，Azure 门户的“定价层”部分会提供一个选项，用于在硬件代系之间切换。

这是一个长时间运行的操作，因为新托管实例将在后端预配，数据库将在旧实例与新实例之间自动转移。 对于客户而言，此过程是无缝的。

如果同一区域不能同时支持这两种硬件代系，可以更改硬件代系，但必须手动完成该操作。 这需要在所需硬件代系可用的区域中预配一个新实例，并在旧实例与新实例之间手动备份和还原数据。


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>如何优化托管实例的性能？ 

常规用途托管实例使用远程存储，因为数据和日志文件的大小对性能的影响很大。 若要优化常规用途服务层级性能，请遵照此博客文章中的说明操作。

对于 IO 密集型工作负荷，请考虑使用第 5 代硬件，而不要使用适合计算密集型工作负荷的第 4 代硬件。 有关详细信息，请参阅有关选择硬件代系的常见问题解答部分。

如果工作负荷包含大量小型事务，请考虑将连接类型从代理切换为重定向模式。

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>托管实例的最大存储大小是多少？ 

托管实例的存储大小取决于所选的服务层级（“常规用途”或“业务关键”）。 有关这些服务层级的存储限制，请参阅[服务层级特征](sql-database-service-tiers-general-purpose-business-critical.md)。

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>备份存储是否是从托管实例存储中扣减出来的？ 

不是，备份存储不是从托管实例的存储空间中扣减出来的。 备份存储与实例存储空间无关，其大小不受限制。 备份存储受实例数据库备份的保留时间（可配置为 7 到 35 天）的限制。 有关详细信息，请参阅[自动化备份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>如何针对管理端口设置入站 NSG 规则？

内置防火墙功能可在群集中的所有虚拟机上配置 Windows 防火墙，以便仅允许与 Microsoft 管理/部署计算机关联的 IP 范围发起的入站连接，并有效地保护管理工作站，防止攻击者通过网络层入侵。

下面是端口的用途：

端口 9000 和 9003 由 Service Fabric 基础结构使用。 Service Fabric 的主要作用是使虚拟群集保持正常，并根据组件副本数保持目标状态。

端口 1438、1440 和 1452 由节点代理使用。 节点代理是群集中运行的一个应用程序，控制平面使用它来执行管理命令。

除了网络层上的内置防火墙以外，还可以使用证书来保护通信。
  
有关详细信息以及如何验证内置防火墙，请参阅 [Azure SQL 数据库托管实例的内置防火墙](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。


## <a name="how-can-i-mitigate-networking-risks"></a>如何缓解网络风险？ 

为了缓解任何网络风险，我们建议客户应用一组安全设置和控制措施：

- 针对所有数据库启用透明数据加密 (TDE)。
- 禁用公共语言运行时 (CLR)。 也建议在本地禁用 CLR。
- 仅使用 Azure AD 帐户。
- 使用低特权 DBA 帐户访问 SQL MI。
- 为 sysadmin 帐户配置 JiT jumpbox 访问权限。
- 启用 SQL 审核，并将其与警报机制相集成。
- 在 ATS 套件中启用威胁检测。


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>在何处可以找到用例，以及使用托管实例可实现的成本节省？

托管实例案例研究：

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
为了让用户更好地了解部署 Azure SQL 数据库托管实例的优势、成本和风险，我们还提供了一份 Forrester 案例研究：[MI 产生的总体经济影响](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="can-i-do-dns-refresh"></a>是否可以执行 DNS 刷新？ 
  
目前我们不提供刷新托管实例 DNS 服务器配置的功能。

DNS 配置最终会刷新：

- 当 DHCP 租约过期时。
- 平台升级时。

一种解决方法是将托管实例降级为 4 个 vCore，然后再次将其升级。 这样刷新 DNS 配置会产生一种负面影响。


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>托管实例是否可以使用静态 IP 地址？

在罕见但必要的情况下，我们可能需要将托管实例联机迁移到新的虚拟群集。 需要进行这种迁移的原因是，我们的技术堆栈发生了变化，旨在提高服务的安全性和可靠性。 迁移到新的虚拟群集会导致映射到托管实例主机名的 IP 地址发生变化。 托管实例服务不会提出静态 IP 地址支持，且有权在不另行通知的情况下，在定期维护周期更改此 IP 地址。

出于此原因，我们强烈反对依赖于 IP 地址的不可变性，因为这可能会导致不必要的停机时间。

## <a name="can-i-move-a-managed-instance-or-vnet"></a>是否可以移动托管实例或 VNet？

不可以，这是当前的平台限制。 创建托管实例后，不支持将托管实例或 VNet 移到另一个资源组或订阅。

## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>是否可以更改现有托管实例的时区？

首次预配托管实例时可以设置时区配置。 不支持更改现有托管实例的时区。 有关详细信息，请参阅[时区限制](sql-database-managed-instance-timezone.md#limitations)。

解决方法包括使用适当的时区创建新的托管实例，然后执行手动备份和还原，我们建议执行[跨实例时间点还原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>如何解决托管实例的性能问题

若要在托管实例与 SQL Server 之间进行性能比较，可以从[有关 Azure SQL 托管实例与 SQL Server 之间的性能比较的最佳做法](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)着手。

由于必需的完整恢复模型以及事务日志写入吞吐量[限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)方面的原因，托管实例上的数据加载速度通常比 SQL Server 中的速度更慢。 有时，通过将瞬态数据载入 tempdb 而不是用户数据库，或者使用聚集列存储或内存优化表，可以解决此问题。


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>是否可将加密的数据库还原到托管实例？

可以，无需解密数据库即可将其还原到托管实例。 需将一个在源系统中用作加密密钥保护器的证书/密钥提供给托管实例，才能从加密的备份文件中读取数据。 可通过两种可能的方式来实现此目的：

- 将证书保护器上传到托管实例。 只能使用 PowerShell 执行此操作。 示例脚本描述了整个过程。
- 将非对称密钥保护器上传到 Azure Key Vault (AKV)，并将托管实例指向该保护器。 此方法类似于自带密钥 (BYOK) TDE 用例，该用例也使用 AKV 集成来存储加密密钥。 如果你只是想要将密钥上传到 AKV，让托管实例用来还原加密的数据库而不真正将该密钥用作加密密钥保护器，请遵照有关设置 BYOK TDE 的说明操作，且不要选中“将所选密钥设为默认 TDE 保护器”。

将加密保护器提供给托管实例使用后，可以继续执行标准的数据库还原过程。

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>如何从 Azure SQL 数据库单一池或弹性池迁移到托管实例？ 

托管实例根据计算和存储大小提供与其他 Azure SQL 数据库部署选项相同的性能级别。 若要在单一实例上合并数据，或者只是需要一种仅在托管实例中受支持的功能，可以使用导出/导入 (BACPAC) 功能来迁移数据。
