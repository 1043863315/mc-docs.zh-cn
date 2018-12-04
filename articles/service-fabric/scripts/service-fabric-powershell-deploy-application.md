---
title: Azure PowerShell 脚本示例 - 将应用程序部署到群集 | Azure
description: Azure PowerShell 脚本示例 - 将应用程序部署到 Service Fabric 群集。
services: service-fabric
documentationcenter: ''
author: rockboyfor
manager: digimobile
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
origin.date: 01/18/2018
ms.date: 03/12/2018
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 9ab631e144db2513fbe3d87b5218797c8d3db50f
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52659333"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>将应用程序部署到 Service Fabric 群集

此示例脚本将应用程序包复制到群集映像存储，并在群集中注册了应用程序类型，同时还删除了不必要的应用程序包，并根据应用程序类型创建了应用程序实例。  如果目标应用程序类型的应用程序清单中定义了任何默认服务，则此时还会创建这些服务。 根据需要自定义参数。 

必要时，使用 [Service Fabric SDK](../service-fabric-get-started.md) 安装 Service Fabric PowerShell 模块。 

## <a name="sample-script"></a>示例脚本

```powershell
# Variables
$endpoint = 'mysftestcluster.chinaeast.cloudapp.chinacloudapi.cn:19000'
$thumbprint = '2779F0BB9A969FB88E04915FFE7955D0389DA7AF'
$packagepath="C:\Users\sfuser\Documents\Visual Studio 2017\Projects\MyApplication\MyApplication\pkg\Release"

# Connect to the cluster using a client certificate.
Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

# Copy the application package to the cluster image store.
Copy-ServiceFabricApplicationPackage $packagepath -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore MyApplication

# Register the application type.
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplication

# Remove the application package to free system resources.
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore MyApplication

# Create the application instance.
New-ServiceFabricApplication -ApplicationName fabric:/MyApplication -ApplicationTypeName MyApplicationType -ApplicationTypeVersion 1.0.0
```

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用[删除应用程序](service-fabric-powershell-remove-application.md)中的脚本删除应用程序实例，取消注册应用程序类型，并从映像存储中删除应用程序包。

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
|[Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| 与 Service Fabric 群集建立连接。 |
|[Copy-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | 将应用程序包复制到群集映像存储。  |
|[Register-ServiceFabricApplicationType](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| 在群集上注册应用程序类型和版本。 |
|[New-ServiceFabricApplication](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| 基于注册的应用程序类型创建应用程序。 |
| [Remove-ServiceFabricApplicationPackage](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | 从映像存储区中删除 Service Fabric 应用程序包。|

## <a name="next-steps"></a>后续步骤

有关 Service Fabric PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/service-fabric/?view=azureservicefabricps)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Powershell 示例。
<!--Update_Description: update meta properties -->