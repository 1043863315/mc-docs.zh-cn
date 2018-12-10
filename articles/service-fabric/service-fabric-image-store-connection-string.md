---
title: Azure Service Fabric 映像存储区连接字符串 | Azure
description: 了解映像存储区连接字符串
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 02/27/2018
ms.date: 10/15/2018
ms.author: v-yeche
ms.openlocfilehash: c7d9d9924ba2fc34a4ba27c427b02d7ab0f72f35
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52649733"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>了解 ImageStoreConnectionString 设置

在某些文档中，浅要提及了“ImageStoreConnectionString”参数的存在，但未阐述其真正的含义。 阅读诸如[使用 PowerShell 部署和删除应用程序][10]等文章后，貌似你只需在目标群集的群集清单中出现值时复制/粘贴该值。 因此设置必须可按群集进行配置，但在通过 [Azure 门户][11]创建群集时，无法配置此设置且其始终为“fabric:ImageStore”。 那么，此设置的存在有何作用？

![群集清单][img_cm]

Service Fabric 一开始被许多不同的团队用作 Azure 内部消耗平台，因此，其某些方面是高度可自定义的，“映像存储 (Image Store)”就是这样的存在。 映像存储区实质上是用于存储应用程序包的可插入存储库。 应用程序部署到群集中的节点时，该节点会从映像存储区下载应用程序包的内容。 ImageStoreConnectionString 是一项设置，它包含客户端和节点用于查找给定群集的正确映像存储区的所有必要信息。

目前，有三种可能的映像存储区提供程序类型，这些类型及其对应的连接字符串如下所示：

1. 映像存储区服务：“fabric:ImageStore”

2. 文件系统：“file:[file system path]”

3. Azure 存储：“xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...];EndpointSuffix=core.chinacloudapi.cn”<!-- Add the EndpointSuffix in configuration of EndpointSuffix=core.chinacloudapi.cn -->
<!-- Notice: core.windows.net to core.chinacloudapi.cn -->

在生产中使用的提供程序类型为映像存储区服务，它是可通过 Service Fabric Explorer 查看的有状态持久化系统服务。 

![映像存储区服务][img_is]

在群集内的系统服务中托管映像存储区可清除包存储库的外部依赖项，并让我们能够更好地控制存储的位置。 围绕映像存储区的后续改进很可能先针对映像存储区提供程序（若非唯一目标）。 客户端已连接到目标群集，因此，映像存储区服务提供程序的连接字符串不具有任何唯一的信息。 客户端只需知道应使用面向系统服务的协议即可。

在开发过程中，本地单机群集使用文件系统提供程序，而不使用映像存储区服务，目的在于让群集的 bootstrap 操作速度略微提升。 差异通常较小，但在开发期间，它对大多数人员而言是有用的优化。 也可通过其他存储提供程序类型部署本地单机群集，但通常无需这样做，因为不管提供程序如何，开发/测试工作流都将保持不变。 Azure 存储提供程序仅用于为在引入映像存储服务提供程序前部署的旧群集提供旧版支持。

此外，文件系统提供程序或 Azure 存储提供程序都不应用作在多个群集之间共享映像存储的方法 - 这会导致群集配置数据损坏，因为每个群集都可将冲突数据写入到映像存储。 若要在多个群集之间共享预配的应用程序包，请改用 [sfpkg][12] 文件，可以使用下载 URI 将这些文件上传到任何外部存储。

因此虽然可配置 ImageStoreConnectionString，但只需使用默认设置。 通过 Visual Studio 发布到 Azure 时，该参数会相应地自动设置。 对于 Azure 中托管的群集的编程部署，连接字符串始终为“fabric: ImageStore”。 有疑问时，始终可通过 [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest)、[.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx) 或 [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest) 检索群集清单验证其值。 同样，本地测试和生产群集应始终配置为使用映像存储区服务提供程序。

### <a name="next-steps"></a>后续步骤
[使用 PowerShell 部署和删除应用程序][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg

<!--Update_Description: update meta properties, wording update  -->