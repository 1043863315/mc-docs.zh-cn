---
title: Azure Monitor 中的 Insights 概述 | Microsoft Docs
description: Insights 在 Azure Monitor 中为特定的应用程序和服务提供了自定义的监视体验。 本文简要介绍了目前可用的每种 Insights。
services: azure-monitor
documentationcenter: ''
author: lingliw
manager: digimobile
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 05/22/2019
ms.date: 06/21/2019
ms.author: v-lingwu
ms.openlocfilehash: c34eac44e701d7f409707ca49b2588d93716a870
ms.sourcegitcommit: dd0ff08835dd3f8db3cc55301815ad69ff472b13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70737398"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure Monitor 中的 Insights 概述
Insights 在 Azure Monitor 中为特定的应用程序和服务提供了自定义的监视体验。 他们将数据存储在 [Azure Monitor 数据平台](../platform/data-platform.md)中，并利用其他 Azure Monitor 功能进行分析和发警报，但可能会收集其他数据，并在 Azure 门户中提供独特的用户体验。 从 Azure 门户中 Azure Monitor 菜单的 **Insights** 部分访问 Insights。

以下部分简要介绍了 Azure Monitor 中当前可用的 Insights。 有关每种 Insights 的详细信息，请参阅相关详细文档。

## <a name="application-insights"></a>Application Insights
Application Insights 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 它适用于本地云、混合云或任何公有云中托管的各种平台（包括 .NET、Node.js 和 Java EE）中的应用程序。 它还与 DevOps 进程集成，并且具有与各种开发工具的连接点。

请参阅[什么是 Application Insights？](../app/app-insights-overview.md)。

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor
用于容器的 Azure Monitor 可监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 监视容器至关重要，特别是在大规模运行包含多个应用程序的生产群集时。

请参阅[用于容器的 Azure Monitor 概述](../insights/container-insights-overview.md)。

![用于容器的 Azure Monitor](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>用于资源组的 Azure Monitor（预览版）
用于资源组的 Azure Monitor 有助于对单个资源遇到的任何问题进行分类和诊断，同时提供有关整个资源组的运行状况和性能的上下文。

请参阅[使用 Azure Monitor（预览版）监视资源组](../insights/resource-group-insights.md)。

![用于资源组的 Azure Monitor](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>用于 VM 的 Azure Monitor（预览版）
用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。

请参阅[什么是用于 VM 的 Azure Monitor？](vminsights-overview.md)

![用于 VM 的 Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>后续步骤
* 详细了解 Insights 利用的 [Azure Monitor 数据平台](../platform/data-platform.md)。
* 了解 [Azure Monitor 使用的不同数据源](../platform/data-sources.md)以及每种 Insights 收集的不同类型的数据。
