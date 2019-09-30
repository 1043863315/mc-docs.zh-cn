---
title: Azure Service Fabric - 将现有的 Azure Service Fabric 群集配置为启用托管标识支持 | Azure
description: 本文介绍如何将现有的 Azure Service Fabric 群集配置为启用托管标识支持
services: service-fabric
author: rockboyfor
ms.service: service-fabric
ms.topic: article
origin.date: 07/25/2019
ms.date: 09/30/2019
ms.author: v-yeche
ms.openlocfilehash: ed2d7d18f1cc81e169520cde1fc68206c325437f
ms.sourcegitcommit: 332ae4986f49c2e63bd781685dd3e0d49c696456
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71340784"
---
<!--Pending for Verify-->
<!--Verify successfully with New Deployment-->

# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>将现有的 Azure Service Fabric 群集配置为启用托管标识支持（预览版）
若要访问 Azure Service Fabric 应用程序的托管标识功能，必须先在群集上启用**托管标识令牌服务**。 此服务负责使用 Service Fabric 应用程序的托管标识对这些应用程序进行身份验证，以及代表它们获取访问令牌。 启用此服务以后，即可在 Service Fabric Explorer 中左侧窗格的“系统”部分  看到它，它以 **fabric:/System/ManagedIdentityTokenService** 名称运行。

> [!NOTE]
> 若要启用**托管标识令牌服务**，必须使用 Service Fabric 运行时 6.5.658.9590 或更高版本。  
> 
> 可以在 Azure 门户中查找 Service Fabric 版群集，方法是：打开群集资源，然后在“基本信息”部分查找“Service Fabric 版本”属性。  
> 
> 如果群集处于“手动”升级模式，则需先将其升级到  6.5.658.9590 或更高版本。

## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>在现有群集中启用托管标识令牌服务
若要在现有群集中启用托管标识令牌服务，需启动群集升级并指定两项更改：启用托管标识令牌服务，以及请求重启每个节点。 为此，请在 Azure 资源管理器模板中添加下述两个代码片段：

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

若要让更改生效，还需更改升级策略，指定在升级进展到群集时，在每个节点上以强制方式重启 Service Fabric 运行时。 此重启确保新启用的系统服务在每个节点上启动并运行。 在下面的代码片段中，`forceRestart` 是基本设置；请对余下设置使用现有值。  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> 成功完成升级以后，请勿忘记回退 `forceRestart` 设置，尽量减少后续升级的影响。 

## <a name="errors-and-troubleshooting"></a>错误和故障排除

如果部署失败并出现以下错误，则表明群集运行时所在的 Service Fabric 的版本不够高：

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>后续步骤
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [从服务代码中利用 Service Fabric 应用程序的托管标识](./how-to-managed-identity-service-fabric-app-code.md)
* [为 Azure Service Fabric 应用程序授予对其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)

<!--Update_Description: new articles on configure existing cluster enalbed managed identity token service-->
<!--new.date: 09/02/2019-->