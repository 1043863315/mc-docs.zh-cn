---
title: 支持 Azure 资源托管标识的 Azure 服务
description: 支持 Azure 资源托管标识和 Azure AD 身份验证的服务列表
services: active-directory
author: MarkusVi
ms.author: v-junlch
origin.date: 06/19/2019
ms.date: 08/05/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5ea721956863c73962353140aa982136fa3091d
ms.sourcegitcommit: 461c7b2e798d0c6f1fe9c43043464080fb8e8246
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68818629"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>支持 Azure 资源托管标识的服务

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 使用托管标识，可对支持 Azure AD 身份验证的任何服务进行身份验证，而无需在代码中插入凭据。 我们正在跨 Azure 将 Azure 资源托管标识与 Azure AD 身份验证集成。 请经常再回来看看，确定这部分内容是否有更新。

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>支持 Azure 资源托管标识的 Azure 服务

以下 Azure 服务支持 Azure 资源托管标识：

### <a name="azure-virtual-machines"></a>Azure 虚拟机

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 可用 | 预览 | 预览 | 预览 | 
| 用户分配 | 预览 | 预览 | 预览 | 预览 |

请参阅以下列表来配置 Azure 虚拟机的托管标识（在可用的区域中）：

- [Azure 门户](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure 虚拟机规模集

|托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 可用 | 预览 | 预览 | 预览 |
| 用户分配 | 预览 | 预览 | 预览 | 预览 |

请参阅以下列表来配置 Azure 虚拟机规模集的托管标识（在可用的区域中）：

- [Azure 门户](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure 应用服务

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 可用 | 可用 | 可用 | 可用 |
| 用户分配 | 预览 | 不可用 | 不可用 | 不可用 |

请参阅以下列表来配置 Azure 应用服务的托管标识（在可用的区域中）：

- [Azure 门户](/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 模板](/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure Functions

托管标识类型 |所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 可用 | 可用 | 可用 | 可用 |
| 用户分配 | 预览 | 不可用 | 不可用 | 不可用 |

请参阅以下列表来配置 Azure Functions 的托管标识（在可用的区域中）：

- [Azure 门户](/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 模板](/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure 逻辑应用

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 预览 | 预览 | 不可用 | 预览 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

请参阅以下列表来配置 Azure 逻辑应用的托管标识（在可用的区域中）：

- [Azure Resource Manager 模板](/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure 数据工厂 V2

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 可用 | 不可用 | 不可用 | 不可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

请参阅以下列表来配置 Azure 数据工厂 V2 的托管标识（在可用的区域中）：

- [Azure 门户](/data-factory/data-factory-service-identity#generate-managed-identity)
- [PowerShell](/data-factory/data-factory-service-identity#generate-managed-identity-using-powershell)
- [REST](/data-factory/data-factory-service-identity#generate-managed-identity-using-rest-api)
- [SDK](/data-factory/data-factory-service-identity#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API 管理

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 可用 | 可用 | 不可用 | 不可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

### <a name="azure-container-registry-tasks"></a>Azure 容器注册表任务

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 可用 | 不可用 | 不可用 | 不可用 |
| 用户分配 | 预览 | 不可用 | 不可用 | 不可用 |

## <a name="azure-services-that-support-azure-ad-authentication"></a>支持 Azure AD 身份验证的 Azure 服务

以下服务支持 Azure AD 身份验证，已通过使用 Azure 资源托管标识的客户端服务进行测试。

### <a name="azure-resource-manager"></a>Azure Resource Manager

请参阅以下列表配置对 Azure 资源管理器的访问权限：

- [通过 Azure 门户分配访问权限](howto-assign-access-portal.md)
- [通过 Powershell 分配访问权限](howto-assign-access-powershell.md)
- [通过 Azure CLI 分配访问权限](howto-assign-access-CLI.md)

| 云 | 资源 ID | 状态 |
|--------|------------|--------|
| Azure 全球 | | 可用 |
| Azure Government | | 可用 |
| Azure 德国 | | 可用 |
| Azure 中国世纪互联 | `https://management.chinacloudapi.cn` | 可用 |

### <a name="azure-key-vault"></a>Azure Key Vault

| 云 | 资源 ID | 状态 |
|--------|------------|--------|
| Azure 全球 | | 可用 |
| Azure Government | | 可用 |
| Azure 德国 |  | 可用 |
| Azure 中国世纪互联 | `https://vault.azure.cn` | 可用 |

### <a name="azure-sql"></a>Azure SQL 

| 云 | 资源 ID | 状态 |
|--------|------------|--------|
| Azure 全球 |   | 可用 |
| Azure Government |  | 可用 |
| Azure 德国 |  | 可用 |
| Azure 中国世纪互联 | `https://database.chinacloudapi.cn/` | 可用 |

### <a name="azure-event-hubs"></a>Azure 事件中心

| 云 | 资源 ID | 状态 |
|--------|------------|--------|
| Azure 全球 | | 预览 |
| Azure Government |  | 不可用 |
| Azure 德国 |   | 不可用 |
| Azure 中国世纪互联 |  | 不可用 |

### <a name="azure-service-bus"></a>Azure 服务总线

| 云 | 资源 ID | 状态 |
|--------|------------|--------|
| Azure 全球 | | 预览 |
| Azure Government |  | 不可用 |
| Azure 德国 |   | 不可用 |
| Azure 中国世纪互联 |  | 不可用 |

### <a name="azure-storage-blobs-and-queues"></a>Azure 存储 blob 和队列

| 云 | 资源 ID | 状态 |
|--------|------------|--------|
| Azure 全球 | `https://storage.azure.com/` | 可用 |
| Azure Government | `https://storage.azure.com/` | 可用 |
| Azure 德国 | `https://storage.azure.com/` | 可用 |
| Azure 中国世纪互联 | `https://storage.azure.com/` | 可用 |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| 云 | 资源 ID | 状态 |
|--------|------------|--------|
| Azure 全球 | | 可用 |
| Azure Government | | 可用 |
| Azure 德国 | | 可用 |
| Azure 中国世纪互联 | `https://*.asazure.chinacloudapi.cn` | 可用 |

