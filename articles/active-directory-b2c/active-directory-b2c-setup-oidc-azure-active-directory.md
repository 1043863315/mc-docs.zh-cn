---
title: 为 Azure Active Directory 组织设置登录 - Azure Active Directory B2C
description: 在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
origin.date: 08/08/2019
ms.date: 11/11/2019
ms.author: v-junlch
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 23e1ac3c4aea7522135dbf4a1d51c311bb12db3c
ms.sourcegitcommit: 40a58a8b9be0c825c03725802e21ed47724aa7d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73934371"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中设置登录特定 Azure Active Directory 组织

若要将 Azure Active Directory (Azure AD) 用作 Azure AD B2C 中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要创建一个表示它的应用程序。 本文介绍如何使用 Azure AD B2C 中的用户流为特定 Azure AD 组织中的用户启用登录。

## <a name="create-an-azure-ad-app"></a>创建 Azure AD 应用

若要让用户从特定的 Azure AD 组织登录，需要在与 Azure AD B2C 租户不同的组织 Azure AD 租户中注册一个应用程序。

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 请确保使用的是包含 Azure AD 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD 租户的目录  。 此租户与 Azure AD B2C 租户不同。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”   。
4. 选择“新注册”。 
5. 输入应用程序的名称。 例如，`Azure AD B2C App`。
6. 对于此应用程序，接受选择“仅此组织目录中的帐户”  。
7. 对于“重定向 URI”  ，接受值 **Web**，并以全小写字母输入以下 URL，其中 `your-B2C-tenant-name` 将替换为 Azure AD B2C 租户的名称。 例如，`https://fabrikam.b2clogin.cn/fabrikam.partner.onmschina.cn/oauth2/authresp`：

    ```
    https://your-B2C-tenant-name.b2clogin.cn/your-B2C-tenant-name.partner.onmschina.cn/oauth2/authresp
    ```

    现在，所有 URL 都应使用 [b2clogin.cn](b2clogin.md)。

8. 单击“注册”  。 复制“应用程序(客户端) ID”供以后使用  。
9. 在应用程序菜单中依次选择“证书和机密”、“新建客户端机密”。  
10. 输入客户端密码的名称。 例如，`Azure AD B2C App Secret`。
11. 选择到期期限。 对于此应用程序，接受选项“1 年内”  。
12. 选择“添加”  ，然后复制显示的新客户端密码的值，以便以后使用。

## <a name="configure-azure-ad-as-an-identity-provider"></a>将 Azure AD 配置为标识提供者

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录  。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
1. 选择“标识提供者”  ，然后选择“新建 OpenID Connect 提供程序”  。
1. 输入“名称”  。 例如，输入“Contoso Azure AD”  。
1. 对于“元数据 URL”  ，请输入以下 URL，将 `your-AD-tenant-domain` 替换为 Azure AD 租户的域名：

    ```
    https://login.partner.microsoftonline.cn/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如，`https://login.partner.microsoftonline.cn/contoso.partner.onmschina.cn/.well-known/openid-configuration`。

    **请勿**使用 Azure AD v2.0 元数据终结点，例如 `https://login.partner.microsoftonline.cn/contoso.partner.onmschina.cn/v2.0/.well-known/openid-configuration`。 这样做会导致在尝试登录时出现类似于 `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.partner.onmschina.cn'` 的错误。

1. 对于“客户端 ID”，请输入之前记录的应用程序 ID  。
1. 对于“客户端机密”，请输入之前记录的客户端机密  。
1. 保留“范围”  、“响应类型”和“响应模式”   的默认值。
1. （可选）输入  Domain_hint 的值。 例如，*ContosoAD*。 在请求中使用 domain_hint 引用此标识提供者时会用到该值  。
1. 在“标识提供者声明映射”  下，输入以下声明映射值：

    * **用户 ID**：*oid*
    * **显示名称**：*name*
    * **名字**：*given_name*
    * **姓氏**：*family_name*
    * **电子邮件**：*unique_name*

1. 选择“保存”  。

<!-- Update_Description: wording update -->