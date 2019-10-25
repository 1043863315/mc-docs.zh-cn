---
title: Azure Stack 诊断日志收集概述 | Microsoft Docs
description: 说明如何在 Azure Stack 的“帮助 + 支持”中进行诊断日志收集，包括按需日志收集和自动日志收集。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 09/04/2019
ms.date: 10/21/2019
ms.author: v-jay
ms.reviewer: prchint
ms.lastreviewed: 09/04/2019
ms.openlocfilehash: 6a0088c10a2737ead187232362acc408dd229f67
ms.sourcegitcommit: 713bd1d1b476cec5ed3a9a5615cfdb126bc585f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72578461"
---
# <a name="overview-of-azure-stack-diagnostic-log-collection"></a>Azure Stack 诊断日志收集概述 

*适用于：Azure Stack 集成系统*

Azure Stack 是一个大型集合，其中的组件可以一起工作并互相交互。 所有这些组件会生成自己独特的日志。 这样一来，问题可能就难以诊断，尤其是在错误来自多个交互的 Azure Stack 组件的情况下。 为了解决此难题，我们设计了一项诊断日志收集体验。 

在版本 1907 之前，诊断体验包括使用 [Test-AzureStack](azure-stack-diagnostic-test.md) 来验证系统运行状况，以及使用[特权终结点 (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) 来收集用于故障排除的日志。 

从 1907 版开始，“帮助和支持”  页添加了一项更简单的体验，即，使用“诊断日志收集”。  
“诊断日志收集”  是一项持续投资的一部分，目的是改进 Azure Stack 操作员在故障排除过程中的体验。 进行这些改进以后，操作员就可以快速收集诊断日志并将其与 Azure 客户支持服务 (CSS) 共享。 可以将日志存储在 Azure 的 Blob 容器中，并可根据需要在其中自定义访问权限。    
   
“诊断日志收集”  可以通过两种不同的方式收集诊断日志：

- **自动收集**：如果启用（此为建议的设置），日志收集将由特定的运行状况警报自动触发，并存储在 Azure 存储帐户中
- **立即收集日志**：此为按需选项，你可以从过去七天选择 1-4 小时滑动窗口来收集日志

![诊断日志收集选项的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

“诊断日志收集”  有易用的用户界面，不需要 PowerShell。 即使基础结构服务关闭，也可以可靠地收集日志。
从 1907 版开始，如果策略允许与 CSS 共享诊断日志，则“诊断日志收集”  是建议的收集方法。 如果“诊断日志收集”  在“帮助和支持”中不可用，则只应使用 [PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) 来收集日志。

## <a name="automatic-diagnostic-log-collection"></a>诊断日志自动收集 

出现某些严重警报时，“诊断日志自动收集”会主动将诊断日志从 Azure Stack 上传到 Azure 中的存储 Blob，显著缩短将诊断日志与 CSS 共享所需的时间。

有关日志自动收集的详细信息，请参阅[配置 Azure Stack 诊断日志自动收集](azure-stack-configure-automatic-diagnostic-log-collection.md)。

## <a name="on-demand-diagnostic-log-collection"></a>诊断日志按需收集

有了按需收集，当 Azure Stack 操作员手动触发收集时，诊断日志就会从 Azure Stack 上传到 Azure 中的存储 Blob。
CSS 会将共享访问签名 (SAS) URL 提供给 CSS 拥有的存储 Blob。 Azure Stack 操作员可以单击“立即收集日志”并输入 SAS URL。  然后，诊断日志就会直接上传到 CSS Blob，不需过渡型共享。 

若要详细了解如何按需收集日志，请参阅[立即收集 Azure Stack 诊断日志](azure-stack-configure-on-demand-diagnostic-log-collection.md)。

## <a name="bandwidth-considerations"></a>带宽注意事项

进行诊断日志收集时日志的平均大小各不相同，具体取决于它是按需运行还是自动运行。 自动日志收集的平均大小约为 2 GB，而按需日志收集的大小取决于需要收集多少小时。 

下表列出了在以受限或计量方式连接到 Azure 时的环境注意事项。

| 网络连接 | 影响 |
|--------------------|--------|
| 低带宽/高延迟连接 | 完成日志上传的时间会延长。 | 
| 共享连接 | 上传也可能影响共享网络连接的其他应用程序/用户 |
| 计量连接 | ISP 可能会针对你额外使用网络的情况收取额外费用 |

有关详细信息，请参阅 [Azure Stack 日志自动收集的最佳做法](azure-stack-best-practices-automatic-diagnostic-log-collection.md)。

## <a name="see-also"></a>另请参阅

[Azure Stack 日志和客户数据处理](/azure-stack/operator/azure-stack-data-collection)

[使用共享访问签名 (SAS)](/storage/common/storage-dotnet-shared-access-signature-part-1)

[有关 Azure Stack 日志自动收集的最佳做法](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
