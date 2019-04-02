---
title: 使用 REST 管理媒体服务实体 | Microsoft Docs
description: 了解如何使用 REST API 管理媒体服务实体。
author: WenJason
manager: digimobile
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/20/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.openlocfilehash: db167fd99754ec34fed1629c73a825c9007567ee
ms.sourcegitcommit: 2d43e48f4c80e085e628e83822eeaa38f62d1cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58624150"
---
# <a name="managing-media-services-entities-with-rest"></a>使用 REST 管理媒体服务实体  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Azure 媒体服务是一项以 OData v3 为基础的基于 REST 的服务。 可以像在任何其他 OData 服务上一样添加、查询、更新和删除实体。 适用时，标注例外情况。 有关 OData 的详细信息，请参阅 [开放数据协议文档](https://www.odata.org/documentation/)。

本主题介绍如何使用 REST 管理 Azure 媒体服务实体。

>[!NOTE]
> 自 2017 年 4 月 1 日起，即使记录总数低于最大配额，也自动删除帐户中所有超过 90 天的作业记录，及其相关的任务记录。 例如，在 2017 年 4 月 1 日，用户帐户中 2016 年 12 月 31 日以前的任何作业记录都会被系统自动删除。 若需存档作业/任务信息，可使用本主题所述代码。

## <a name="considerations"></a>注意事项  

访问媒体服务中的实体时，必须在 HTTP 请求中设置特定标头字段和值。 有关详细信息，请参阅[媒体服务 REST API 开发的设置](media-services-rest-how-to-use.md)。

## <a name="connect-to-media-services"></a>连接到媒体服务

若要了解如何连接到 AMS API，请参阅[通过 Azure AD 身份验证访问 Azure 媒体服务 API](media-services-use-aad-auth-to-access-ams-api.md)。 

## <a name="adding-entities"></a>添加实体
媒体服务中的每个实体都通过 POST HTTP 请求添加到实体集（如资产）中。

以下示例说明了如何创建 AccessPolicy。

    POST https://media.chinacloudapi.cn/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.chinacloudapi.cn
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>查询实体
查询和列出实体非常简单，仅涉及 GET HTTP 请求和可选的 OData 操作。
以下示例会检索包含所有 MediaProcessor 实体的列表。

    GET https://media.chinacloudapi.cn/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.chinacloudapi.cn

也可检索特定实体或与特定实体关联的所有实体集，如以下示例所示：

    GET https://media.chinacloudapi.cn/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.chinacloudapi.cn

    GET https://media.chinacloudapi.cn/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.chinacloudapi.cn

以下示例仅返回所有作业的 State 属性。

    GET https://media.chinacloudapi.cn/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.chinacloudapi.cn

以下示例返回名为“SampleTemplate”的所有 JobTemplate。

    GET https://media.chinacloudapi.cn/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.chinacloudapi.cn

> [!NOTE]
> 媒体服务不支持 $expand 操作以及“LINQ 注意事项（WCF Data Services）”中所述的不受支持的 LINQ 方法。
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>枚举大型实体集合
查询实体时，一次返回的实体数限制为 1000 个，因为公共 REST v2 将查询结果数限制为 1000 个。 使用 skip 和 top 枚举大型实体集合。 

以下示例说明如何使用 skip 和 top 来跳过前 2000 个作业并获取后 1000 个作业。  

    GET https://media.chinacloudapi.cn/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.chinacloudapi.cn

## <a name="updating-entities"></a>更新实体
根据实体类型及其所处的状态，可以通过 PATCH、PUT 或 MERGE HTTP 请求更新该实体上的属性。 有关这些操作的详细信息，请参阅 [PATCH/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx)。

以下代码示例演示如何更新资产实体上的名称属性。

    MERGE https://media.chinacloudapi.cn/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.chinacloudapi.cn
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>删除实体
可以使用 DELETE HTTP 请求在媒体服务中删除实体。 删除实体的顺序可能很重要，具体视实体而定。 例如，资产等实体要求先撤消（或删除）引用该特定资产的所有定位符，然后再删除资产。

以下示例演示如何删除用于将文件上传到 blob 存储的定位符。

    DELETE https://media.chinacloudapi.cn/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.chinacloudapi.cn
    Content-Length: 0

<!--Update_Description: update x-ms-version-->