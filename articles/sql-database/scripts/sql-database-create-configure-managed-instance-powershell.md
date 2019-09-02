---
title: PowerShell 示例 - 在 Azure SQL 数据库中创建托管实例 | Microsoft Docs
description: Azure PowerShell 示例脚本，用于在 Azure SQL 数据库中创建托管实例
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
manager: digimobile
origin.date: 03/25/2019
ms.date: 08/19/2019
ms.openlocfilehash: 2f9b550bfb476562a486a1e90d569809e9c11cbd
ms.sourcegitcommit: 52ce0d62ea704b5dd968885523d54a36d5787f2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544859"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>使用 PowerShell 创建 Azure SQL 数据库托管实例

此 PowerShell 脚本示例在新虚拟网络的专用子网中创建 Azure SQL 数据库托管实例。 它还为虚拟网络配置路由表和网络安全组。 成功运行脚本后，即可从虚拟网络或本地环境访问托管实例。 请参阅[将 Azure VM 配置为连接到 Azure SQL 数据库托管实例](../sql-database-managed-instance-configure-vm.md)和[配置从本地到 Azure SQL 数据库托管实例的点到站点连接](../sql-database-managed-instance-configure-p2s.md)。

> [!IMPORTANT]
> 有关限制，请参阅[支持的区域](../sql-database-managed-instance-resource-limits.md#supported-regions)和[支持的订阅类型](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)。

本教程需要 AZ PowerShell 1.4.0 或更高版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 需要运行 `Connect-AzAccount -Environment AzureChinaCloud` 以创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

```powershell
# Connect-AzAccount -Environment AzureChinaCloud
# The SubscriptionId in which to create these objects
$SubscriptionId = ''
# Set the resource group name and location for your managed instance
$resourceGroupName = "myResourceGroup-$(Get-Random)"
$location = "chinaeast"
# Set the networking values for your managed instance
$vNetName = "myVnet-$(Get-Random)"
$vNetAddressPrefix = "10.0.0.0/16"
$defaultSubnetName = "myDefaultSubnet-$(Get-Random)"
$defaultSubnetAddressPrefix = "10.0.0.0/24"
$miSubnetName = "myMISubnet-$(Get-Random)"
$miSubnetAddressPrefix = "10.0.0.0/24"
#Set the managed instance name for the new managed instance
$instanceName = "myMIName-$(Get-Random)"
# Set the admin login and password for your managed instance
$miAdminSqlLogin = "SqlAdmin"
$miAdminSqlPassword = "ChangeYourAdminPassword1"
# Set the managed instance service tier, compute level, and license mode
$edition = "General Purpose"
$vCores = 8
$maxStorage = 256
$computeGeneration = "Gen4"
$license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount

# Set subscription context
Set-AzContext -SubscriptionId $subscriptionId 

# Create a resource group
$resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location

# Configure virtual network, subnets, network security group, and routing table
$virtualNetwork = New-AzVirtualNetwork `
                      -ResourceGroupName $resourceGroupName `
                      -Location $location `
                      -Name $vNetName `
                      -AddressPrefix $vNetAddressPrefix

                  Add-AzVirtualNetworkSubnetConfig `
                      -Name $miSubnetName `
                      -VirtualNetwork $virtualNetwork `
                      -AddressPrefix $miSubnetAddressPrefix `
                  | Set-AzVirtualNetwork

$virtualNetwork = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroupName

$miSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                        -Name $miSubnetName `
                        -VirtualNetwork $virtualNetwork 

$miSubnetConfigId = $miSubnetConfig.Id

$networkSecurityGroupMiManagementService = New-AzNetworkSecurityGroup `
                      -Name 'myNetworkSecurityGroupMiManagementService' `
                      -ResourceGroupName $resourceGroupName `
                      -location $location

$routeTableMiManagementService = New-AzRouteTable `
                      -Name 'myRouteTableMiManagementService' `
                      -ResourceGroupName $resourceGroupName `
                      -location $location

Set-AzVirtualNetworkSubnetConfig `
                      -VirtualNetwork $virtualNetwork `
                      -Name $miSubnetName `
                      -AddressPrefix $miSubnetAddressPrefix `
                      -NetworkSecurityGroup $networkSecurityGroupMiManagementService `
                      -RouteTable $routeTableMiManagementService | `
                    Set-AzVirtualNetwork

Get-AzNetworkSecurityGroup `
                      -ResourceGroupName $resourceGroupName `
                      -Name "myNetworkSecurityGroupMiManagementService" `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 100 `
                      -Name "allow_management_inbound" `
                      -Access Allow `
                      -Protocol Tcp `
                      -Direction Inbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix * `
                      -DestinationPortRange 9000,9003,1438,1440,1452 `
                      -DestinationAddressPrefix * `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 200 `
                      -Name "allow_misubnet_inbound" `
                      -Access Allow `
                      -Protocol * `
                      -Direction Inbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix $miSubnetAddressPrefix `
                      -DestinationPortRange * `
                      -DestinationAddressPrefix * `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 300 `
                      -Name "allow_health_probe_inbound" `
                      -Access Allow `
                      -Protocol * `
                      -Direction Inbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix AzureLoadBalancer `
                      -DestinationPortRange * `
                      -DestinationAddressPrefix * `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 1000 `
                      -Name "allow_tds_inbound" `
                      -Access Allow `
                      -Protocol Tcp `
                      -Direction Inbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix VirtualNetwork `
                      -DestinationPortRange 1433 `
                      -DestinationAddressPrefix * `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 1100 `
                      -Name "allow_redirect_inbound" `
                      -Access Allow `
                      -Protocol Tcp `
                      -Direction Inbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix VirtualNetwork `
                      -DestinationPortRange 11000-11999 `
                      -DestinationAddressPrefix * `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 4096 `
                      -Name "deny_all_inbound" `
                      -Access Deny `
                      -Protocol * `
                      -Direction Inbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix * `
                      -DestinationPortRange * `
                      -DestinationAddressPrefix * `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 100 `
                      -Name "allow_management_outbound" `
                      -Access Allow `
                      -Protocol Tcp `
                      -Direction Outbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix * `
                      -DestinationPortRange 80,443,12000 `
                      -DestinationAddressPrefix * `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 200 `
                      -Name "allow_misubnet_outbound" `
                      -Access Allow `
                      -Protocol * `
                      -Direction Outbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix * `
                      -DestinationPortRange * `
                      -DestinationAddressPrefix $miSubnetAddressPrefix `
                      | Add-AzNetworkSecurityRuleConfig `
                      -Priority 4096 `
                      -Name "deny_all_outbound" `
                      -Access Deny `
                      -Protocol * `
                      -Direction Outbound `
                      -SourcePortRange * `
                      -SourceAddressPrefix * `
                      -DestinationPortRange * `
                      -DestinationAddressPrefix * `
                      | Set-AzNetworkSecurityGroup


Get-AzRouteTable `
                      -ResourceGroupName $resourceGroupName `
                      -Name "myRouteTableMiManagementService" `
                      | Add-AzRouteConfig `
                      -Name "ToManagedInstanceManagementService" `
                      -AddressPrefix 0.0.0.0/0 `
                      -NextHopType Internet `
                      | Add-AzRouteConfig `
                      -Name "ToLocalClusterNode" `
                      -AddressPrefix $miSubnetAddressPrefix `
                      -NextHopType VnetLocal `
                     | Set-AzRouteTable

# Create managed instance
New-AzSqlInstance -Name $instanceName `
                      -ResourceGroupName $resourceGroupName -Location chinaeast -SubnetId $miSubnetConfigId `
                      -AdministratorCredential (Get-Credential) `
                      -StorageSizeInGB $maxStorage -VCore $vCores -Edition $edition `
                      -ComputeGeneration $computeGeneration -LicenseType $license

# This script will take a minimum of 3 hours to create a new managed instance in a new virtual network. 
# A second managed instance is created much faster.

# Clean up deployment 
# Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。
| [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络 |
| [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | 向虚拟网络添加子网配置 |
| [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Get-AzVirtualNetwork) | 获取资源组中的虚拟网络 |
| [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-AzVirtualNetwork) | 设置虚拟网络的目标状态 |
| [Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | 获取虚拟网络中的子网 |
| [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | 配置虚拟网络中子网配置的目标状态 |
| [New-AzRouteTable](https://docs.microsoft.com/powershell/module/az.network/New-AzRouteTable) | 创建路由表 |
| [Get-AzRouteTable](https://docs.microsoft.com/powershell/module/az.network/Get-AzRouteTable) | 获取路由表 |
| [Set-AzRouteTable](https://docs.microsoft.com/powershell/module/az.network/Set-AzRouteTable) | 设置路由表的目标状态 |
| [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/New-AzSqlInstance) | 创建 Azure SQL 数据库托管实例 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
