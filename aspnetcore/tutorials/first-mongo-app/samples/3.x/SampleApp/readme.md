---
page_type: sample
description: 本教程演示如何使用 MongoDB NoSQL 数据库创建 ASP.NET Core Web API。
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 95a2a6fcda0a4f7148183981f7dbacd06388329d
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106515"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="ca4a1-102">使用 ASP.NET Core 和 MongoDB 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="ca4a1-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="ca4a1-103">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="ca4a1-104">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="ca4a1-105">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="ca4a1-105">Configure MongoDB</span></span>
* <span data-ttu-id="ca4a1-106">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="ca4a1-106">Create a MongoDB database</span></span>
* <span data-ttu-id="ca4a1-107">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="ca4a1-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="ca4a1-108">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="ca4a1-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="ca4a1-109">自定义 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="ca4a1-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ca4a1-110">先决条件</span><span class="sxs-lookup"><span data-stu-id="ca4a1-110">Prerequisites</span></span>

* [<span data-ttu-id="ca4a1-111">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="ca4a1-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="ca4a1-112">[Visual Studio 2019 预览版](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) 与 ASP.NET 和 Web 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="ca4a1-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="ca4a1-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ca4a1-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="ca4a1-114">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="ca4a1-114">Configure MongoDB</span></span>

<span data-ttu-id="ca4a1-115">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="ca4a1-116">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="ca4a1-117">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="ca4a1-118">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="ca4a1-119">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="ca4a1-120">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="ca4a1-121">例如，Windows 上的 C:\\BooksData。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="ca4a1-122">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="ca4a1-123">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="ca4a1-124">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-124">Open a command shell.</span></span> <span data-ttu-id="ca4a1-125">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="ca4a1-126">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="ca4a1-127">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-127">Open another command shell instance.</span></span> <span data-ttu-id="ca4a1-128">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="ca4a1-129">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="ca4a1-130">如果它不存在，则将创建名为 BookstoreDb 的数据库。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="ca4a1-131">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="ca4a1-132">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="ca4a1-133">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="ca4a1-134">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="ca4a1-135">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-135">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > <span data-ttu-id="ca4a1-136">本文所示的 ID 与运行此示例时的 ID 不匹配。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="ca4a1-137">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="ca4a1-138">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-138">The following result is displayed:</span></span>

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    <span data-ttu-id="ca4a1-139">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="ca4a1-140">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-140">The database is ready.</span></span> <span data-ttu-id="ca4a1-141">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="ca4a1-142">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="ca4a1-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="ca4a1-143">转到“文件” > “新建” > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="ca4a1-144">选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="ca4a1-145">将项目命名为“BooksApi”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="ca4a1-146">选择“.NET Core”目标框架和“ASP.NET Core 3.0”。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="ca4a1-147">选择“API”项目模板，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="ca4a1-148">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ca4a1-149">在“包管理器控制台”窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="ca4a1-150">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="ca4a1-151">添加实体模型</span><span class="sxs-lookup"><span data-stu-id="ca4a1-151">Add an entity model</span></span>

1. <span data-ttu-id="ca4a1-152">将 Models 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="ca4a1-153">使用以下代码将 `Book` 类添加到 Models 目录：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    <span data-ttu-id="ca4a1-154">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="ca4a1-155">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="ca4a1-156">使用 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="ca4a1-157">使用 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而非 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构传递。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="ca4a1-158">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="ca4a1-159">`BookName` 属性使用 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="ca4a1-160">`Name` 的属性值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="ca4a1-161">添加配置模型</span><span class="sxs-lookup"><span data-stu-id="ca4a1-161">Add a configuration model</span></span>

1. <span data-ttu-id="ca4a1-162">向 appsettings.json 添加以下数据库配置值：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="ca4a1-163">使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录 ：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    <span data-ttu-id="ca4a1-164">前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="ca4a1-165">JSON 和 C# 具有相同的属性名称，目的是简化映射过程。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="ca4a1-166">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    <span data-ttu-id="ca4a1-167">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-167">In the preceding code:</span></span>

    * <span data-ttu-id="ca4a1-168">appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="ca4a1-169">例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="ca4a1-170">`IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="ca4a1-171">在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="ca4a1-172">在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="ca4a1-173">添加 CRUD 操作服务</span><span class="sxs-lookup"><span data-stu-id="ca4a1-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="ca4a1-174">将 Services 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="ca4a1-175">使用以下代码将 `BookService` 类添加到 Services 目录：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    <span data-ttu-id="ca4a1-176">上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="ca4a1-177">使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分添加的 appsettings.json 配置值。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="ca4a1-178">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    <span data-ttu-id="ca4a1-179">上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="ca4a1-180">单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="ca4a1-181">根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="ca4a1-182">在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="ca4a1-183">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="ca4a1-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：读取用于执行数据库操作的服务器实例。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="ca4a1-185">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="ca4a1-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：表示用于执行操作的 Mongo 数据库。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="ca4a1-187">本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="ca4a1-188">调用此方法后，对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="ca4a1-189">在 `GetCollection<TDocument>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="ca4a1-190">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="ca4a1-191">`TDocument` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="ca4a1-192">`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="ca4a1-193">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="ca4a1-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="ca4a1-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="ca4a1-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="ca4a1-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="ca4a1-198">添加控制器</span><span class="sxs-lookup"><span data-stu-id="ca4a1-198">Add a controller</span></span>

<span data-ttu-id="ca4a1-199">使用以下代码将 `BooksController` 类添加到 Controllers 目录：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

<span data-ttu-id="ca4a1-200">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-200">The preceding web API controller:</span></span>

* <span data-ttu-id="ca4a1-201">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="ca4a1-202">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="ca4a1-203">在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="ca4a1-204">状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="ca4a1-205">`CreatedAtRoute` 还会将 `Location` 标头添加到响应中。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="ca4a1-206">`Location` 标头指定新建书籍的 URI。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="ca4a1-207">测试 Web API</span><span class="sxs-lookup"><span data-stu-id="ca4a1-207">Test the web API</span></span>

1. <span data-ttu-id="ca4a1-208">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-208">Build and run the app.</span></span>

1. <span data-ttu-id="ca4a1-209">导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="ca4a1-210">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-210">The following JSON response is displayed:</span></span>

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. <span data-ttu-id="ca4a1-211">导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="ca4a1-212">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="ca4a1-213">配置 JSON 序列化选项</span><span class="sxs-lookup"><span data-stu-id="ca4a1-213">Configure JSON serialization options</span></span>

<span data-ttu-id="ca4a1-214">关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="ca4a1-215">应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="ca4a1-216">`bookName` 属性应作为 `Name` 返回。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="ca4a1-217">为满足上述要求，请进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="ca4a1-218">JSON.NET 已从 ASP.NET 共享框架中删除。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="ca4a1-219">将包引用添加到 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-219">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="ca4a1-220">在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddMvc` 方法调用：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    <span data-ttu-id="ca4a1-221">通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="ca4a1-222">例如，`Book` 类的 `Author` 属性序列化为 `Author`。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="ca4a1-223">在 Models/Book.cs 中，使用以下 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="ca4a1-224">`[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="ca4a1-225">在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="ca4a1-226">重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="ca4a1-227">注意 JSON 属性名称中的区别。</span><span class="sxs-lookup"><span data-stu-id="ca4a1-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ca4a1-228">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ca4a1-228">Next steps</span></span>

<span data-ttu-id="ca4a1-229">有关构建 ASP.NET Core Web API 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="ca4a1-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="ca4a1-230">本文的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="ca4a1-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="ca4a1-231">使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="ca4a1-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
