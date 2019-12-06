---
title: 纵向扩展功能和容量 - Azure 应用服务 | Microsoft Docs
description: 了解如何增加 Azure 应用服务中的应用以增加容量和功能。
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
origin.date: 08/19/2019
ms.date: 11/25/2019
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: 8c008a456c790f1c46756da10c9b09f3e7c5196f
ms.sourcegitcommit: e7dd37e60d0a4a9f458961b6525f99fa0e372c66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555957"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>在 Azure 应用服务中纵向扩展应用

本文介绍如何在 Azure 应用服务中缩放应用。 缩放的工作流有两种：向上缩放和向外缩放；本文介绍向上缩放工作流。

* [纵向扩展](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：获取更多 CPU、内存、磁盘空间和额外功能，例如专用虚拟机 (VM)、自定义域和证书、过渡槽、自动缩放以及更多功能。 可以通过更改应用所属的应用服务计划的定价层来向上缩放。
* [横向扩展](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加用于运行应用的 VM 实例数。
  可以横向扩展到多达 30 个实例，具体取决于定价层。 独立层中的[应用服务环境](environment/intro.md)会进一步将横向扩展计数增加到 100 个实例  。 有关向外缩放的详细信息，请参阅[手动或自动缩放实例计数](/azure-monitor/platform/autoscale-get-started)。 可在该文中了解如何使用自动缩放，即根据预定义的规则和计划自动缩放实例计数。

缩放设置仅需几秒即可应用，并且会影响[应用服务计划](../app-service/overview-hosting-plans.md)中的所有应用。
缩放设置不需要更改代码或重新部署应用程序。

有关各个应用服务计划的定价和功能的信息，请参阅[应用服务定价详细信息](https://www.azure.cn/pricing/details/app-service/)。  

<!-- no spending limits in mooncake -->

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>增加定价层

> [!NOTE]
> 要纵向扩展到 PremiumV2 层，请参阅[为应用服务配置 PremiumV2 层](app-service-configure-premium-tier.md)  。
>

1. 在浏览器中，打开 [Azure 门户][portal]。

1. 在应用服务应用页的左侧菜单中，选择“纵向扩展(应用服务计划)”。 
   
3. 选择层，然后选择“应用”。  选择不同的类别（例如“生产”），并选择“查看其他选项”以显示更多层。  
   
    ![导航到向上缩放 Azure 应用。][ChooseWHP]

    操作完成后，会看到一个包含绿色勾选标记（成功）的通知弹出窗口。

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>与缩放相关的资源
如果应用依赖于其他服务，如 Azure SQL 数据库或 Azure 存储，则可单独对这些资源进行纵向扩展。 这些资源不由应用服务计划管理。

1. 在应用的“概述”页中，选择“资源组”链接。  
   
    ![与向上缩放 Azure 应用相关的资源](./media/web-sites-scale/RGEssentialsLink.png)

2. 在“资源组”页的“摘要”部分，选择要缩放的资源。   以下屏幕截图显示了 SQL 数据库资源。
   
    ![导航到资源组页面对 Azure 应用进行纵向扩展](./media/web-sites-scale/ResourceGroup.png)

    若要纵向扩展相关资源，请参阅特定资源类型的文档。 例如，若要纵向扩展单个 SQL 数据库，请参阅[缩放 Azure SQL 数据库中的单一数据库资源](../sql-database/sql-database-single-database-scale.md)。 若要纵向扩展 Azure Database for MySQL 资源，请参阅[缩放 MySQL 资源](../mysql/concepts-pricing-tiers.md#scale-resources)。

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>比较定价层

有关详细信息（例如每个定价层的 VM 大小），请参阅[应用服务定价详细信息](https://www.azure.cn/pricing/details/app-service)。

有关服务限制、配额和约束的表以及每个层级所支持的功能，请参阅[应用服务限制](../azure-subscription-service-limits.md#app-service-limits)。

<a name="Next Steps"></a>

## <a name="more-resources"></a>更多资源

<!-- [Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md) -->
<!-- [SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930 -->

[为应用服务配置 PremiumV2 层](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://www.azure.cn/pricing/details/app-service/
[azuresubscriptions]:https://account.windowsazure.cn/subscriptions
[portal]: https://portal.azure.cn/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
