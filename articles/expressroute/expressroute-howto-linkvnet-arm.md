---
title: 将虚拟网络链接到 ExpressRoute 线路：PowerShell：Azure | Azure
description: 本文档概述如何使用 Resource Manager 部署模型和 PowerShell 将虚拟网络 (VNet) 链接到 ExpressRoute 线路。
services: expressroute
documentationcenter: na
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: daacb6e5-705a-456f-9a03-c4fc3f8c1f7e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/08/2018
ms.author: v-yiso
ms.date: 04/02/2018
ms.openlocfilehash: 6136368c18bf1acf509e4a782efe753689cdc7c0
ms.sourcegitcommit: 61fc3bfb9acd507060eb030de2c79de2376e7dd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>将虚拟网络连接到 ExpressRoute 线路
> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [PowerShell（经典）](expressroute-howto-linkvnet-classic.md)
>

本文有助于使用资源管理器部署模型和 PowerShell 将虚拟网络 (VNet) 链接到 Azure ExpressRoute 线路。 虚拟网络可以在同一个订阅中，也可以属于另一个订阅。 本文还介绍如何更新虚拟网络链接。 

## <a name="before-you-begin"></a>准备阶段
* 安装最新版本的 Azure PowerShell 模块。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。
* 在开始配置之前，请查看[先决条件](./expressroute-prerequisites.md)、[路由要求](./expressroute-routing.md)和[工作流](./expressroute-workflows.md)。
* 必须有一个活动的 ExpressRoute 线路。 
  * 请按说明[创建 ExpressRoute 线路](./expressroute-howto-circuit-arm.md)，并通过连接提供商启用该线路。 
  * 确保为线路配置 Azure 专用对等互连。 有关路由说明，请参阅[配置路由](./expressroute-howto-routing-arm.md)一文。 
  * 确保配置 Azure 专用对等互连，并运行用户网络和 Microsoft 之间的 BGP 对等互连，以便启用端到端连接。
  * 确保已创建并完全预配一个虚拟网络和一个虚拟网络网关。 按照说明[为 ExpressRoute 创建虚拟网关](./expressroute-howto-add-gateway-resource-manager.md)。 ExpressRoute 的虚拟网关使用 GatewayType“ExpressRoute”，而不是 VPN。

* 最多可以将 10 个虚拟网络链接到一条标准 ExpressRoute 线路。 使用标准 ExpressRoute 线路时，所有虚拟网络都必须位于同一地缘政治区域。 

* 单个 VNet 可最多连接到 4 条 ExpressRoute 线路。 通过以下流程为正在连接的每条 ExpressRoute 线路创建新的连接对象。 ExpressRoute 线路可在同一订阅、不同订阅或两者兼有。

* 如果已启用 ExpressRoute 高级外接程序，则可以链接 ExpressRoute 线路的地缘政治区域外部的虚拟网络，或者将更多虚拟网络连接到 ExpressRoute 线路。 有关高级外接程序的更多详细信息，请参阅[常见问题解答](expressroute-faqs.md)。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>将同一订阅中的虚拟网络连接到线路

可以使用以下 cmdlet 将虚拟网络网关连接到 ExpressRoute 线路。 在运行 cmdlet 前，请确保已创建虚拟网络网关并可将其用于进行链接：

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "China North" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>将不同订阅中的虚拟网络连接到线路

用户可以在多个订阅之间共享 ExpressRoute 线路。 下图是在多个订阅之间共享 ExpressRoute 线路的简单示意图。

大型云中的每个较小云用于表示属于组织中不同部门的订阅。 组织内的每个部门可以使用自己的订阅部署其服务，但可以共享单个 ExpressRoute 线路以连接回本地网络。 单个部门（在此示例中为 IT 部门）可以拥有 ExpressRoute 线路。 组织内的其他订阅可以使用 ExpressRoute 线路。

> [!NOTE]
> 订阅所有者需要缴纳 ExpressRoute 线路的连接和带宽费用。 所有虚拟网络共享相同的带宽。
> 
> 

![跨订阅连接](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>管理 - 线路所有者和线路用户

“线路所有者”是 ExpressRoute 线路资源的已授权高级用户。 线路所有者可以创建可供“线路用户”兑换的授权。 线路用户是虚拟网关的所有者，这些网关与 ExpressRoute 线路位于不同的订阅中。 线路用户可以兑换授权（每个虚拟网络需要一个授权）。

线路所有者有权随时修改和撤消授权。 撤消授权会导致从已撤消访问权限的订阅中删除所有链路连接。

### <a name="circuit-owner-operations"></a>线路所有者操作

**创建授权**

线路所有者创建授权。 这样即可创建授权密钥，供线路用户用来将其虚拟网络网关连接到 ExpressRoute 线路。 一个授权只可用于一个连接。

以下 cmdlet 代码片段演示如何创建授权：

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


对此操作的响应将包含授权密钥和状态：

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**查看授权**

线路所有者可以通过运行以下 cmdlet 查看针对特定线路发出的所有授权：

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**添加授权**

线路所有者可以使用以下 cmdlet 添加授权：

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**删除授权**

线路所有者可以通过运行以下 cmdlet 来撤消/删除对用户的授权：

```powershell
Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    
```

### <a name="circuit-user-operations"></a>线路用户操作

线路用户需有对等 ID 以及线路所有者提供的授权密钥。 授权密钥是一个 GUID。

可通过以下命令检查对等 ID：

```powershell
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**兑换连接授权**

线路用户可以通过运行以下 cmdlet 兑现链接授权：

```powershell
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "China North" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**释放连接授权**

可以通过删除 ExpressRoute 线路与虚拟网络之间的连接释放授权。

## <a name="modify-a-virtual-network-connection"></a>修改虚拟网络连接
可以更新虚拟网络连接的某些属性。 

**更新连接权重**

虚拟网络可以连接到多条 ExpressRoute 线路。 可以从多条 ExpressRoute 线路收到相同的前缀。 若要选择使用哪个连接发送目标为此前缀的流量，可以更改连接的 *RoutingWeight*。 会在具有最高 *RoutingWeight* 的连接上发送流量。

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

*RoutingWeight* 的范围是 0 到 32000。 默认值为 0。

## <a name="next-steps"></a>后续步骤

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](./expressroute-faqs.md)。



<!--Update_Description:update meta properties and wording-->