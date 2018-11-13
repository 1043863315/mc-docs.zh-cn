---
title: 安装本地数据网关 | Azure
description: 了解如何安装并配置本地数据网关。
author: rockboyfor
manager: digimobile
ms.service: azure-analysis-services
ms.topic: conceptual
origin.date: 09/10/2018
ms.date: 11/22/2018
ms.author: v-yeche
ms.reviewer: minewiskan
ms.openlocfilehash: 3a8a0b18a8d4ce5cf15baa2e95e7fba45955f78b
ms.sourcegitcommit: e8a0b7c483d88bd3c88ed47ed2f7637dec171a17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2018
ms.locfileid: "51195353"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>安装并配置本地数据网关
当同一区域中的一个或多个 Azure Analysis Services 服务器连接到本地数据源时，需要本地数据网关。 若要了解有关网关的详细信息，请参阅[本地数据网关](analysis-services-gateway.md)。

## <a name="prerequisites"></a>先决条件
**最低要求：**

* .NET 4.5 Framework
* 64 位版本的 Windows 7/Windows Server 2008 R2（或更高版本）

**推荐：**

* 8 核 CPU
* 8 GB 内存
* 64 位版本的 Windows 2012 R2（或更高版本）

**重要注意事项：**

* 在安装过程中将网关注册到 Azure 时，会选择订阅的默认区域。 可以选择不同的区域。 如果在多个区域中具有服务器，则必须为每个区域安装一个网关。 
* 不能在域控制器上安装网关。
* 一台计算机上只能安装一个网关。
* 在计算机处于开启但未处于休眠状态下安装网关。
* 不要在使用无线网络连接的计算机上安装网关。 否则，可能会降低性能。
* 安装网关时，你用来登录到计算机的用户帐户必须具有“作为服务登录”权限。 安装完成后，本地数据网关服务使用 NT SERVICE\PBIEgwService 帐户作为服务登录。 可以在安装期间指定一个不同的帐户，也可以在安装完成后在“服务”中指定一个不同的帐户。 请确保组策略设置同时允许你在安装时登录的帐户以及你选择的具有“作为服务登录”权限的服务帐户。
* 在 Azure AD 中使用与要在其中注册网关的订阅相同[租户](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)的帐户登录到 Azure。 安装和注册网关时不支持 Azure B2B（来宾）帐户。
* 如果数据源位于 Azure 虚拟网络 (VNet) 上，则必须配置 [AlwaysUseGateway](analysis-services-vnet-gateway.md) 服务器属性。
<!-- * The (unified) gateway described here is not supported in Azure China Cloud, Azure Germany, and Azure China sovereign regions. Use **Dedicated On-premises gateway for Azure Analysis Services**, installed from your server's **Quick Start** in the portal. -->

<a name="download"></a>
## <a name="download"></a>下载
 [下载网关](https://aka.ms/azureasgateway-mc)

<a name="install"></a>
## <a name="install"></a>安装

1. 运行安装程序。

2. 选择位置，接受条款，并单击“安装”。

   ![安装位置和许可条款](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. 登录 Azure。 该帐户必须在租户的 Azure Active Directory 中。 这是网关管理员使用的帐户。 安装和注册网关时不支持 Azure B2B（来宾）帐户。

   ![登录 Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > 如果使用域帐户登录，它将映射到你在 Azure AD 中的组织帐户。 你的组织帐户将用作网关管理员。

<a name="register"></a>
## <a name="register"></a>注册
若要在 Azure 中创建网关资源，必须将安装的本地实例注册到网关云服务。 

1.  选择“在此计算机上注册新网关”。

    ![注册](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. 键入网关的名称和恢复密钥。 默认情况下，网关使用订阅的默认区域。 如需选择不同的区域，请选择“更改区域”。

    > [!IMPORTANT]
    > 将恢复密钥保存在安全位置。 接管、迁移或还原网关时需要使用恢复密钥。 

   ![注册](media/analysis-services-gateway-install/aas-gateway-register-name.png)

<a name="create-resource"></a>
## <a name="create-an-azure-gateway-resource"></a>创建 Azure 网关资源
安装并注册网关后，需在 Azure 订阅中创建网关资源。 使用注册网关时所用的同一帐户登录到 Azure。

1. 在 Azure 门户中，单击“创建资源” > “集成” > “本地数据网关”。

   ![创建网关资源](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. 在“创建连接网关”中，输入以下设置：

    * **名称**：输入网关资源的名称。 

    * **订阅**：选择要与网关资源关联的 Azure 订阅。 

      默认订阅取决于用来登录的 Azure 帐户。

    * **资源组**：创建资源组或选择现有资源组。

    * **位置**：选择网关的注册区域。

    * **安装名称**：如果尚未选择网关安装，请选择注册的网关。 

    完成后，单击“创建”。

<a name="connect-servers"></a>
## <a name="connect-servers-to-the-gateway-resource"></a>将服务器连接到网关资源

1. 在 Azure Analysis Services 服务器概述中，单击“本地数据网关”。

   ![将服务器连接到网关](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. 在“选择要连接的本地数据网关”中选择自己的网关资源，并单击“连接选定网关”。

   ![将服务器连接到网关资源](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > 如果列表中不显示你的网关，很可能是你的服务器与你注册网关时指定的区域不在同一个区域。 

就这么简单。 如需打开端口或执行任何故障排除，请务必查看[本地数据网关](analysis-services-gateway.md)。

## <a name="next-steps"></a>后续步骤
* [管理 Analysis Services](analysis-services-manage.md)   
* [从 Azure Analysis Services 获取数据](analysis-services-connect.md)   
* [对 Azure 虚拟网络上的数据源使用网关](analysis-services-vnet-gateway.md)

<!-- Update_Description: update meta properties  -->
