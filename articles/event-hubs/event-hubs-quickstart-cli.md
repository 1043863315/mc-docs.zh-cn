---
title: 使用 Azure CLI 创建事件中心 - Azure 事件中心 | Azure
description: 本快速入门介绍如何使用 Azure CLI 创建事件中心，然后使用 Java 发送和接收事件。
services: event-hubs
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: java
ms.topic: quickstart
ms.custom: seodec18
origin.date: 02/11/2020
ms.date: 03/16/2020
ms.author: v-tawe
ms.openlocfilehash: 70bcde132db078651110f3cccba6e39618039dab
ms.sourcegitcommit: 1d3d8dfdaf6281f06640cbee7124a1e8bf102c50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2020
ms.locfileid: "80243920"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>快速入门：使用 Azure CLI 创建事件中心

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

在本快速入门中，请使用 Azure CLI 创建事件中心。

## <a name="prerequisites"></a>先决条件
若要完成本快速入门，需要一个 Azure 订阅。 如果没有订阅，请在开始之前[创建一个试用帐户][]。


如果选择在本地安装并使用 Azure CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 请运行 `az --version` 检查版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli?view=azure-cli-latest)。

## <a name="sign-in-to-azure"></a>登录 Azure

如果在本地运行 CLI，请按以下步骤登录到 Azure 并设置当前订阅：

运行以下命令来登录到 Azure：

```azurecli
az cloud set -n AzureChinaCloud
az login
```

设置当前订阅上下文。 将 `MyAzureSub` 替换为要使用的 Azure 订阅的名称：

```azurecli
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>创建资源组
资源组是 Azure 资源的逻辑集合。 所有资源在资源组中进行部署和管理。 运行以下命令创建资源组：

```azurecli
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location chinaeast
```

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
事件中心命名空间提供唯一的范围容器，可以通过其完全限定的域名进行引用，而在该容器中，可以创建一个或多个事件中心。 若要在资源组中创建命名空间，请运行以下命令：

```azurecli
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: China East>
```

## <a name="create-an-event-hub"></a>创建事件中心
请运行以下命令，以便创建事件中心：

```azurecli
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

祝贺！ 现已使用 Azure CLI 创建了一个事件中心命名空间，并在该命名空间中创建了一个事件中心。 

## <a name="next-steps"></a>后续步骤

在本文中，你已创建一个资源组、一个事件中心命名空间和一个事件中心。 有关如何将事件发送到事件中心（或）从事件中心接收事件的分步说明，请参阅“发送和接收事件”教程  ： 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C（仅发送）](event-hubs-c-getstarted-send.md)
- [Apache Storm（仅接收）](event-hubs-storm-getstarted-receive.md)

[创建一个试用帐户]: https://www.azure.cn/pricing/1rmb-trial/
[Install the Azure CLI]: /cli/install-azure-cli?view=azure-cli-latest
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://en.wikipedia.org/wiki/Fully_qualified_domain_name
