---
title: Azure Stack Hub 中的客户计费和退款
description: 了解如何根据资源使用情况对 Azure Stack Hub 用户进行计费，以及如何访问计费信息以进行分析和退款。
author: WenJason
ms.topic: article
origin.date: 01/22/2020
ms.date: 02/24/2020
ms.author: v-jay
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 84b274d0b49e410e70ab3faa3fe0c673c82809b4
ms.sourcegitcommit: afe972418a883551e36ede8deae32ba6528fb8dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77540884"
---
# <a name="usage-and-billing-in-azure-stack-hub"></a>Azure Stack Hub 中的使用情况和计费

本文介绍如何根据资源使用情况对 Azure Stack Hub 用户进行计费，以及如何访问计费信息以进行分析和退款。

Azure Stack Hub 针对使用的资源收集使用情况数据并将其分组，然后将此数据转发到 Azure Commerce。 Azure Commerce 计收 Azure Stack Hub 使用费的方式与计收 Azure 使用费相同。

也可以使用计费适配器来获取使用情况数据并将其导出到自己的计费或退款系统，或者导出到商业智能工具（例如 Microsoft Power BI）。

## <a name="usage-pipeline"></a>使用情况管道

Azure Stack Hub 中的每个资源提供程序会根据资源使用情况发布使用情况数据。 使用情况服务定期（每小时或每天）聚合使用情况数据并将其存储在使用情况数据库中。 Azure Stack Hub 操作员和用户可以通过 Azure Stack Hub 资源使用情况 API 来访问存储的使用情况数据。

如果已[将 Azure Stack Hub 实例注册到 Azure](azure-stack-registration.md)，则 Azure Stack Hub 会配置为将使用情况数据发送到 Azure Commerce。 

下图显示了使用情况管道中的关键组件：

![使用情况管道](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>可以找到哪些使用情况信息，如何查找？

Azure Stack Hub 资源提供程序（例如计算、存储和网络）每隔一小时为每个订阅生成使用情况数据。 使用情况数据包含有关所用资源的信息，例如资源名称、所用订阅和所用数量。 若要了解计量的 ID 资源，请参阅[使用情况 API 常见问题解答](azure-stack-usage-related-faq.md)。


> [!NOTE]  
> 对于 Azure Stack 开发工具包 (ASDK) 和在容量模型下许可的 Azure Stack Hub 集成系统用户，不需要进行使用情况数据报告。

Azure 计费门户显示应计费资源的使用情况数据。 除了应计费资源之外，Azure Stack Hub 还会捕获更广范围内资源的使用情况数据，可以通过 REST API 或 PowerShell cmdlet 在 Azure Stack Hub 环境中访问这些数据。 Azure Stack Hub 操作员可以获取所有用户订阅的使用情况数据。 单个用户只能获取自己的详细使用情况。

## <a name="usage-reporting-for-multi-tenant-cloud-solution-providers"></a>多租户云解决方案提供商的使用情况报告

使用 Azure Stack Hub 的多租户云解决方案提供商 (CSP) 可能希望单独报告每个客户的使用情况，以便提供商可以向不同的 Azure 订阅收取使用费用。

每个客户将会获得一个按不同 Azure Active Directory (Azure AD) 租户表示的标识。 Azure Stack Hub 支持向每个 Azure AD 租户分配一个 CSP 订阅。 可将租户及其订阅添加到基本 Azure Stack Hub 注册。 将会针对所有 Azure Stack Hub 实例执行基本注册。 如果没有为租户注册订阅，用户仍可使用 Azure Stack Hub，其使用情况数据将发送到用于基本注册的订阅。

## <a name="next-steps"></a>后续步骤

- [注册到 Azure Stack Hub](azure-stack-registration.md)
- [提供程序资源使用情况 API](azure-stack-provider-resource-api.md)
- [租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)
- [有关使用情况的常见问题解答](azure-stack-usage-related-faq.md)
