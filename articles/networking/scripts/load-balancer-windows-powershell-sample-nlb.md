---
title: Azure PowerShell 脚本示例：对传入 VM 的流量进行负载均衡以实现高可用性
description: Azure PowerShell 脚本示例：对传入 VM 的流量进行负载均衡以实现高可用性
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
origin.date: 05/16/2017
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: b1141d722af74607f08892ae990c5b072a29de0b
ms.sourcegitcommit: df1adc5cce721db439c1a7af67f1b19280004b2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "65835743"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>对传入 VM 的流量进行负载均衡以实现高可用性

此脚本示例创建运行多个 Windows 虚拟机（使用高度可用且负载均衡的配置进行配置）所需的所有项。 运行脚本后，即可拥有已加入到 Azure 可用性集并可通过 Azure 负载均衡器访问的 3 个虚拟机。

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

# <a name="variables-for-common-values"></a>常见值的变量
$rgName='MyResourceGroup' $location='chinaeast'

# <a name="create-user-object"></a>创建用户对象
$cred = Get-Credential -Message '输入用于虚拟机的用户名和密码。'

# <a name="create-a-resource-group"></a>创建资源组。
New-AzResourceGroup -Name $rgName -Location $location

# <a name="create-a-virtual-network"></a>创建虚拟网络。
$subnet = New-AzVirtualNetworkSubnetConfig -Name 'MySubnet' -AddressPrefix 192.168.1.0/24

$vnet = New-AzVirtualNetwork -ResourceGroupName $rgName -Name 'MyVnet' ` -AddressPrefix 192.168.0.0/16 -Location $location -Subnet $subnet

# <a name="create-a-public-ip-address"></a>创建公共 IP 地址。
$publicIp = New-AzPublicIpAddress -ResourceGroupName $rgName -Name 'myPublicIP' ` -Location $location -AllocationMethod Dynamic

# <a name="create-a-front-end-ip-configuration-for-the-website"></a>为网站创建前端 IP 配置。
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp

# <a name="create-the-back-end-address-pool"></a>创建后端地址池。
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

# <a name="creates-a-load-balancer-probe-on-port-80"></a>在端口 80 上创建负载均衡器探测。
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 ` -RequestPath / -IntervalInSeconds 360 -ProbeCount 5

# <a name="creates-a-load-balancer-rule-for-port-80"></a>为端口 80 创建负载均衡器规则。
$rule = New-AzLoadBalancerRuleConfig -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 ` -FrontendIpConfiguration $feip -BackendAddressPool $bePool

# <a name="create-three-nat-rules-for-port-3389"></a>为端口 3389 创建三个 NAT 规则。
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig -Name 'myLoadBalancerRDP1' -FrontendIpConfiguration $feip ` -Protocol tcp -FrontendPort 4221 -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig -Name 'myLoadBalancerRDP2' -FrontendIpConfiguration $feip ` -Protocol tcp -FrontendPort 4222 -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig -Name 'myLoadBalancerRDP3' -FrontendIpConfiguration $feip ` -Protocol tcp -FrontendPort 4223 -BackendPort 3389

# <a name="create-a-load-balancer"></a>创建负载均衡器。
$lb = New-AzLoadBalancer -ResourceGroupName $rgName -Name 'MyLoadBalancer' -Location $location `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool ` -Probe $probe -LoadBalancingRule $rule -InboundNatRule $natrule1,$natrule2,$natrule3

# <a name="create-a-network-security-group-rule-for-port-3389"></a>为端口 3389 创建网络安全组规则。
$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 ` -SourceAddressPrefix Internet -SourcePortRange * ` -DestinationAddressPrefix * -DestinationPortRange 3389

# <a name="create-a-network-security-group-rule-for-port-80"></a>为端口 80 创建网络安全组规则。
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 ` -SourceAddressPrefix Internet -SourcePortRange * ` -DestinationAddressPrefix * -DestinationPortRange 80

# <a name="create-a-network-security-group"></a>创建网络安全组
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location ` -Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2

# <a name="create-three-virtual-network-cards-and-associate-with-public-ip-address-and-nsg"></a>创建三个虚拟网卡，并将其与公共 IP 地址和 NSG 相关联。
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg ` -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg ` -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg ` -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]

# <a name="create-an-availability-set"></a>创建可用性集。
$as = New-AzAvailabilitySet -ResourceGroupName $rgName -Location $location ` -Name 'MyAvailabilitySet' -Sku Aligned -PlatformFaultDomainCount 3 -PlatformUpdateDomainCount 3

