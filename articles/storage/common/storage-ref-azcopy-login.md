---
title: azcopy login | Microsoft Docs
description: 本文提供有关 azcopy login 命令的参考信息。
author: WenJason
ms.service: storage
ms.topic: reference
origin.date: 10/16/2019
ms.date: 02/10/2020
ms.author: v-jay
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d5d7bf9bc96a162825905f4875e6c1ae86e0115f
ms.sourcegitcommit: 5c4141f30975f504afc85299e70dfa2abd92bea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77028933"
---
# <a name="azcopy-login"></a>azcopy login

登录到 Azure Active Directory 以访问 Azure 存储资源。

## <a name="synopsis"></a>摘要

登录到 Azure Active Directory 以访问 Azure 存储资源。

若要获得访问 Azure 存储帐户的授权，必须在存储帐户、父资源组或父订阅的上下文中为用户帐户分配“存储 Blob 数据参与者”角色。 

此命令会通过 OS 内置机制缓存当前用户的已加密登录信息。

有关详细信息，请参阅示例。

> [!IMPORTANT]
> 如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑从命令行历史记录中清除包含凭据的变量。 要防止变量出现在历史记录中，可以使用脚本提示用户输入凭据，并设置环境变量。

```azcopy
azcopy login [flags] --aad-endpoint https://login.partner.microsoftonline.cn
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

将默认的 AAD 租户 ID 设置为常用 ID，以交互方式登录：

```azcopy
azcopy login --aad-endpoint https://login.partner.microsoftonline.cn
```

使用指定的租户 ID 以交互方式登录：

```azcopy
azcopy login --tenant-id "[TenantID]" --aad-endpoint https://login.partner.microsoftonline.cn
```

使用虚拟机 (VM) 的系统分配标识登录：

```azcopy
azcopy login --identity --aad-endpoint https://login.partner.microsoftonline.cn
```

使用 VM 的用户分配标识和服务标识的客户端 ID 登录：

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]" --aad-endpoint https://login.partner.microsoftonline.cn
```

使用 VM 的用户分配标识和服务标识的对象 ID 登录：

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]" --aad-endpoint https://login.partner.microsoftonline.cn
```

使用 VM 的用户分配标识和服务标识的资源 ID 登录：

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID" --aad-endpoint https://login.partner.microsoftonline.cn
```

使用客户端机密以服务主体身份登录。 将环境变量 AZCOPY_SPA_CLIENT_SECRET 设置为客户端机密，以便进行基于机密的服务主体身份验证。

```azcopy
azcopy login --service-principal --aad-endpoint https://login.partner.microsoftonline.cn
```

使用证书和密码以服务主体身份登录。 将环境变量 AZCOPY_SPA_CERT_PASSWORD 设置为证书的密码，以便进行基于证书的服务主体授权。

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --aad-endpoint https://login.partner.microsoftonline.cn
```

确保将 /path/to/my/cert 作为 PEM 或 PKCS12 文件的路径处理。 AzCopy 不会到系统证书存储中获取你的证书。

进行基于证书的服务主体身份验证时，--certificate-path 是必需的。

## <a name="options"></a>选项

|选项|说明|
|--|--|
|--aad-endpoint|要使用的 Azure Active Directory 终结点。 默认值 (https://login.microsoftonline.com) 适用于公共 Azure 云。 在国家云中进行身份验证时设置此参数。 请参阅 [Azure AD 身份验证终结点](/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)。
托管服务标识不需要此标志。|
|--application-id string|用户分配标识的应用程序 ID。 服务主体身份验证所需。|
|--certificate-path string|用于 SPN 身份验证的证书的路径。 基于证书的服务主体身份验证所需。|
|-h、--help|显示 login 命令的帮助内容。|
|--identity|使用虚拟机的标识（也称托管服务标识 (MSI)）登录。|
|--identity-client-id string|用户分配标识的客户端 ID。|
|--identity-object-id string|用户分配标识的对象 ID。|
|--identity-resource-id string|用户分配标识的资源 ID。|
|--service-principal|使用证书或机密通过 SPN（服务主体名称）登录。 客户端机密或证书密码必须置于相应的环境变量中。 键入 `AzCopy env` 即可查看环境变量的名称和说明。|
|--tenant-id string| Azure Active Directory 目录 ID，用于 OAuth 设备交互式登录。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
