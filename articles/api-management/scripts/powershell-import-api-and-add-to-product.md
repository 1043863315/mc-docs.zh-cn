---
title: Azure PowerShell 脚本示例 - 导入 API
description: Azure PowerShell 脚本示例 - 导入 API
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
origin.date: 11/16/2017
ms.date: 08/13/2018
ms.author: v-yiso
ms.custom: mvc
ms.openlocfilehash: b5639808e2fb2296c1114956b7ffc10300ec4f0b
ms.sourcegitcommit: 98c7d04c66f18b26faae45f2406a2fa6aac39415
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39486893"
---
# <a name="import-an-api"></a>导入 API

此示例脚本导入 API，并将其添加到“API 管理”产品。 

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块版本 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount -Environment AzureChinaCloud` 以创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

```powershell
##########################################################
#  Script to import an API and add it to a Product in api Management 
#  Adding the Imported api to a product is necessary, so that it can be called by a subscription
########################################################### 

$random = (New-Guid).ToString().Substring(0,8)

#Azure specific details
$subscriptionId = "my-azure-subscription-id"

# Api Management service specific details
$apimServiceName = "apim-$random"
$resourceGroupName = "apim-rg-$random"
$location = "China East"
$organisation = "Contoso"
$adminEmail = "admin@contoso.com"

# Api Specific Details
$swaggerUrl = "http://petstore.swagger.io/v2/swagger.json"
$apiPath = "petstore"

# Set the context to the subscription Id where the cluster will be created
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a resource group.
New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

# Create the Api Management service. Since the SKU is not specified, it creates a service with Developer SKU. 
New-AzureRmApiManagement -ResourceGroupName $resourceGroupName -Name $apimServiceName -Location $location -Organization $organisation -AdminEmail $adminEmail

# Create the API Management context
$context = New-AzureRmApiManagementContext -ResourceGroupName $resourceGroupName -ServiceName $serviceName

# import api from Url
$api = Import-AzureRmApiManagementApi -Context $context -SpecificationUrl $swaggerUrl -SpecificationFormat Swagger -Path $apiPath

$productName = "Pet Store Product"
$productDescription = "Product giving access to Petstore api"
$productState = "Published"

# Create a Product to publish the Imported Api. This creates a product with a limit of 10 Subscriptions
$product = New-AzureRmApiManagementProduct -Context $context -Title $productName -Description $productDescription -State $productState -SubscriptionsLimit 10 

# Add the petstore api to the published Product, so that it can be called in developer portal console
Add-AzureRmApiManagementApiToProduct -Context $context -ProductId $product.ProductId -ApiId $api.ApiId
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组和所有相关的资源，可以使用 [Remove-AzureRmResourceGroup](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。

```azurepowershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [PowerShell 示例](../powershell-samples.md)中找到 Azure API 管理的其他 Azure Powershell 示例。
