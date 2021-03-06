---
title: Azure Stack Hub SQL 资源提供程序 1.1.33.0 发行说明
titleSuffix: Azure Stack Hub
description: 查看 Azure Stack Hub SQL 资源提供程序 1.1.33.0 更新的发行说明。
author: WenJason
ms.topic: article
origin.date: 1/22/2020
ms.date: 02/24/2020
ms.author: v-jay
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 37d62b92d14af5e662690b160c7e2e8df72e97ec
ms.sourcegitcommit: afe972418a883551e36ede8deae32ba6528fb8dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77540309"
---
# <a name="sql-resource-provider-11330-release-notes"></a>SQL 资源提供程序 1.1.33.0 发行说明

本发行说明介绍 SQL 资源提供程序 1.1.33.0 版中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 SQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序具有相应的最低 Azure Stack Hub 版本。 下面列出了安装此 SQL 资源提供程序版本所需的最低 Azure Stack Hub 发行版：

> |最低 Azure Stack Hub 版本|SQL 资源提供程序版本|
> |-----|-----|
> |版本 1808 (1.1808.0.97)|[SQL RP 版本 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 SQL 资源提供程序之前，请先将支持的最低 Azure Stack Hub 更新应用到 Azure Stack Hub 集成系统。

## <a name="new-features-and-fixes"></a>新功能和修复
此 Azure Stack Hub SQL 资源提供程序版本包含以下改进和修复：

### <a name="fixes"></a>修复项

- **SQL 资源提供程序门户扩展可能选择了错误的订阅**。 SQL 资源提供程序使用 Azure 资源管理器调用来确定要使用的第一个服务管理员订阅，该订阅可能不是默认的提供程序订阅。  如果发生这种情况，则表明 SQL 资源提供程序运行不正常。

- **SQL 宿主服务器未列出托管的数据库。** 查看 SQL 宿主服务器的租户资源时，可能会发现用户创建的数据库未列出。

- **如果未启用 TLS 1.2，以前的 SQL 资源提供程序 (1.1.30.0) 部署可能会失败**。 更新了 SQL 资源提供程序 1.1.33.0，可以在部署资源提供程序、更新资源提供程序或轮换机密时启用 TLS 1.2。

- **SQL 资源提供程序机密轮换失败**。 修复了在轮换机密时会生成以下错误代码的问题：`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>已知问题

- **SQL SKU 最长可能需要在一小时后才显示在门户中**。 创建新的 SQL 数据库时，新建的 SKU 最长可能需要在一小时后才显示出来并可供使用。

    **解决方法**：无。

- **重复使用 SQL 登录名**。 尝试使用与现有登录名相同的用户名在同一订阅下创建新的 SQL 登录名时，会导致重复使用同一登录名和现有密码的问题。

    **解决方法**：在同一订阅下创建新登录名时使用不同的用户名，或者在不同订阅下使用相同的用户名创建登录名。

- **共享的 SQL 登录名导致数据不一致**。 如果在同一订阅下为多个 SQL 数据库共享某个 SQL 登录名，则更改登录密码会导致数据不一致。

    **解决方法**：在同一订阅下始终对不同的数据库使用不同的登录名。

- **SQL 资源提供程序无法添加 SQL Server Always On 侦听器**。 使用 SQL Server Always On 侦听器的 IP 地址时，SQL 资源提供程序 VM 无法解析侦听器的主机名。

    **解决方法**：确保 DNS 正常运行，以便将侦听器 IP 解析为侦听器主机名。

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>云管理员操作 Azure Stack Hub 时的已知问题
请参阅 [Azure Stack Hub 发行说明](azure-stack-servicing-policy.md)中的文档。

## <a name="next-steps"></a>后续步骤
[详细了解 SQL 资源提供程序](azure-stack-sql-resource-provider.md)。

[准备部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md#prerequisites)。

[从旧版升级 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。