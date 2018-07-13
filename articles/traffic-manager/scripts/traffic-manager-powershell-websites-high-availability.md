---
title: Azure PowerShell 脚本示例 - 为实现应用程序的高可用性路由流量 | Azure
description: Azure PowerShell 脚本示例 - 为实现应用程序的高可用性路由流量
services: traffic-manager
documentationcenter: traffic-manager
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
origin.date: 04/26/2018
ms.date: 07/09/2018
ms.author: v-yeche
ms.openlocfilehash: 74d157e54e8b8a100153d6dea09acadaf12d17c8
ms.sourcegitcommit: 037a777484c32657c30778c14c27c14db36d39c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37799987"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>使用 Azure PowerShell 为实现应用程序的高可用性路由流量

此脚本将创建一个资源组、两个应用服务计划、两个 Web 应用、一个流量管理器配置文件和两个流量管理器终结点。 流量管理器将流量引导到一个区域（作为主要区域）中的应用程序；主要区域中的应用程序不可用时，引导到次要区域。 执行脚本前，必须将 MyWebApp、MyWebAppL1 和 MyWebAppL2 值更改为 Azure 内的唯一值。 运行脚本后，可以使用 URL mywebapp.trafficmanager.cn 访问主要区域中的应用。

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)中的说明安装 Azure PowerShell，并运行 `Connect-AzureRmAccount -Environment AzureChinaCloud` 创建与 Azure 的连接。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Variables for common values
$rgName1="MyResourceGroup1"
$rgName2="MyResourceGroup2"
$location1="chinaeast"
$location2="chinanorth"

# The values of the variables below must be unique (replace with your own names).
$webApp1="mywebapp$(Get-Random)"
$webApp2="mywebapp$(Get-Random)"
$webAppL1="MyWebAppL1"
$webAppL2="MyWebAppL2"

# Create a resource group in location one.
New-AzureRmResourceGroup -Name $rgName1 -Location $location1

# Create a resource group in location two.
New-AzureRmResourceGroup -Name $rgName2 -Location $location2

# Create a website deployed from GitHub in both regions (replace with your own GitHub URL).
$gitrepo="https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git"

# Create a hosting plan and website and deploy it in location one (requires Standard 1 minimum SKU).

$appServicePlan = New-AzureRmAppServicePlan -Name $webappl1 -ResourceGroupName $rgName1 `
  -Location $location1 -Tier Standard 

$web1 = New-AzureRmWebApp -ResourceGroupName $rgname1 -Name $webApp1 -Location $location1 `
  -AppServicePlan $webappl1

# Configure GitHub deployment from your GitHub repo and deploy once.
$PropertiesObject = @{
    repoUrl = "$gitrepo";
    branch = "master";
    isManualIntegration = "true";
}

Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName $rgname1 `
-ResourceType Microsoft.Web/sites/sourcecontrols -ResourceName $webapp1/web `
-ApiVersion 2015-08-01 -Force

# Create a hosting plan and website and deploy it in location two (requires Standard 1 minimum SKU).

$appServicePlan = New-AzureRmAppServicePlan -Name $webappl2 -ResourceGroupName $rgName2 `
  -Location $location2 -Tier Standard 

$web2 = New-AzureRmWebApp -ResourceGroupName $rgname2 -Name $webApp2 `
  -Location $location2 -AppServicePlan $webappl2

$PropertiesObject = @{
    repoUrl = "$gitrepo";
    branch = "master";
    isManualIntegration = "true";
}

Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName $rgname2 `
  -ResourceType Microsoft.Web/sites/sourcecontrols -ResourceName $webapp2/web `
  -ApiVersion 2015-08-01 -Force

# Create a Traffic Manager profile.
$tm = New-AzureRmTrafficManagerProfile -Name 'MyTrafficManagerProfile' -ResourceGroupName $rgname1 `
  -TrafficRoutingMethod Priority -RelativeDnsName $web1.Name -Ttl 60 `
  -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath /

# Create an endpoint for the location one website deployment and set it as the priority target.
$endpoint = New-AzureRmTrafficManagerEndpoint -Name 'MyEndPoint1' -ProfileName $tm.Name `
  -ResourceGroupName $rgname1 -Type AzureEndpoints -Priority 1 `
  -TargetResourceId $web1.Id -EndpointStatus Enabled

# Create an endpoint for the location two website deployment and set it as the secondary target.
$endpoint2 = New-AzureRmTrafficManagerEndpoint -Name 'MyEndPoint2' -ProfileName $tm.Name `
  -ResourceGroupName $rgname1 -Type AzureEndpoints -Priority 2 `
  -TargetResourceId $web2.Id -EndpointStatus Enabled

```
<!-- Notice: Line 93 should be $web1.Name NOT $web1.SiteName-->

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup1
Remove-AzureRmResourceGroup -Name myResourceGroup2
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用、流量管理器配置文件和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)  | 创建用于存储所有资源的资源组。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/module/azurerm.websites/new-azurermappserviceplan) | 创建应用服务计划。 这与 Azure Web 应用的服务器场类似。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/module/azurerm.websites/new-azurermwebapp) | 创建应用服务计划中的 Azure Web 应用。 |
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresource) | 创建应用服务计划中的 Azure Web 应用。 |
| [New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile) | 创建 Azure 流量管理器配置文件。 |
| [New-AzureRmTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerendpoint) | 将终结点添加到 Azure 流量管理器配置文件。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可在 [Azure 网络概述文档](../powershell-samples.md?toc=%2fnetworking%2ftoc.json)中找到其他网络 PowerShell 脚本示例。
<!-- Update_Description: new articles on traffic manager powershell websites high availability -->
<!--ms.date: 07/09/2018-->