---
title: 在 Visual Studio 中管理 Azure Servic Fabric 应用程序 | Azure
description: 使用 Visual Studio 来创建、开发、打包、部署和调试 Azure Service Fabric 应用程序和服务。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 03/26/2018
ms.date: 05/28/2018
ms.author: v-yeche
ms.openlocfilehash: b862997e1555a774efc03c7d5597a082441d437c
ms.sourcegitcommit: e50f668257c023ca59d7a1df9f1fe02a51757719
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2018
ms.locfileid: "34554293"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>使用 Visual Studio 简化 Service Fabric 应用程序的编写和管理
可以通过 Visual Studio 管理 Azure Service Fabric 应用程序和服务。 [设置开发环境](service-fabric-get-started.md)后，可以使用 Visual Studio 创建 Service Fabric 应用程序、添加服务，或在本地开发群集中打包、注册和部署应用程序。

## <a name="deploy-your-service-fabric-application"></a>部署 Service Fabric 应用程序
默认情况下，部署一个应用程序会将以下步骤合并为一个简单的操作：

1. 创建应用程序包
2. 将应用程序包上传到映像存储
3. 注册应用程序类型
4. 删除任何正在运行的应用程序实例
5. 删除应用程序实例

在 Visual Studio 中，按 **F5** 部署应用程序，然后将调试程序附加到所有应用程序实例。 可以使用 **Ctrl + F5** 部署应用程序而不进行调试，或者使用发布配置文件将应用程序发布到本地或远程群集。

### <a name="application-debug-mode"></a>应用程序调试模式
Visual Studio 提供一个名为“应用程序调试模式”的属性，它控制 Visual Studio 在调试时如何处理应用程序部署。

#### <a name="to-set-the-application-debug-mode-property"></a>设置“应用程序调试模式”属性
1. 在 Service Fabric 应用程序项目 (*.sfproj) 的快捷菜单上，选择“属性”（或按 **F4** 键）。
2. 在“属性”窗口中，设置“应用程序调试模式”属性。

![设置“应用程序调试模式”属性][debugmodeproperty]

#### <a name="application-debug-modes"></a>应用程序调试模式

1. **刷新应用程序**此模式能快速更改和调试代码并支持调试时编辑静态 Web 文件。 此模式只能在本地开发群集处于 [1 节点模式](./service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode)下时有效。 这是默认的应用程序调试模式。
<!-- URL should be Add . [1-Node mode](./service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode) -->
2. **删除应用程序** 会在调试会话结束时删除应用程序。
3. **自动升级**调试会话结束时，应用程序继续运行。 下一个调试会话将部署视为升级。 此升级过程会保留你在上一个调试会话中输入的任何数据。
4. **保留应用程序**调试会话结束时，应用程序在群集中继续运行。 在下一个调试会话开始时将删除应用程序。

对于**自动升级**，数据是通过应用 Service Fabric 的应用程序升级功能予以保留。 若要详细了解如何升级应用程序，以及如何在真实环境中执行升级，请参阅 [Service Fabric 应用程序升级](service-fabric-application-upgrade.md)。

## <a name="add-a-service-to-your-service-fabric-application"></a>向 Service Fabric 应用程序添加服务
可以向应用程序中添加新服务以扩展其功能。 若要确保应用程序包中包含该服务，请通过“新建 Fabric 服务...”  菜单项添加服务。

![添加新的 Service Fabric 服务][newservice]

选择要添加到应用程序的 Service Fabric 项目类型，并指定服务的名称。  请参阅[为服务选择框架](service-fabric-choose-framework.md)，这篇文章有助于你确定要使用的服务类型。

![选择要添加到应用程序的 Service Fabric 服务项目类型][addserviceproject]

新的服务会添加到解决方案和现有的应用程序包中。 服务引用和默认服务实例将添加到应用程序清单中，导致服务创建并在下次部署应用程序时启动。

![新的服务会添加到应用程序清单中][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>打包 Service Fabric 应用程序
要将应用程序及其服务部署到群集中，你需要创建应用程序包。  该包整理应用程序清单、服务清单和特定布局的其他必要文件。  Visual Studio 设置和管理“pkg”目录中应用程序项目的文件夹中的程序包。  单击“应用程序”上下文菜单中的“包”可创建或更新应用程序包。

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>使用云资源管理器删除应用程序和应用程序类型
可以使用云资源管理器从 Visual Studio 内部执行基本的群集管理操作，可以从“视图”  菜单启动该管理器。 例如，可以删除本地或远程群集上的应用程序和取消预配其上的应用程序类型。

![删除应用程序][removeapplication]

> [!TIP]
> 有关更丰富的群集管理功能，请参阅[使用 Service Fabric Explorer 可视化群集](service-fabric-visualizing-your-cluster.md)。
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
* [Service Fabric 应用程序模型](service-fabric-application-model.md)
* [Service Fabric 应用程序部署](service-fabric-deploy-remove-applications.md)
* [管理多个环境的应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)
* [调试 Service Fabric 应用程序](service-fabric-debugging-your-application.md)
* [使用 Service Fabric Explorer 可视化群集](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png

<!-- Update_Description: update meta properties, update link -->