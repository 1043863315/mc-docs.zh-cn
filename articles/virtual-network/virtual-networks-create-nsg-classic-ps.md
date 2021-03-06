---
title: 使用 PowerShell 创建网络安全组（经典）| Azure
description: 了解如何使用 PowerShell 创建和部署网络安全组（经典）
services: virtual-network
documentationcenter: na
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 02/02/2019
ms.date: 01/13/2020
ms.author: v-yeche
ms.openlocfilehash: 37964c52e977f7e0db4a29dd4309037b79196101
ms.sourcegitcommit: bc5f8b4f8ccd7c723f64055825508d1dfcc2162b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75859213"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>使用 PowerShell 创建网络安全组（经典）
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文介绍经典部署模型。 还可以[在 Resource Manager 部署模型中创建 NSG](tutorial-filter-network-traffic.md)。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

下面的示例 PowerShell 命令需要一个已经基于上述方案创建的简单环境。 如果想要运行本文档中所显示的命令，首先通过 [创建 VNet](virtual-networks-create-vnet-classic-netcfg-ps.md)构建测试环境。

## <a name="create-an-nsg-for-the-front-end-subnet"></a>为前端子网创建 NSG

1. 如果从未用过 Azure PowerShell，请参阅[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。

2. 创建一个名为 *NSG-FrontEnd* 的网络安全组：

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location chinanorth `
      -Label "Front end subnet NSG"
    ```

3. 创建一个允许从 Internet 访问端口 3389 的安全规则：

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
    ```

4. 创建一个允许从 Internet 访问端口 80 的安全规则：

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```
5. 将网络安全组与子网相关联：

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-Frontend" `
    | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName "TestVNet" `
    -Subnet "FrontEnd"
    ```
## <a name="create-an-nsg-for-the-back-end-subnet"></a>为后端子网创建 NSG

1. 创建一个名为 *NSG-BackEnd* 的网络安全组：

    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location chinanorth `
      -Label "Back end subnet NSG"
    ```

2. 创建一个允许从前端子网访问端口 1433（SQL Server 使用的默认端口）的安全规则：

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. 创建一个阻止从子网访问 Internet 的安全规则：

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```
4. 将网络安全组与子网相关联：
    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-Backend" `
    | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName "TestVNet" `
    -Subnet "BackEnd"
    ```

<!-- Update_Description: update meta properties, wording update, update link -->