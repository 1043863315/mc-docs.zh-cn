---
title: 在 Windows 上容器化 Azure Service Fabric 服务
description: 了解如何在 Windows 上容器化 Service Fabric Reliable Services 和 Reliable Actors 服务。
services: service-fabric
documentationcenter: .net
author: rockboyfor
manager: digimobile
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
origin.date: 05/23/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 4f21a45f70aec162b41d9ef234f1e8d85984963c
ms.sourcegitcommit: b8fb6890caed87831b28c82738d6cecfe50674fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58625369"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>在 Windows 上容器化 Service Fabric Reliable Services 和 Reliable Actors

Service Fabric 支持容器化 Service Fabric 微服务（基于 Reliable Services 和 Reliable Actor 的服务）。 有关详细信息，请参阅 [Service Fabric 容器](service-fabric-containers-overview.md)。

本文档提供了在 Windows 容器中运行服务的指南。

> [!NOTE]
> 当前此功能仅适用于 Windows。 若要运行容器，必须在包含容器的 Windows Server 2016 上运行群集。

## <a name="steps-to-containerize-your-service-fabric-application"></a>容器化 Service Fabric 应用程序的步骤

1. 在 Visual Studio 中打开 Service Fabric 应用程序。

2. 将 [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) 类添加到项目。 此类中的代码在容器中运行时，是用于在应用程序中正确加载 Service Fabric 运行时二进制文件的帮助程序。

3. 对于要容器化的每个代码包，在程序入口点初始化加载程序。 将以下代码片段中所示的静态构造函数添加到程序入口点文件中。

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. 生成并[打包](service-fabric-package-apps.md#Package-App)项目。 若要生成并创建包，请在解决方案资源管理器中右键单击应用程序项目，选择“包”命令。

5. 对于每个需要容器化的代码包，运行 PowerShell 脚本 [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1)。 用法如下：

    完整的 .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      该脚本在 $dockerPackageOutputDirectoryPath 处生成包含 Docker 项目的文件夹。 可以根据需要修改生成的 Dockerfile 来 `expose` 任何端口，以及运行设置脚本等 。

6. 接下来需要[生成](service-fabric-get-started-containers.md#Build-Containers) Docker 容器包并将其[推送](service-fabric-get-started-containers.md#Push-Containers)到存储库。

7. 修改 ApplicationManifest.xml 和 ServiceManifest.xml，以添加容器映像、存储库信息、注册表身份验证和端口到主机映射。 有关修改清单的信息，请参阅[创建 Azure Service Fabric 容器应用程序](service-fabric-get-started-containers.md)。 服务清单中的代码包定义需要替换为相应的容器映像。 请确保将入口点更改为 ContainerHost 类型。

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.cn/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. 为复制器和服务终结点添加端口到主机映射。 由于 Service Fabric 在运行时分配这两个端口，因此 ContainerPort 会设置为零，将分配的端口用于映射。

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. 有关配置容器隔离模式，请参阅[配置隔离模式]( https://docs.azure.cn/service-fabric/service-fabric-get-started-containers#configure-isolation-mode)。 Windows 支持容器的两种隔离模式：进程和 Hyper-V。 以下代码片段演示如何在应用程序清单文件中指定隔离模式。

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

10. 若要测试此应用程序，需要将其部署到正在运行版本 5.7 或更高版本的群集。 对于运行时版本 6.1 或更低版本，需要编辑和更新群集设置，启用此预览功能。 请按照[本文](service-fabric-cluster-fabric-settings.md)中的步骤操作，添加下一步所示的设置。
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. 接下来，将已编辑的应用程序包[部署](service-fabric-deploy-remove-applications.md)到此群集。

现在，你应当已拥有了运行群集的容器化 Service Fabric 应用程序。

## <a name="next-steps"></a>后续步骤
* 详细了解如何运行 [Service Fabric 上的容器](service-fabric-get-started-containers.md)。
* 了解 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle.md)。

<!--Update_Description: update meta properties, wording update -->