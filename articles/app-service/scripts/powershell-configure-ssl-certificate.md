---
title: Azure PowerShell 脚本示例 - 将自定义 SSL 证书绑定到 Web 应用 | Azure
description: Azure PowerShell 脚本示例 - 将自定义 SSL 证书绑定到 Web 应用
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
origin.date: 03/20/2017
ms.date: 09/04/2019
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: 316e7849a186edaffa719585355a03e837406ce7
ms.sourcegitcommit: e7dd37e60d0a4a9f458961b6525f99fa0e372c66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555949"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app-using-powershell"></a>使用 PowerShell 将自定义 SSL 证书绑定到 Web 应用

此示例脚本在应用服务中创建一个 Web 应用及其相关资源，并将自定义域名的 SSL 证书绑定到该应用。 

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount -Environment AzureChinaCloud` 创建与 Azure 的连接。 同时，请确保：

- 已使用 `az login` 命令创建与 Azure 的连接。
- 可以访问域注册机构的 DNS 配置页。
- 有要上传和绑定的 SSL 证书的有效 .PFX 文件及其密码。

## <a name="sample-script"></a>示例脚本


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
```powershell
$fqdn="<Replace with your custom domain name>"
$pfxPath="<Replace with path to your .PFX file>"
$pfxPassword="<Replace with your .PFX password>"
$webappname="mywebapp$(Get-Random)"
$location="China North"

# Create a resource group.
New-AzResourceGroup -Name $webappname -Location $location

# Create an App Service plan in Free tier.
New-AzAppServicePlan -Name $webappname -Location $location `
-ResourceGroupName $webappname -Tier Free

# Create a web app.
New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname `
-ResourceGroupName $webappname

Write-Host "Configure a CNAME record that maps $fqdn to $webappname.chinacloudsites.cn"
Read-Host "Press [Enter] key when ready ..."

# Before continuing, go to your DNS configuration UI for your custom domain and follow the 
# instructions at https://aka.ms/appservicecustomdns to configure a CNAME record for the 
# hostname "www" and point it your web app's default domain name.

# Upgrade App Service plan to Basic tier (minimum required by custom SSL certificates)
Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname `
-Tier Basic

# Add a custom domain name to the web app. 
Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `
-HostNames @($fqdn,"$webappname.chinacloudsites.cn")

# Upload and bind the SSL certificate to the web app.
New-AzWebAppSSLBinding -WebAppName $webappname -ResourceGroupName $webappname -Name $fqdn `
-CertificateFilePath $pfxPath -CertificatePassword $pfxPassword -SslState SniEnabled

```
## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzAppServicePlan](https://docs.microsoft.com/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](https://docs.microsoft.com/powershell/module/az.websites/new-azwebapp) | 创建 Web 应用。 |
| [Set-AzAppServicePlan](https://docs.microsoft.com/powershell/module/az.websites/set-azappserviceplan) | 修改应用服务计划以更改其定价层。 |
| [Set-AzWebApp](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) | 修改 Web 应用的配置。 |
| [New-AzWebAppSSLBinding](https://docs.microsoft.com/powershell/module/az.websites/new-azwebappsslbinding) | 为 Web 应用创建 SSL 证书绑定。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
