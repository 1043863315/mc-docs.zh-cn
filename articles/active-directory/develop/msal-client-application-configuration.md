---
title: 客户端应用程序配置（Microsoft 身份验证库）
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 身份验证库 (MSAL) 中公共客户端和机密客户端应用程序的配置选项。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 09/27/2019
ms.date: 11/05/2019
ms.author: v-junlch
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 086a61c91705f655435d75574d45d16362b9972f
ms.sourcegitcommit: 3c98f52b6ccca469e598d327cd537caab2fde83f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79291077"
---
# <a name="application-configuration-options"></a>应用程序配置选项

在代码中，可以初始化新的公共或机密客户端应用程序（或 MSAL.js 的用户代理）以进行身份验证并获取令牌。 初始化 Microsoft 身份验证库 (MSAL) 中的客户端应用程序时，可以设置许多的配置选项。 这些选项划分为两组：

- 注册选项，包括：
    - [颁发机构](#authority)（由标识提供者[实例](#cloud-instance)、应用的登录[受众](#application-audience)以及可能的租户 ID 构成）。
    - [客户端 ID](#client-id)。
    - [重定向 URI](#redirect-uri)。
    - [客户端机密](#client-secret)（用于机密客户端应用程序）。
- [日志记录选项](#logging)，包括日志级别，以及使用该库的组件的名称。

## <a name="authority"></a>颁发机构

颁发机构是一个 URL，表示 MSAL 可从中请求令牌的目录。 常见的颁发机构包括：

- https\://login.partner.microsoftonline.cn/\<tenant\>/，其中，&lt;tenant&gt; 是 Azure Active Directory (Azure AD) 租户的租户 ID，或者与此 Azure AD 租户关联的域。 仅用于将特定组织的用户登录。
- https\://login.partner.microsoftonline.cn/common/。 用于通过工作和学校帐户将用户登录。
- https\://login.partner.microsoftonline.cn/organizations/。 用于通过工作和学校帐户将用户登录。

颁发机构设置需要与在门户中声明的设置一致。

颁发机构 URL 由实例和受众构成。

颁发机构可以是：
- Azure AD 云颁发机构。
- Azure AD B2C 颁发机构。 请参阅 [B2C 细节](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)。
- Active Directory 联合身份验证服务 (AD FS) 颁发机构。 请参阅 [AD FS support](https://aka.ms/msal-net-adfs-support)（AD FS 支持）。

Azure AD 云颁发机构有两个组成部分：
- 标识提供者实例 
- 应用的登录受众 

可将实例和受众连接到一起，以颁发机构 URL 的形式提供。 在 MSAL 3.*x* 以前的 MSAL.NET 版本中，必须根据想要面向的云和登录受众自行构成颁发机构。  下图显示了颁发机构 URL 的构成方式：

![如何构成颁发机构 URL](./media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>云实例

实例用于指定应用是要从 Azure 公有云还是国家/地区云将用户登录。  在代码中使用 MSAL 可以通过枚举或者将 URL 作为 `Instance` 成员（如果知道该成员）传递给[国家/地区云实例](authentication-national-cloud.md#azure-ad-authentication-endpoints)，来设置 Azure 云实例。

如果同时指定 `Instance` 和 `AzureCloudInstance`，MSAL.NET 将引发显式异常。

如果未指定实例，应用将面向 Azure 公有云实例（URL `https://login.onpartner.microsoftonline.cn` 的实例）。

## <a name="application-audience"></a>应用程序受众

登录受众取决于应用的业务需求：
- 业务线应用程序 (LOB) 开发人员也许会生成一个只在其组织中使用的单租户应用程序。 在这种情况下，需要按租户 ID（Azure AD 实例的 ID）或者与 Azure AD 实例关联的域名指定组织。
- ISV 可能想要使用任一组织或某些组织（多租户应用）中的用户工作帐户和学校帐户将用户登录。 

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>如何在代码/配置中指定受众

在代码中使用 MSAL 时，可以使用以下值之一指定受众：
- Azure AD 颁发机构受众枚举
- 租户 ID，可以是：
  - 单租户应用程序的 GUID（Azure AD 实例的 ID）
  - 与 Azure AD 实例关联的域名（也适用于单租户应用程序）
- 以下租户 ID 占位符之一代表 Azure AD 颁发机构受众枚举：
    - 多租户应用程序的 `organizations`
    - 用于通过工作和学校帐户将用户登录的 `common`

如果你同时指定 Azure AD 颁发机构受众和租户 ID，MSAL 将引发有含义的异常。

如果你未指定受众，应用将面向充当受众的 Azure AD。 （也就是说，它的行为与指定了 `common` 时一样。）

### <a name="effective-audience"></a>有效的受众

应用程序的有效受众是在应用中设置的受众与在应用注册中指定的受众之间的最小值（如果存在交集）。 实际上，可以在[应用注册](https://portal.azure.cn/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)体验中指定应用的受众（支持的帐户类型）。 有关详细信息，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。

## <a name="client-id"></a>客户端 ID

客户端 ID 是注册应用时由 Azure AD 分配给应用的唯一应用程序（客户端）ID。

## <a name="redirect-uri"></a>重定向 URI

重定向 URI 是标识提供者将安全令牌发回到的 URI。

### <a name="redirect-uri-for-public-client-apps"></a>公共客户端应用的重定向 URI

如果你是使用 MSAL 的公共客户端应用开发人员：
- 可能需要在桌面或 UWP 应用程序 (MSAL.NET 4.1+) 中使用 `.WithDefaultRedirectUri()`。 此方法会将公共客户端应用程序的重定向 URI 属性设置为建议用于公共客户端应用程序的默认重定向 URI。 

  平台  | 重定向 URI  
  ---------  | --------------
  桌面应用 (.NET FW) | `https://login.partner.microsoftonline.cn/common/oauth2/nativeclient` 
  UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` 的值。 这可以通过浏览器启用 SSO，方法是：将值设置为需注册的 WebAuthenticationBroker.GetCurrentApplicationCallbackUri() 的结果
  .NET Core | `https://localhost`。 这样用户就可以将系统浏览器用于交互式身份验证，因为 .NET Core 目前没有 UI 用于嵌入式 Web 视图。

- 若要构建不支持代理的 Xamarin Android 和 iOS 应用程序，则不需添加重定向 URI（对于 Xamarin Android 和 iOS，重定向 URI 会自动设置为 `msal{ClientId}://auth`

- 需要在[应用注册](https://portal.azure.cn/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)中配置重定向 URI：

   ![应用注册中的重定向 URI](./media/msal-client-application-configuration/redirect-uri.png)

可以使用 `RedirectUri` 属性替代重定向 URI（例如，如果使用代理）。 下面是该方案的重定向 URI 的一些示例：

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

有关更多 iOS 详细信息，请参阅[将使用 Microsoft Authenticator 的 iOS 应用程序从 ADAL.NET 迁移到 MSAL.NET](msal-net-migration-ios-broker.md) 和[在 iOS 上利用中转站](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)。
有关更多 Android 详细信息，请参阅 [Android 中的代理身份验证](brokered-auth.md)。

### <a name="redirect-uri-for-confidential-client-apps"></a>机密客户端应用的重定向 URI

对于 Web 应用，重定向 URI（或回复 URI）是 Azure AD 用来向应用程序发回令牌的 URI。 如果机密应用是 Web 应用/Web API，则此 URI 可以是该 Web 应用/Web API 的 URL。 重定向 URI 需在应用注册中注册。 部署一个最初已在本地测试的应用时，这种注册尤其重要。 然后，需要在门户中添加已部署的应用的回复 URL。

对于守护程序应用，不需要指定重定向 URI。

## <a name="client-secret"></a>客户端机密

此选项指定机密客户端应用的客户端机密。 在使用 PowerShell AzureAD、PowerShell AzureRM 或 Azure CLI 注册应用期间，此机密（应用密码）将提供给 Azure AD。

## <a name="logging"></a>日志记录

其他配置选项可用于日志记录和故障排除。 有关其用法的详细信息，请参阅[日志记录](msal-logging.md)一文。

## <a name="next-steps"></a>后续步骤

了解如何[使用 MSAL.NET 实例化客户端应用程序](msal-net-initializing-client-applications.md)。
了解如何[使用 MSAL.js 实例化客户端应用程序](msal-js-initializing-client-applications.md)。

<!-- Update_Description: wording update -->