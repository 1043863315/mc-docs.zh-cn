---
title: 使用 ZIP 或 WAR 文件部署代码 - Azure 应用服务 | Azure
description: 了解如何使用 ZIP 文件（或者对于 Java 开发人员而言使用 WAR 文件）将应用部署到 Azure 应用服务。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
origin.date: 08/12/2018
ms.date: 11/25/2019
ms.author: v-tawe
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 60c6fe1ed7b08210510c0be0084a95cc5c15d232
ms.sourcegitcommit: e7dd37e60d0a4a9f458961b6525f99fa0e372c66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555804"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>使用 ZIP 或 WAR 文件将应用部署到 Azure 应用服务

本文演示如何使用 ZIP 或 WAR 文件将 Web 应用部署到 [Azure 应用服务](overview.md)。 

此 .zip 文件部署使用相同的 Kudu 服务，支持基于持续集成的部署。 对于 .zip 文件部署，Kudu 支持以下功能： 

- 删除先前的部署留下的文件。
- 启用默认生成过程的选项，包括包还原。
- [部署自定义](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)，包括运行部署脚本。  
- 部署日志。 
- 文件大小上限是 2048 MB。

有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

WAR 文件部署将 [WAR](https://wikipedia.org/wiki/WAR_(file_format)) 文件部署到应用服务，以运行 Java Web 应用。 请参阅[部署 WAR 文件](#deploy-war-file)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本文中的步骤：

* [创建一个应用服务应用](/app-service/)，或者使用为其他教程创建的应用。

## <a name="create-a-project-zip-file"></a>创建一个项目 zip 文件

>[!NOTE]
> 如果以 zip 文件的形式下载文件，请首先提取该文件。 例如，如果从 GitHub 下载了 zip 文件，则无法按原样部署该文件。 GitHub 可添加额外的嵌套目录，但这不适用于应用服务。 
>

在本地终端窗口中，导航到应用项目的根目录。 

此目录应包含 Web 应用的入口文件，例如 _index.html_、_index.php_ 和 _app.js_。 它还可能包含包管理文件，如 _project.json_、_composer.json_、_package.json_、_bower.json_ 和 _requirements.txt_。

创建一个包含项目中的所有内容的 zip 存档。 以下命令使用您终端中的默认工具执行操作：

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
<!-- The above endpoint does not work for Linux App Services at this time. Consider using FTP or the [ZIP deploy API](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) instead. -->

## <a name="deploy-zip-file-with-azure-cli"></a>使用 Azure CLI 部署 ZIP 文件

请确保 Azure CLI 版本是 2.0.21 或更高版本。 要查看已有版本，请在终端窗口中运行 `az --version` 命令。

使用 [az webapp deployment source config-zip](/cli/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令将上传的 zip 文件部署到 Web 应用。  

下面的示例部署上传的 zip 文件。 使用 Azure CLI 的本地安装时，请为 `--src` 指定本地 zip 文件的路径。

```azurecli
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

此命令将 zip 文件中的文件和目录部署到默认的应用服务应用程序文件夹 (`\home\site\wwwroot`) 并重启应用。

默认情况下，部署引擎假定 ZIP 文件已准备好按原样运行，并且不运行任何生成自动化。 若要启用与 [Git 部署](deploy-local-git.md)中相同的生成自动化，请通过在 Azure CLI 中运行以下命令来设置 `SCM_DO_BUILD_DURING_DEPLOYMENT` 应用设置：

```azurecli
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```



有关详细信息，请参阅 [Kudu 文档](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>部署 WAR 文件

若要将 WAR 文件部署到应用服务，请将 POST 请求发送到 https://<app_name>.scm.chinacloudsites.cn/api/wardeploy。 POST 请求必须在消息正文中包含此 .war 文件。 应用的部署凭据是通过使用 HTTP BASIC 身份验证在请求中提供的。 

对于 HTTP 基本身份验证，需使用应用服务部署凭据。 若要了解如何设置部署凭据，请参阅[设置和重置用户级别凭据](deploy-configure-credentials.md#userscope)。

### <a name="with-curl"></a>使用 cURL

以下示例使用 cURL 工具部署 .war 文件。 替换占位符 `<username>`、`<war-file-path>` 和 `<app-name>`。 出现 cURL 提示时，键入密码。

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app_name>.scm.chinacloudsites.cn/api/wardeploy
```

### <a name="with-powershell"></a>使用 PowerShell

下面的示例使用 [Publish-AzWebapp](https://docs.microsoft.com/powershell/module/az.websites/publish-azwebapp) 上传 .war 文件。 替换占位符 `<group-name>`、`<app-name>` 和 `<war-file-path>`。

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>后续步骤

有关更高级的部署方案，请参阅[使用 Git 部署到 Azure](deploy-local-git.md)。 通过基于 Git 的 Azure 部署可实现版本控制、包还原、MSBuild 等功能。

## <a name="more-resources"></a>更多资源

* [Kudu：从 zip 文件部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [ 部署凭据](deploy-ftp.md)
