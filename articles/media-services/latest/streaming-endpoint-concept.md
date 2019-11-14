---
title: Azure 媒体服务中的流式处理终结点（来源）| Microsoft Docs
description: 在 Azure 媒体服务中，流式处理终结点（来源）表示一个动态打包和流式处理服务，该服务可以直接将内容传递给客户端播放器应用程序，也可以传递给内容分发网络 (CDN) 以进一步分发。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
origin.date: 07/11/2019
ms.date: 11/04/2019
ms.author: v-jay
ms.openlocfilehash: ecb24996e554b6409d239cceb108382cfb2e3a22
ms.sourcegitcommit: f9a257e95444cb64c6d68a7a1cfe7e94c5cc5b19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416201"
---
# <a name="streaming-endpoints"></a>流式处理终结点 

在 Azure 媒体服务中，[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)表示动态（实时）打包和源服务，该服务可使用一个常见流式处理媒体协议（HLS 或 DASH）直接将实时和按需内容发送到客户端播放器应用程序。 此外，**流式处理终结点**为行业领先的 DRM 提供动态（实时）加密。

用户创建媒体服务帐户时，将为用户创建一个处于“已停止”状态的默认  流式处理终结点。 无法删除“默认”流式处理终结点  。 可以在帐户下创建更多的流式处理终结点（请参阅[配额和限制](limits-quotas-constraints.md)）。 

> [!NOTE]
> 若要开始流式处理视频，需启动要从中流式处理视频的**流式处理终结点**。 
>  
> 仅当流式处理终结点处于运行状态时才进行计费。

## <a name="naming-convention"></a>命名约定

流式处理 URL 的主机名格式为：`{servicename}-{accountname}-{regionname}.streaming.media.chinacloudapi.cn`，其中 `servicename` = 流式处理终结点名称或实时事件名称。 

使用默认的流式处理终结点时，将省略 `servicename`，因此 URL 为：`{accountname}-{regionname}.streaming.chinacloudapi.cn`。 

### <a name="limitations"></a>限制

* 流式处理终结点名称的最大值为 24 个字符。
* 该名称应遵循此[正则表达式](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)模式：`^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`。

## <a name="types"></a>类型  

有两种类型的**流式处理终结点**：**标准**（预览版）和**高级**。 类型由用户为流式处理终结点分配的缩放单元（`scaleUnits`）数定义。 

下表描述了类型：  

|类型|缩放单元|说明|
|--------|--------|--------|  
|**标准**|0|默认的流式处理终结点是“标准”类型，可以通过调整 `scaleUnits` 更改为“高级”类型。 |
|**高级**|>0|“高级”  流式处理终结点适用于高级工作负荷，可提供专用且可缩放的带宽容量。 可以通过调整 `scaleUnits`（流单元）转到“高级”  类型。 `scaleUnits` 提供专用的出口容量，可以按照 200 Mbps 的增量购买。 使用高级  类型时，每个启用的单元都为应用程序提供额外的带宽容量。 |

有关 SLA 的信息，请参阅[定价和 SLA](https://azure.cn/pricing/details/media-services/)。

## <a name="comparing-streaming-types"></a>比较流式处理类型

功能|标准|高级
---|---|---
吞吐量 |最大 600 Mbps |每个流单元 (SU) 200 Mbps。
按比例计费| 每日|每日
动态加密|是|是
动态打包|是|是
缩放|自动扩展到目标吞吐量。|更多的 SU
IP 筛选/G20/自定义主机 |是|是
渐进式下载|是|是
建议的用法 |建议用于绝大多数的流式处理方案。|专业用途。

## <a name="properties"></a>属性 

本部分提供有关某些流式处理终结点属性的详细信息。 有关如何创建新流式处理终结点和所有属性描述的示例，请参阅[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)。 

- `accessControl` - 用于为此流式处理终结点配置以下安全设置：Akamai 签名标头身份验证密钥和允许连接到此终结点的 IP 地址。
- `crossSiteAccessPolicies` - 用于为各种客户端指定跨站点访问策略。 有关详细信息，请参阅[跨域策略文件规范](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)和[提供跨域边界的服务](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)。<br/>这些设置仅适用于平滑流式处理。
- `customHostNames` - 用于配置流式处理终结点以接受定向到自定义主机名的流量。  此属性对“标准”和“高级”流式处理终结点有效。
    
    域名的所有权必须由媒体服务确认。 媒体服务通过要求将包含媒体服务帐户 ID 的 `CName` 记录作为组件添加到正在使用的域来验证域名所有权。 例如，要将“sports.contoso.com”用作流式处理终结点的自定义主机名，则必须将 `<accountId>.contoso.com` 的记录配置为指向其中一个媒体服务验证主机名。 验证主机名由 verifydns.\<mediaservices-dns-zone> 组成。 

    下面是预期在 Azure 中国区的验证记录中使用的 DNS 区域。
  
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    例如，将“945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com”映射到“verifydns.mediaservices.chinacloudapi.cn”的 `CName` 记录证明媒体服务 ID 945a4c4e-28ea-45cd-8ccb- a519f6b700ad 拥有 contoso.com 域的所有权，因此可以将 contoso.com 下的任何名称用作该帐户下的流式处理终结点的自定义主机名。 若要查找媒体服务 ID 值，请转至 [Azure 门户](https://portal.azure.cn/)，然后选择你的媒体服务帐户。 “帐户 ID”显示在页面的右上方。 
        
    如果尝试在没有正确验证 `CName` 记录的情况下设置自定义主机名，则 DNS 响应将失败，然后缓存一段时间。 拥有适当的记录后，可能需要一段时间才能重新验证缓存的响应。 根据自定义域的 DNS 提供程序，重新验证记录可能需要几分钟到一个小时的时间。
        
    除了将 `<accountId>.<parent domain>` 映射到 `verifydns.<mediaservices-dns-zone>` 的 `CName`，还必须创建另一个 `CName`，以将自定义主机名（例如 `sports.contoso.com`）映射到媒体服务流式处理终结点的主机名（例如 `amstest-cne21.streaming.media.chinacloudapi.cn`）。
 
    > [!NOTE]
    > 位于同一数据中心的流式处理终结点不能共享相同的自定义主机名。

    媒体服务当前对自定义域不支持 SSL。 
    
- `maxCacheAge` - 替代媒体片段和按需清单上的流式处理终结点设置的默认 max-age HTTP 缓存控制标头。 该值以秒为单位进行设置。
- `resourceState` -

    - Stopped - 创建后的流式处理终结点的初始状态
    - Starting - 正在转换为运行状态
    - Running - 可将内容流式传输到客户端
    - Scaling - 缩放单元正在增加或减少
    - Stopping - 正在转换到停止状态
    - Deleting - 正在删除
    
- `scaleUnits` - 提供专用的出口容量，可以按照 200 Mbps 的增量购买。 如果需要转到高级  类型，请调整 `scaleUnits`。

## <a name="next-steps"></a>后续步骤

此[存储库](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)中的示例介绍了如何使用 .NET 启动默认流式处理终结点。

