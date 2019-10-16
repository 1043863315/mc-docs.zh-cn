---
title: 用于 Azure 备份的 Azure 资源管理器模板
description: 用于恢复服务保管库和 Azure 备份功能的 Azure 资源管理器模板
author: lingliw
manager: digimobile
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: v-lingwu
ms.custom: mvc
ms.openlocfilehash: b75e9147b255616cc2b6a9ada382543672ea4d12
ms.sourcegitcommit: 2f2ced6cfaca64989ad6114a6b5bc76700870c1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71330222"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>用于 Azure 备份的 Azure 资源管理器模板

下表包含可以与恢复服务保管库和 Azure 备份功能配合使用的 Azure 资源管理器模板的链接。

|   |   |
|---|---|
|**恢复服务保管库** | |
| [创建恢复服务保管库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| 创建恢复服务保管库。 此保管库可以用于 Azure 备份和 Azure Site Recovery。 |
|**备份虚拟机**| |
| [备份资源管理器 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | 使用现有的恢复服务保管库和备份策略，备份同一资源组中的资源管理器虚拟机。|
| [将 IaaS VM 备份到恢复服务保管库](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | 用于备份经典虚拟机和资源管理器虚拟机的模板。 |
| [为 IaaS VM 创建每周备份策略](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | 模板创建恢复服务保管库和每周备份策略，该策略用于备份经典虚拟机和资源管理器虚拟机。|
| [为 IaaS VM 创建每日备份策略](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | 模板创建恢复服务保管库和每日备份策略，该策略用于备份经典虚拟机和资源管理器虚拟机。|
| [部署启用了备份的 Windows Server VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | 模板创建启用默认备份策略的 Windows Server VM 和恢复服务保管库。|
|**监视备份作业** |  |
| [将 Azure Monitor 日志与 Azure Backup 配合使用](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | 模板部署用于 Azure 备份的 Azure Monitor 日志，后者用于监视备份和还原作业、备份警报以及在恢复服务保管库中使用的云存储。|  
|**在 Azure VM 中备份 SQL Server** |  |
| [在 Azure VM 中备份 SQL Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | 模板创建特定于恢复服务保管库和工作负荷的备份策略。 它使用 Azure 备份服务注册 VM 并在该 VM 上配置保护。 目前，它仅适用于 SQL 库映像。 |
|   |   |


<!-- Update_Description: update metedata properties -->