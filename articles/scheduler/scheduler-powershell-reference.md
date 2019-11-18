---
title: PowerShell cmdlet 参考 - Azure 计划程序
description: 了解 Azure 计划程序 PowerShell cmdlet
services: scheduler
ms.service: scheduler
author: WenJason
ms.author: v-jay
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
origin.date: 08/18/2016
ms.date: 11/04/2019
ms.openlocfilehash: f97af0a71da1338bcf2d537d8d7fa87a701f686c
ms.sourcegitcommit: f9a257e95444cb64c6d68a7a1cfe7e94c5cc5b19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416274"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure 计划程序 PowerShell cmdlet 参考

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)将替换[即将停用](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)的 Azure 计划程序。 若要继续使用在计划程序中设置的作业，请尽快[迁移到 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要编写脚本以创建和管理计划程序作业和作业集合，可以使用 PowerShell cmdlet。 本文列出了主要 Azure 计划程序 PowerShell cmdlet，以及指向其参考文章的链接。 要为 Azure 订阅安装 Azure PowerShell，请参阅[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。 有关 [Azure Resource Manager cmdlet](https://docs.microsoft.com/powershell/azure/overview) 的详细信息，请参阅[将 Azure PowerShell 与 Azure Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

| Cmdlet | 说明 |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |禁用某一作业集合。 |
| [Enable-AzureRmSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |启用某一作业集合。 |
| [Get-AzSchedulerJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |获取计划程序作业。 |
| [Get-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |获取作业集合。 |
| [Get-AzSchedulerJobHistory](https://docs.microsoft.com/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |获取作业历史记录。 |
| [New-AzSchedulerHttpJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |创建 HTTP 作业。 |
| [New-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |创建作业集合。 |
| [New-AzSchedulerServiceBusQueueJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | 创建服务总线队列作业。 |
| [New-AzSchedulerServiceBusTopicJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |创建服务总线主题作业。 |
| [New-AzSchedulerStorageQueueJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |创建存储队列作业。 |
| [Remove-AzSchedulerJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |删除计划程序作业。 |
| [Remove-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |删除作业集合。 |
| [Set-AzSchedulerHttpJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |修改计划程序 HTTP 作业。 |
| [Set-AzSchedulerJobCollection](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |修改作业集合。 |
| [Set-AzSchedulerServiceBusQueueJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |修改服务总线队列作业。 |
| [Set-AzSchedulerServiceBusTopicJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |修改服务总线主题作业。 |
| [Set-AzSchedulerStorageQueueJob](https://docs.microsoft.com/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |修改存储队列作业。 |
||| 

有关详细信息，可以运行以下任何 cmdlet： 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>另请参阅

* [什么是 Azure 计划程序？](scheduler-intro.md)
* [概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [创建和计划第一个作业 - Azure 门户](scheduler-get-started-portal.md)
* [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)
