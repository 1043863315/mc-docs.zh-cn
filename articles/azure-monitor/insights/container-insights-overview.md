---
title: 用于容器的 Azure Monitor 概述 | Azure Docs
description: 本文介绍用于容器的 Azure Monitor，它通过监视 AKS 群集和 Azure 中容器实例的运行状况监视 AKS 容器见解解决方案及其提供的值。
ms.topic: conceptual
author: lingliw
ms.author: v-lingwu
origin.date: 01/07/2020
ms.date: 2/18/2020
ms.openlocfilehash: 9a412fb127d1ac7f0b1c1754e34b442d1efc4fbf
ms.sourcegitcommit: 27eaabd82b12ad6a6840f30763034a6360977186
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77497613"
---
# <a name="azure-monitor-for-containers-overview"></a>用于容器的 Azure Monitor 概述

用于容器的 Azure Monitor 功能旨在监视部署到以下位置的容器工作负荷的性能：

- Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集
- Azure 容器实例
- Azure Stack 或本地上托管的自我托管 Kubernetes 群集
- Azure Red Hat OpenShift

适用于容器的 Azure Monitor 支持运行 Linux 和 Windows Server 2019 操作系统的群集。 

监视容器至关重要，特别是在大规模运行包含多个应用程序的生产群集时。

用于容器的 Azure Monitor 通过 Metrics API 从 Kubernetes 中提供的控制器、节点和容器收集内存和处理器指标，来提供性能可见性。 容器日志也会被收集。  从 Kubernetes 群集启用监视后，将通过适用于 Linux 的 Log Analytics 代理的容器化版本自动收集指标和日志。 指标将写入指标存储区，日志数据将写入与 [Log Analytics](../log-query/log-query-overview.md) 工作区关联的日志存储区。 

![用于容器的 Azure Monitor 的体系结构](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>用于容器的 Azure Monitor 提供了什么？

适用于容器的 Azure Monitor 提供使用 Azure Monitor 的不同功能的综合监视体验。 利用这些功能，可以了解运行 Linux 和 Windows Server 2019 操作系统的 Kubernetes 群集的性能和运行状况，以及容器工作负载。 使用用于容器的 Azure Monitor 可以：

* 确定节点上运行的 AKS 容器及其平均处理器和内存利用率。 此信息可帮助标识资源瓶颈。
* 确定 Azure 容器实例中托管的容器组及其容器的处理器和内存利用率。  
* 确定容器在控制器或 Pod 中的驻留位置。 此信息可帮助了解控制器或 Pod 的整体性能。 
* 查看在主机上运行的与支持 Pod 的标准过程无关的工作负荷的资源利用率。
* 了解群集在平均负载和最重负载下的行为。 此信息有助于了解容量需求及确定群集可承受的最大负载。 
* 配置警报，以便在节点或容器上的 CPU 和内存使用率超过阈值时，或者在基础结构中的群集或节点运行状况汇总中发生运行状况状态更改时，主动通知你或进行记录。
* 与 [Prometheus](https://prometheus.io/docs/introduction/overview/) 集成，以使用[查询](container-insights-log-search.md)查看从节点和 Kubernetes 收集的应用程序和工作负荷指标，以创建自定义警报、仪表板和详细的执行详细分析。

    >[!NOTE]
    >目前，对 Azure Red Hat OpenShift 的支持是公共预览版中的一项功能。
    >

监视 Windows Server 群集与监视 Linux 群集相比，其主要差异如下：

- 内存 RSS 指标不适用于 Windows 节点和容器。
- 磁盘存储容量信息不适用于 Windows 节点。
- 容器日志不可用于 Windows 节点中运行的容器。
- 实时数据（预览版）功能支持已提供，但不可用于 Windows 容器日志。
- 仅监视 Pod 环境，不监视 Docker 环境。
- 使用预览版时，最多支持 30 个 Windows Server 容器。 此限制不适用于 Linux 容器。 

## <a name="how-do-i-access-this-feature"></a>如何访问此功能？
可以通过两种方式访问用于容器的 Azure Monitor：从 Azure Monitor 访问或直接从所选 AKS 群集访问。 在 Azure Monitor 中可以从全局角度查看已部署的所有容器（受到监视的容器和未受监视的容器），从而可以跨订阅和资源组进行搜索和筛选，然后从所选容器钻取到用于容器的 Azure Monitor。  否则，可以直接从 AKS 页上选定的 AKS 容器访问该功能。  

![访问用于容器的 Azure Monitor 的方法概述](./media/container-insights-overview/azmon-containers-experience.png)

如果有兴趣监视和管理在 AKS 外部运行的 Docker 和 Windows 容器主机以查看配置、审核和资源利用率，请参阅[容器监视解决方案](../../azure-monitor/insights/containers.md)。

## <a name="next-steps"></a>后续步骤

若要开始监视 Kubernetes 群集，请查看[如何启用用于容器的 Azure Monitor](container-insights-onboard.md)，以了解启用监视的要求和可用方法。 
