---
title: 教程：生成一个用于在 Azure Cosmos DB SQL API 帐户中管理数据的 .NET 控制台应用
description: 教程：了解如何使用 C# 控制台应用程序创建 Azure Cosmos DB SQL API 资源。
author: rockboyfor
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
origin.date: 11/05/2019
ms.date: 12/16/2019
ms.author: v-yeche
ms.openlocfilehash: cfe1ffe7f2736933832ef51b88a7599274999a9b
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75336396"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>教程：生成一个用于在 Azure Cosmos DB SQL API 帐户中管理数据的 .NET 控制台应用

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [异步 Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

欢迎使用 Azure Cosmos DB SQL API 入门教程。 学习本教程后，将拥有一个可创建并查询 Azure Cosmos DB 资源的控制台应用程序。

本教程使用 3.0 版或更高版的 [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)。 可以使用 [.NET Framework 或 .NET Core](https://dotnet.microsoft.com/download)。

本教程的内容包括：

> [!div class="checklist"]
>
> * 创建并连接到 Azure Cosmos 帐户
> * 在 Visual Studio 中配置项目
> * 创建数据库和容器
> * 向容器添加项
> * 查询容器
> * 对项执行创建、读取、更新和删除 (CRUD) 操作
> * 删除数据库

没有时间？ 不必担心！ 可在 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)上获取完整的解决方案。 有关快速说明，请转到[“获取完整的教程解决方案”部分](#GetSolution)。

现在，让我们开始吧！

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 帐户。 如果没有，可以注册[试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步骤 1：创建 Azure Cosmos DB 帐户

创建 Azure Cosmos DB 帐户。 如果已经有要使用的帐户，请跳过此部分。 若要使用 Azure Cosmos DB 模拟器，请按照 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器。 然后跳转到[步骤 2：设置 Visual Studio 项目](#SetupVS)。

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

<a name="SetupVS"></a>
## <a name="step-2-set-up-your-visual-studio-project"></a>步骤 2：设置 Visual Studio 项目

1. 打开 Visual Studio 并选择“创建新项目”  。
1. 在“创建新项目”  中，选择用于 C# 的“控制台应用(.NET Framework)”  ，然后选择“下一步”  。
1. 将项目命名为 *CosmosGettingStartedTutorial*，然后选择“创建”。 

    ![配置项目](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. 在“解决方案资源管理器”  中，右键单击 Visual Studio 解决方案下方的新控制台应用程序，然后选择“管理 NuGet 包”。 
1. 在“NuGet 包管理器”  中选择“浏览”  ，然后搜索“Microsoft.Azure.Cosmos”  。 选择“Microsoft.Azure.Cosmos”  ，然后选择“安装”  。

    ![安装用于 Azure Cosmos DB 客户端 SDK 的 NuGet](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

    Azure Cosmos DB SQL API 客户端库的包 ID 是 [Azure Cosmos DB 客户端库](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)。

很好！ 现在，我们已完成安装，让我们开始编写一些代码。 如需本教程的已完成的项目，请参阅 [Developing a .NET console app using Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)（使用 Azure Cosmos DB 开发 .NET 控制台应用）。

<a name="Connect"></a>
## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a>步骤 3：连接到 Azure Cosmos DB 帐户

1. 将 *Program.cs* 文件中 C# 应用程序开头的引用替换为以下引用：

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;
    ```

1. 将这些常量和变量添加到 `Program` 类中。

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

    > [!NOTE]
    > 如果你熟悉旧版 .NET SDK，则可能熟悉术语“集合”和“文档”。   由于 Azure Cosmos DB 支持多个 API 模型，因此 3.0 版的 .NET SDK 使用通用术语“容器”和“项”。   容器可以是集合、图或表。  项可以是文档、边缘/顶点或行，是容器中的内容。  有关详细信息，请参阅[在 Azure Cosmos DB 中使用数据库、容器和项](databases-containers-items.md)。

1. 打开 [Azure 门户](https://portal.azure.cn)。 找到 Azure Cosmos DB 帐户，然后选择“密钥”。 

    ![从 Azure 门户获取 Azure Cosmos DB 密钥](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. 在 *Program.cs* 中，将 `<your endpoint URL>` 替换为 **URI** 的值。 将 `<your primary key>` 替换为“主密钥”  的值。

1. 在 **Main** 方法下面，添加名为 **GetStartedDemoAsync** 的新异步任务，将新的 `CosmosClient` 实例化。

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    我们使用 **GetStartedDemoAsync** 作为入口点，该入口点调用在 Azure Cosmos DB 资源上运行的方法。

1. 添加以下代码，从 **Main** 方法中运行 **GetStartedDemoAsync** 异步任务。 **Main** 方法将捕获异常并将它们写到控制台上。

    ```csharp

    public static async Task Main(string[] args)
    {
        try
        {
            Console.WriteLine("Beginning operations...\n");
            Program p = new Program();
            await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
            Exception baseException = de.GetBaseException();
            Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }

    ```

1. 选择 F5 来运行应用程序。

    控制台会显示消息“演示结束，请按任意键退出”。  此消息确认应用程序已连接到 Azure Cosmos DB。 然后，可以关闭控制台窗口。

祝贺！ 你已成功连接到 Azure Cosmos DB 帐户。

## <a name="step-4-create-a-database"></a>步骤 4：创建数据库

数据库是跨容器分区的项的逻辑容器。 [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) 类的 `CreateDatabaseIfNotExistsAsync` 或 `CreateDatabaseAsync` 方法可以创建数据库。

1. 复制 `CreateDatabaseAsync` 方法并将其粘贴到 `GetStartedDemoAsync` 方法下面。

    ```csharp

    /// <summary>
    /// Create the database if it does not exist
    /// </summary>
    private async Task CreateDatabaseAsync()
    {
        // Create a new database
        this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
        Console.WriteLine("Created Database: {0}\n", this.database.Id);
    }

    ```

    `CreateDatabaseAsync` 使用 `databaseId` 字段中指定的 ID `FamilyDatabase` 来创建新数据库（如果尚不存在该数据库）。

1. 复制并粘贴以下代码，在其中实例化 CosmosClient 来调用刚添加的 **CreateDatabaseAsync** 方法。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    *Program.cs* 现在应如下所示，其中填充了终结点和主密钥。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. 选择 F5 来运行应用程序。

    > [!NOTE]
    > 如果收到“503 服务不可用异常”错误，则直接连接模式所需的[端口](performance-tips.md#networking)可能已被防火墙阻止。 若要解决此问题，打开所需端口或使用网关模式连接，如以下代码所示：
    
    ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
    ```

祝贺！ 你已成功创建 Azure Cosmos 数据库。  

<a name="CreateColl"></a>
## <a name="step-5-create-a-container"></a>步骤 5：创建容器

> [!WARNING]
> 方法 `CreateContainerIfNotExistsAsync` 创建新的容器，牵涉到定价。 有关详细信息，请访问 [定价页](https://www.azure.cn/pricing/details/cosmos-db/)。
>
>

可以使用 `CosmosDatabase` 类中的 [**CreateContainerIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) 或 [**CreateContainerAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) 方法创建容器。 容器包含项（在使用 SQL API 的情况下为 JSON 文档）和关联的 JavaScript 服务器端应用程序逻辑，例如存储过程、用户定义的函数以及触发器。

1. 复制 `CreateContainerAsync` 方法并将其粘贴到 `CreateDatabaseAsync` 方法下面。 `CreateContainerAsync` 使用 `containerId` 字段中指定的 ID `FamilyContainer` 来创建按 `LastName` 属性分区的新容器（如果尚不存在该容器）。

    ```csharp

    /// <summary>
    /// Create the container if it does not exist. 
    /// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
    /// </summary>
    /// <returns></returns>
    private async Task CreateContainerAsync()
    {
        // Create a new container
        this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
        Console.WriteLine("Created Container: {0}\n", this.container.Id);
    }

    ```

1. 复制并粘贴以下代码，其中实例化了 CosmosClient 来调用刚添加的 **CreateContainer** 方法。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. 选择 F5 来运行应用程序。

祝贺！ 你已成功创建 Azure Cosmos 容器。  

<a name="CreateDoc"></a>
## <a name="step-6-add-items-to-the-container"></a>步骤 6：向容器添加项

`CosmosContainer` 类的 [**CreateItemAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) 方法可以创建项。 使用 SQL API 时，项会投射为文档，后者是用户定义的任意 JSON 内容。 现在，可以将项插入 Azure Cosmos 容器中。

在此示例中，让我们首先创建 `Family` 类来表示存储在 Azure Cosmos DB 中的对象。 我们还会创建在 `Family` 中使用的 `Parent`、`Child`、`Pet`、`Address` 子类。 该项必须有一个以 JSON 格式序列化为 `id` 的 `Id` 属性。

1. 选择 Ctrl+Shift+A，打开“添加新项”  。 将新类 `Family.cs` 添加到项目。

    ![向项目添加新的 Family.cs 类的屏幕截图](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. 复制 `Family`、`Parent`、`Child`、`Pet` 和 `Address` 类并将其粘贴到 `Family.cs` 中。

    ```csharp
    using Newtonsoft.Json;

    namespace CosmosGettingStartedTutorial
    {
        public class Family
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
            public string LastName { get; set; }
            public Parent[] Parents { get; set; }
            public Child[] Children { get; set; }
            public Address Address { get; set; }
            public bool IsRegistered { get; set; }
            public override string ToString()
            {
                return JsonConvert.SerializeObject(this);
            }
        }

        public class Parent
        {
            public string FamilyName { get; set; }
            public string FirstName { get; set; }
        }

        public class Child
        {
            public string FamilyName { get; set; }
            public string FirstName { get; set; }
            public string Gender { get; set; }
            public int Grade { get; set; }
            public Pet[] Pets { get; set; }
        }

        public class Pet
        {
            public string GivenName { get; set; }
        }

        public class Address
        {
            public string State { get; set; }
            public string County { get; set; }
            public string City { get; set; }
        }
    }

    ```

1. 回到 *Program.cs* 中，将 `AddItemsToContainerAsync` 方法添加到 `CreateContainerAsync` 方法后面。

    ```csharp

    /// <summary>
    /// Add Family items to the container
    /// </summary>
    private async Task AddItemsToContainerAsync()
    {
        // Create a family object for the Andersen family
        Family andersenFamily = new Family
        {
            Id = "Andersen.1",
            LastName = "Andersen",
            Parents = new Parent[]
            {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay" }
            },
            Children = new Child[]
            {
                new Child
                {
                    FirstName = "Henriette Thaulow",
                    Gender = "female",
                    Grade = 5,
                    Pets = new Pet[]
                    {
                        new Pet { GivenName = "Fluffy" }
                    }
                }
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = false
        };

        try
        {
            // Read the item to see if it exists.  
            ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
            Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
        }
        catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
        {
            // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
            ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

            // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
            Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
        }

        // Create a family object for the Wakefield family
        Family wakefieldFamily = new Family
        {
            Id = "Wakefield.7",
            LastName = "Wakefield",
            Parents = new Parent[]
            {
                new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                new Parent { FamilyName = "Miller", FirstName = "Ben" }
            },
            Children = new Child[]
            {
                new Child
                {
                    FamilyName = "Merriam",
                    FirstName = "Jesse",
                    Gender = "female",
                    Grade = 8,
                    Pets = new Pet[]
                    {
                        new Pet { GivenName = "Goofy" },
                        new Pet { GivenName = "Shadow" }
                    }
                },
                new Child
                {
                    FamilyName = "Miller",
                    FirstName = "Lisa",
                    Gender = "female",
                    Grade = 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = true
        };

        try
        {
            // Read the item to see if it exists
            ItemResponse<Family> wakefieldFamilyResponse = await this.container.ReadItemAsync<Family>(wakefieldFamily.Id, new PartitionKey(wakefieldFamily.LastName));
            Console.WriteLine("Item in database with id: {0} already exists\n", wakefieldFamilyResponse.Resource.Id);
        }
        catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
        {
            // Create an item in the container representing the Wakefield family. Note we provide the value of the partition key for this item, which is "Wakefield"
            ItemResponse<Family> wakefieldFamilyResponse = await this.container.CreateItemAsync<Family>(wakefieldFamily, new PartitionKey(wakefieldFamily.LastName));

            // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
            Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", wakefieldFamilyResponse.Resource.Id, wakefieldFamilyResponse.RequestCharge);
        }
    }

    ```

    代码会进行检查，确保不存在 ID 相同的项。 我们会插入两个项，Andersen 家族和 Wakefield 家族各一个。  

1. 在 `GetStartedDemoAsync` 方法中添加对 `AddItemsToContainerAsync` 的调用。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. 选择 F5 来运行应用程序。

祝贺！ 你已成功创建两个 Azure Cosmos 项。  

<a name="Query"></a>
## <a name="step-7-query-azure-cosmos-db-resources"></a>步骤 7：查询 Azure Cosmos DB 资源

Azure Cosmos DB 支持对存储在每个容器中的 JSON 文档进行各种查询。 有关详细信息，请参阅 [SQL 查询入门](sql-api-sql-query.md)。 以下示例代码演示了如何针对我们在上一步插入的项来运行查询。

1. 复制 `QueryItemsAsync` 方法，并将其粘贴到 `AddItemsToContainerAsync` 方法后面。

    ```csharp

    /// <summary>
    /// Run a query (using Azure Cosmos DB SQL syntax) against the container
    /// </summary>
    private async Task QueryItemsAsync()
    {
        var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

        Console.WriteLine("Running query: {0}\n", sqlQueryText);

        QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
        FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

        List<Family> families = new List<Family>();

        while (queryResultSetIterator.HasMoreResults)
        {
            FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
            foreach (Family family in currentResultSet)
            {
                families.Add(family);
                Console.WriteLine("\tRead {0}\n", family);
            }
        }
    }

    ```

1. 在 ``GetStartedDemoAsync`` 方法中添加对 ``QueryItemsAsync`` 的调用。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. 选择 F5 来运行应用程序。

祝贺！ 你已成功查询 Azure Cosmos 容器。

<a name="ReplaceItem"></a>
## <a name="step-8-replace-a-json-item"></a>步骤 8：替换 JSON 项

现在，我们将更新 Azure Cosmos DB 中的一个项。 我们将更改 `Family` 的 `IsRegistered` 属性以及某个孩子的 `Grade`。

1. 复制 `ReplaceFamilyItemAsync` 方法，并将其粘贴到 `QueryItemsAsync` 方法后面。

    ```csharp

    /// <summary>
    /// Replace an item in the container
    /// </summary>
    private async Task ReplaceFamilyItemAsync()
    {
        ItemResponse<Family> wakefieldFamilyResponse = await this.container.ReadItemAsync<Family>("Wakefield.7", new PartitionKey("Wakefield"));
        var itemBody = wakefieldFamilyResponse.Resource;

        // update registration status from false to true
        itemBody.IsRegistered = true;
        // update grade of child
        itemBody.Children[0].Grade = 6;

        // replace the item with the updated content
        wakefieldFamilyResponse = await this.container.ReplaceItemAsync<Family>(itemBody, itemBody.Id, new PartitionKey(itemBody.LastName));
        Console.WriteLine("Updated Family [{0},{1}].\n \tBody is now: {2}\n", itemBody.LastName, itemBody.Id, wakefieldFamilyResponse.Resource);
    }

    ```

1. 在 `GetStartedDemoAsync` 方法中添加对 `ReplaceFamilyItemAsync` 的调用。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. 选择 F5 来运行应用程序。

祝贺！ 你已成功替换 Azure Cosmos 项。

<a name="DeleteDocument"></a>
## <a name="step-9-delete-item"></a>步骤 9：删除项目

现在，我们将删除 Azure Cosmos DB 中的一个项。

1. 复制 `DeleteFamilyItemAsync` 方法，并将其粘贴到 `ReplaceFamilyItemAsync` 方法后面。

    ```csharp

    /// <summary>
    /// Delete an item in the container
    /// </summary>
    private async Task DeleteFamilyItemAsync()
    {
        var partitionKeyValue = "Wakefield";
        var familyId = "Wakefield.7";

        // Delete an item. Note we must provide the partition key value and id of the item to delete
        ItemResponse<Family> wakefieldFamilyResponse = await this.container.DeleteItemAsync<Family>(familyId,new PartitionKey(partitionKeyValue));
        Console.WriteLine("Deleted Family [{0},{1}]\n", partitionKeyValue, familyId);
    }

    ```

1. 在 `GetStartedDemoAsync` 方法中添加对 `DeleteFamilyItemAsync` 的调用。

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. 选择 F5 来运行应用程序。

祝贺！ 你已成功删除 Azure Cosmos 项。

<a name="DeleteDatabase"></a>
## <a name="step-10-delete-the-database"></a>步骤 10：删除数据库

现在，我们将删除数据库。 删除已创建的数据库会删除该数据库及其所有子资源。 这些资源包括容器、项、任何存储过程、用户定义的函数以及触发器。 我们还释放 `CosmosClient` 实例。

1. 复制 `DeleteDatabaseAndCleanupAsync` 方法，并将其粘贴到 `DeleteFamilyItemAsync` 方法后面。

    ```csharp

    /// <summary>
    /// Delete the database and dispose of the Cosmos Client instance
    /// </summary>
    private async Task DeleteDatabaseAndCleanupAsync()
    {
        DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
        // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

        Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

        //Dispose of CosmosClient
        this.cosmosClient.Dispose();
    }

    ```

1. 在 ``GetStartedDemoAsync`` 方法中添加对 ``DeleteDatabaseAndCleanupAsync`` 的调用。

    ```csharp

    /// <summary>
    /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    /// </summary>
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();
        await this.DeleteFamilyItemAsync();
        await this.DeleteDatabaseAndCleanupAsync();
    }

    ```

1. 选择 F5 来运行应用程序。

祝贺！ 你已成功删除 Azure Cosmos 数据库。

<a name="Run"></a>
## <a name="step-11-run-your-c-console-application-all-together"></a>步骤 11：一起运行 C# 控制台应用程序！

在 Visual Studio 中选择 F5，即可在调试模式下生成并运行应用程序。

应会在控制台窗口中看到整个应用的输出。 输出会显示我们添加的查询的结果， 并且应与下面的示例文本相符。

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

祝贺！ 已经完成了本教程，并且获得了一个正常工作的 C# 控制台应用程序！

<a name="GetSolution"></a>
## <a name="get-the-complete-tutorial-solution"></a>获取完整的教程解决方案

如果没有时间完成本教程中的步骤，或者只需下载代码示例，则可下载这些示例。

若要生成 `GetStarted` 解决方案，需要具备以下先决条件：

* 有效的 Azure 帐户。 如果没有，可以注册[试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。
* 一个 [Azure Cosmos DB 帐户][cosmos-db-create-account]。
* GitHub 上提供的 [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) 解决方案。

若要在 Visual Studio 中还原对 Azure Cosmos DB .NET SDK 的引用，请在解决方案资源管理器中右键单击此解决方案，然后选择“还原 NuGet 包”   。 接下来，在 *App.config* 文件中更新 `EndPointUri` 和 `PrimaryKey` 值，如[步骤 3：连接到 Azure Cosmos DB 帐户](#Connect)所述。

就这么简单，生成以后即可开始操作！

## <a name="next-steps"></a>后续步骤

* 需要更复杂的 ASP.NET MVC 教程？ 有关分步说明，请参阅[教程：通过 .NET SDK 开发使用 Azure Cosmos DB 的 ASP.NET Core MVC Web 应用程序](sql-api-dotnet-application.md)。
* 需要执行 Azure Cosmos DB 缩放和性能测试？ 请参阅 [Azure Cosmos DB 的性能和缩放测试](performance-testing.md)。
* 若要了解如何监视 Azure Cosmos DB 请求、使用情况和存储，请参阅[监视 Azure Cosmos DB 中的性能和存储指标](monitor-accounts.md)。
* 若要对示例数据集运行查询，请参阅[查询操场](https://www.documentdb.com/sql/demo)。
* 若要了解有关 Azure Cosmos DB 的详细信息，请参阅[欢迎使用 Azure Cosmos DB](/cosmos-db/introduction)。

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account

<!-- Update_Description: update meta properties, wording update -->