# <a name="create-three-virtual-machines"></a>创建三个虚拟机。

# <a name="-vm1"></a>############## VM1 ###############

# <a name="create-a-virtual-machine-configuration"></a>创建虚拟机配置
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS2 -AvailabilitySetId $as.Id | `
  Set-AzureRmVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred | ` Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer ` -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nicVM1.Id

# <a name="create-a-virtual-machine"></a>创建虚拟机
$vm1 = New-AzVM -ResourceGroupName $rgName -Location $location -VM $vmConfig

# <a name="-vm2"></a>############## VM2 ###############

# <a name="create-a-virtual-machine-configuration"></a>创建虚拟机配置
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS2 -AvailabilitySetId $as.Id | `
  Set-AzureRmVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred | ` Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer ` -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nicVM2.Id

# <a name="create-a-virtual-machine"></a>创建虚拟机
$vm2 = New-AzVM -ResourceGroupName $rgName -Location $location -VM $vmConfig

# <a name="-vm3"></a>############## VM3 ###############

# <a name="create-a-virtual-machine-configuration"></a>创建虚拟机配置
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS2 -AvailabilitySetId $as.Id | `
  Set-AzureRmVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred | ` Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer ` -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nicVM3.Id

# <a name="create-a-virtual-machine"></a>创建虚拟机
$vm3 = New-AzVM -ResourceGroupName $rgName -Location $location -VM $vmConfig

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机、可用性集、负载均衡器和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/zh-cn/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 创建子网配置。 在虚拟网络创建过程中会使用此配置。 |
| [New-AzVirtualNetwork](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-azvirtualnetwork) | 创建 Azure 虚拟网络和子网。 |
| [New-AzPublicIpAddress](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-azpublicipaddress)  | 使用静态 IP 地址和关联的 DNS 名称创建公共 IP 地址。 |
| [New-AzLoadBalancer](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-azloadbalancer)  | 创建 Azure 负载均衡器。 |
| [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-azloadbalancerprobeconfig) | 创建负载均衡器探测。 负载均衡器探测用于监视负载均衡器集中的每个 VM。 如果任何 VM 无法访问，流量不会路由到该 VM。 |
| [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-azloadbalancerruleconfig) | 创建负载均衡器规则。 在此示例中，将为端口 80 创建一个规则。 当 HTTP 流量到达负载均衡器时，它会路由到负载均衡器集中某个 VM 的端口 80。 |
| [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | 创建负载均衡器网络地址转换 (NAT) 规则。  NAT 规则将负载均衡器的端口映射到 VM 上的端口。 在本示例中，将为发往负载均衡器集中的每个 VM 的 SSH 流量创建 NAT 规则。  |
| [New-AzNetworkSecurityGroup](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-aznetworksecuritygroup) | 创建网络安全组 (NSG)，这是 Internet 和虚拟机之间的安全边界。 |
| [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-aznetworksecurityruleconfig) | 创建 NSG 规则以允许入站流量。 在此示例中，为 SSH 流量打开端口 22。 |
| [New-AzNetworkInterface](https://docs.microsoft.com/zh-cn/powershell/module/az.network/new-aznetworkinterface) | 创建虚拟网卡并将其连接到虚拟网络、子网和 NSG。 |
| [New-AzAvailabilitySet](https://docs.microsoft.com/zh-cn/powershell/module/az.compute/new-azavailabilityset) | 创建可用性集。 可用性集通过将虚拟机分布到各个物理资源上（以便发生故障时，不会影响整个集）来确保应用程序运行时间。 |
| [New-AzVMConfig](https://docs.microsoft.com/zh-cn/powershell/module/az.compute/new-azvmconfig) | 创建 VM 配置。 此配置包括 VM 名称、操作系统和管理凭据等信息。 在创建 VM 期间使用此配置。 |
| [New-AzVM](https://docs.microsoft.com/zh-cn/powershell/module/az.compute/new-azvm)  | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [Remove-AzResourceGroup](https://docs.microsoft.com/zh-cn/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可在 [Azure 网络概述文档](../powershell-samples.md?toc=%2fnetworking%2ftoc.json)中找到其他网络 PowerShell 脚本示例。
