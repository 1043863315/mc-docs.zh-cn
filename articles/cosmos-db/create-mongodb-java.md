---
title: 快速入门：使用 Azure API for Mongo DB 和 Java SDK 生成 Web 应用 - Azure Cosmos DB
description: 演示一个 Java 代码示例，可以参考该示例使用 Azure Cosmos DB 的用于 MongoDB 的 API 进行连接和查询。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
origin.date: 12/26/2018
ms.date: 09/09/2019
ms.author: v-yeche
ms.custom: seo-java-august2019
ms.openlocfilehash: ced0b1a5acf1fd3b2dc45272c4504435b3ee3d06
ms.sourcegitcommit: 66192c23d7e5bf83d32311ae8fbb83e876e73534
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70254808"
---
# <a name="quickstart-build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-java-sdk"></a>快速入门：使用 Azure Cosmos DB 的用于 MongoDB 的 API 和 Java SDK 生成 Web 应用

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB 是世纪互联提供的多区域分布式多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Cosmos DB 核心的多区域分布和水平缩放功能。 

本快速入门演示如何使用 [Azure Cosmos DB 的用于 MongoDB 的 API](mongodb-introduction.md) 创建 Cosmos 帐户。 然后生成并部署使用 [MongoDB Java 驱动程序](https://docs.mongodb.com/ecosystem/drivers/java/)构建的控制台应用。 

## <a name="prerequisites"></a>先决条件

在运行此示例之前，必须具备以下先决条件：
* [安装适用于 Azure 的 JDK 和 Azure Stack JDK 版本 8](https://docs.azure.cn/java/java-supported-jdk-runtime?view=azure-java-stable)
* Maven（如果没有 Maven，请运行 `apt-get install maven`）

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>添加集合

将新数据库命名为 **db**，将新集合命名为 **coll**。

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 GitHub 克隆应用，设置连接字符串，然后运行该应用。 会看到以编程方式处理数据是多么容易。 

1. 打开命令提示符，新建一个名为“git-samples”的文件夹，然后关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. 然后在偏好的编辑器中打开代码。 

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-string)。 

以下代码片段全部摘自 Program.java 文件。

* 将对 DocumentClient 进行初始化。

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* 会创建新数据库和集合。

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* 使用 `MongoCollection.insertOne` 插入一些文档

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* 使用 `MongoCollection.find` 执行一些查询

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 从帐户中选择“快速启动”   ，选择 Java，然后将连接字符串复制到剪贴板。

2. 打开 `Program.java` 文件，将 MongoClientURI 构造函数的参数替换为该连接字符串。 现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

## <a name="run-the-console-app"></a>运行控制台应用

1. 在终端中运行 `mvn package`，安装所需的 npm 模块

2. 在终端中运行 `mvn exec:java -D exec.mainClass=GetStarted.Program`，启动 Java 应用程序。

现在可以使用 [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) 来查询、修改和处理此新数据。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何创建 Cosmos 帐户、创建集合和运行控制台应用。 现在可以向你的 Cosmos 数据库导入更多数据。

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](mongodb-migrate.md)

<!--Update_Description: update meta properties, update link -->