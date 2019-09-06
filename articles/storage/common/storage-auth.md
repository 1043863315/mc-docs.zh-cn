---
title: 授权访问 Azure 存储 | Microsoft Docs
description: 了解授权访问 Azure 存储的各种方式，包括 Azure Active Directory、共享密钥身份验证或共享访问签名。
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 03/21/2019
ms.date: 09/09/2019
ms.author: v-jay
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 38655a913957604b38a32090d98a4a6af5cff628
ms.sourcegitcommit: 66a77af2fab8a5f5b34723dc99e4d7ce0c380e78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2019
ms.locfileid: "70209388"
---
# <a name="authorizing-access-to-azure-storage"></a>授权访问 Azure 存储

每次访问存储帐户中的数据时，客户端都会通过 HTTP/HTTPS 向 Azure 存储发出请求。 每个对安全资源的请求都必须经过授权，以便服务确保客户端具有访问数据所需的权限。

下表介绍了多个选项，Azure 存储提供这些选项，用于授权对资源的访问：

|  |共享密钥（存储帐户密钥）  |共享访问签名 (SAS)  |Azure Active Directory (Azure AD)  |匿名公共读取访问权限  |
|---------|---------|---------|---------|---------|
|Azure Blob     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[支持](storage-sas-overview.md)         |[支持](storage-auth-aad.md)         |[支持](../blobs/storage-manage-access-to-resources.md)         |
|Azure 文件存储 (SMB)     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |不支持         |不支持         |不支持         |
|Azure 文件存储 (REST)     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[支持](storage-sas-overview.md)         |不支持         |不支持         |
|Azure 队列     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[支持](storage-sas-overview.md)         |[支持](storage-auth-aad.md)         |不支持         |
|Azure 表     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[支持](storage-sas-overview.md)         |不支持         |不支持         |

下面简要介绍每个授权选项：

- 用于 Blob 和队列的 **Azure Active Directory (Azure AD) 集成**。 Azure AD 提供基于角色的访问控制 (RBAC)，用于精细地控制客户端对存储帐户中资源的访问权限。 若要详细了解用于 Blob 的 Azure AD 集成，请参阅[使用 Azure Active Directory 对 Azure 存储请求进行身份验证](storage-auth-aad.md)。
- 用于 blob、文件、队列和表的**共享密钥授权**。 使用共享密钥的客户端会随使用存储帐户访问密钥签名的每个请求传递一个标头。 有关详细信息，请参阅[通过共享密钥进行授权](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)。
- 用于 blob、文件、队列和表的**共享访问签名**。 共享访问签名 (SAS) 针对存储帐户中的资源提供有限的委托访问权限。 通过对签名的有效时间间隔或对它授予的权限添加约束，可灵活地管理访问权限。 有关详细信息，请参阅[使用共享访问签名 (SAS)](storage-sas-overview.md)。
- 用于容器和 blob 的**匿名公共读取访问**。 无需授权。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../blobs/storage-manage-access-to-resources.md)。  

默认情况下，Azure 存储中的所有资源都受到保护，并且只能由帐户所有者使用。 虽然你可以使用上述任何授权策略为客户端授予访问存储帐户资源的权限，但 Microsoft 建议尽可能使用 Azure AD，以获得最大的安全性和易用性。 
