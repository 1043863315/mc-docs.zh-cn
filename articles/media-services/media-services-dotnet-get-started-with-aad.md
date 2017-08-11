---
title: "通过 Azure AD 身份验证使用 .NET 访问 Azure 媒体服务 API | Azure"
description: "本主题介绍如何使用 Azure Active Directory (Azure AD) 身份验证通过 .NET 访问 Azure 媒体服务 (AMS) API。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 06/17/2017
ms.date: 08/07/2017
ms.author: v-haiqya
ms.openlocfilehash: cf81e8aeb95ef7a3738f3f621a2ba31c563d458b
ms.sourcegitcommit: dc2d05f1b67f4988ef28a0931e6e38712f4492af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2017
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>使用 Azure AD 身份验证通过 .NET 访问 Azure 媒体服务 API

从 windowsazure.mediaservices 4.0.0.4 开始，Azure 媒体服务支持基于 Azure Active Directory (Azure AD) 的身份验证。 本主题介绍如何使用 Azure AD 身份验证通过 Microsoft .NET 访问 Azure 媒体服务 API。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 有关详细信息，请参阅 [Azure 试用](https://www.azure.cn/pricing/1rmb-trial/)。 
- 一个媒体服务帐户。 有关详细信息，请参阅[通过使用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。
- 最新的 [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) 包。
- 熟悉主题[使用 AAD 身份验证访问 Azure 媒体服务 API 概述](media-services-use-aad-auth-to-access-ams-api.md)。 

通过 Azure 媒体服务使用 Azure AD 身份验证时，可以通过以下两种方式之一进行身份验证：

- **用户身份验证**：对使用应用程序与 Azure 媒体服务资源进行交互的人员执行身份验证。 交互式应用程序应首先提示用户输入凭据。 举个例子，授权用户用来监视编码作业或实时传送视频流的管理控制台应用程序。 
- **服务主体身份验证**：对服务进行身份验证。 通常使用此身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用：如 Web 应用、函数应用、逻辑应用、 API 或微服务。

>[!IMPORTANT]
>Azure 媒体服务目前支持 Azure 访问控制服务身份验证模型。 但是，访问控制授权将于 2018 年 6 月 1 日弃用。 建议尽快迁移到 Azure Active Directory 身份验证模型。

## <a name="get-an-azure-ad-access-token"></a>获取 Azure AD 访问令牌

要通过 Azure AD 身份验证连接到 Azure 媒体服务 API，客户端应用程序需要请求 Azure AD 访问令牌。 使用媒体服务 .NET 客户端 SDK 时，有关如何获取 Azure AD 访问令牌的诸多详细信息将在 [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) 和 [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) 类中进行包装和简化。 

例如，无需提供 Azure AD 主管机构、媒体服务资源 URI 或本机 Azure AD 应用程序详细信息。 这些是已由 Azure AD 访问令牌提供程序类配置的已知值。 

如果不使用 Azure 媒体服务 .NET SDK，我们建议使用 [Azure AD 身份验证库](../active-directory/develop/active-directory-authentication-libraries.md)。 要获取用于 Azure AD 身份验证库所需的参数的值，请参阅[使用 Azure 门户访问 Azure AD 身份验证设置](media-services-portal-get-started-with-aad.md)。

还可以选择将 **AzureAdTokenProvider** 的默认实现方式替换为自己的实现方式。

## <a name="install-and-configure-azure-media-services-net-sdk"></a>安装和配置 Azure 媒体服务 .NET SDK

>[!NOTE] 
>要将 Azure AD 身份验证用于媒体服务 .NET SDK，需要有最新的 [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) 包。 此外，将引用添加到 **Microsoft.IdentityModel.Clients.ActiveDirectory** 程序集。 如果使用的是现有应用，则加入 **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** 程序集。 

1. 在 Visual Studio 中创建新的 C# 控制台应用程序。
2. 使用 [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet 包安装 **Azure 媒体服务 .NET SDK**。 

    若要使用 NuGet 添加引用，请执行以下步骤：在“解决方案资源管理器”中，右键单击项目名称，并选择“管理 NuGet 包”。 然后搜索 **windowsazure.mediaservices**，并选择“安装”。

    -或-

    在 Visual Studio 的“包管理器控制台”中运行以下命令。

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. 将 **using** 添加到源代码中。

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>使用用户身份验证

若要通过使用用户身份验证选项连接到 Azure 媒体服务 API，客户端应用程序需要使用以下参数请求 Azure AD 令牌：  

- Azure AD 租户终结点。 可以在 Azure 门户中检索租户信息。 将鼠标悬停在右上角的已登录用户上。
- 媒体服务资源 URI。
- 媒体服务（本机）应用程序客户端 ID。 
- 媒体服务（本机）应用程序重定向 URI。 

这些参数的值可在 **AzureEnvironments.AzureCloudEnvironment** 中找到。 **AzureEnvironments.AzureCloudEnvironment** 常量是 .NET SDK 中的一个帮助程序，可以为公共 Azure 数据中心获取正确的环境变量设置。 

它包含预定义的环境设置，从而仅允许访问公共数据中心中的媒体服务。 在中国云区域，可以使用 **AzureChinaCloudEnvironment**。

以下示例代码创建一个令牌：

    var tokenCredentials = new AzureAdTokenCredentials("microsoft.partner.onmschina.cn", AzureEnvironments.AzureChinaCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

若要开始针对媒体服务编程，需要创建一个代表服务器上下文的 **CloudMediaContext** 实例。 **CloudMediaContext** 包括对各种重要集合的引用，这些集合包括作业、资产、文件、访问策略和定位符。 

此外，还需要将**媒体 REST 服务的资源 URI** 传递到 **CloudMediaContext** 构造函数。 要获取媒体 REST 服务的资源 URI，请登录到 Azure 门户，选择 Azure 媒体服务帐户，并依次选择“API 访问权限”、“通过用户身份验证连接到 Azure 媒体服务”。 

下面的代码示例创建 **CloudMediaContext** 实例：

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

以下示例介绍如何创建 Azure AD 令牌和上下文：

    namespace AADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.partner.onmschina.cn".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR AAD TENANT DOMAIN HERE}", AzureEnvironments.AzureChinaCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                // Specify your REST API endpoint, for example "https://accountname.restv2.chinaeast.media.chinacloudapi.cn/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }

        }
    }

>[!NOTE]
>如果收到异常，指示“远程服务器返回了一个错误: (401)未授权”，请参阅使用 Azure AD身份验证访问 Azure 媒体服务 API 概述的[访问控制](media-services-use-aad-auth-to-access-ams-api.md#access-control)部分。

## <a name="use-service-principal-authentication"></a>使用服务主体身份验证

若要通过服务主体选项连接到 Azure 媒体服务 API，中间层应用程序（Web API 或 Web 应用程序）需要使用以下参数请求 Azure AD 令牌：  

- Azure AD 租户终结点。 可以在 Azure 门户中检索租户信息。 将鼠标悬停在右上角的已登录用户上。
- 媒体服务资源 URI。
- Azure AD 应用程序值：“客户端 ID”和“客户端机密”。

“客户端 ID”和“客户端机密”参数的值都可以在 Azure 门户中找到。 有关详细信息，请参阅[使用 Azure 门户进行 Azure AD 身份验证入门](media-services-portal-get-started-with-aad.md)。

以下代码示例使用将 **AzureAdClientSymmetricKey** 作为参数的 **AzureAdTokenCredentials** 构造函数创建令牌： 

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureChinaCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

还可以指定将 **AzureAdClientCertificate** 作为参数的 **AzureAdTokenCredentials** 构造函数。 

有关如何在表单中创建和配置可由 Azure AD 使用的证书的说明，请参阅[使用证书在守护程序应用中对 Azure AD 进行身份验证 - 手动配置步骤](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md)。

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureChinaCloudEnvironment);

若要开始针对媒体服务编程，需要创建一个代表服务器上下文的 **CloudMediaContext** 实例。 此外，还需要将**媒体 REST 服务的资源 URI** 传递到 **CloudMediaContext** 构造函数。 也可以从 Azure 门户获取**媒体 REST 服务的资源 URI** 值。

下面的代码示例创建 **CloudMediaContext** 实例：

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

以下示例介绍如何创建 Azure AD 令牌和上下文：

    namespace AADAuthSample
    {

        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureChinaCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                // Specify your REST API endpoint, for example "https://accountname.restv2.chinaeast.media.chinacloudapi.cn/API".       
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }

                Console.ReadLine();
            }

        }
    }

## <a name="next-steps"></a>后续步骤

开始[将文件上传到帐户](media-services-dotnet-upload-files.md)。

<!--Update_Description: update word & code-->