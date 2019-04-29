---
title: 使用 ASP.NET Core 和 MongoDB 构建 Web API
author: prkhandelwal
description: 本教程演示如何使用 MongoDB NoSQL 数据库构建 ASP.NET Core Web API。
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 95a5f8bdb4b302d6bdae7b5809b54f1b263e6ee4
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012859"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="de303-103">使用 ASP.NET Core 和 MongoDB 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="de303-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="de303-104">作者 [Pratik Khandelwal](https://twitter.com/K2Prk) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="de303-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="de303-105">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="de303-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="de303-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="de303-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="de303-107">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="de303-107">Configure MongoDB</span></span>
> * <span data-ttu-id="de303-108">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="de303-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="de303-109">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="de303-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="de303-110">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="de303-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="de303-111">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="de303-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="de303-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="de303-112">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="de303-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de303-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="de303-114">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="de303-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="de303-115">已安装“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2017 版本 15.9 或更高版本](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)</span><span class="sxs-lookup"><span data-stu-id="de303-115">[Visual Studio 2017 version 15.9 or later](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="de303-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="de303-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="de303-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de303-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="de303-118">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="de303-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="de303-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de303-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="de303-120">用于 Visual Studio Code 的 C#</span><span class="sxs-lookup"><span data-stu-id="de303-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="de303-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="de303-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="de303-122">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="de303-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="de303-123">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="de303-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="de303-124">Visual Studio for Mac 版本 7.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="de303-124">Visual Studio for Mac version 7.7 or later</span></span>](https://www.visualstudio.com/downloads/)
* [<span data-ttu-id="de303-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="de303-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="de303-126">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="de303-126">Configure MongoDB</span></span>

<span data-ttu-id="de303-127">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。</span><span class="sxs-lookup"><span data-stu-id="de303-127">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="de303-128">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="de303-128">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="de303-129">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="de303-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="de303-130">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="de303-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="de303-131">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="de303-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="de303-132">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="de303-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="de303-133">例如，Windows 上的 C:\\BooksData。</span><span class="sxs-lookup"><span data-stu-id="de303-133">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="de303-134">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="de303-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="de303-135">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="de303-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="de303-136">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="de303-136">Open a command shell.</span></span> <span data-ttu-id="de303-137">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="de303-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="de303-138">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="de303-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="de303-139">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="de303-139">Open another command shell instance.</span></span> <span data-ttu-id="de303-140">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="de303-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="de303-141">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="de303-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="de303-142">如果该命令尚不存在，则将创建名为 BookstoreDb 的数据库。</span><span class="sxs-lookup"><span data-stu-id="de303-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="de303-143">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="de303-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="de303-144">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="de303-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="de303-145">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="de303-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="de303-146">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="de303-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="de303-147">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="de303-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="de303-148">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="de303-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="de303-149">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="de303-149">The following result is displayed:</span></span>

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

    <span data-ttu-id="de303-150">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="de303-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="de303-151">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="de303-151">The database is ready.</span></span> <span data-ttu-id="de303-152">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="de303-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="de303-153">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="de303-153">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="de303-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de303-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="de303-155">转到“文件” > “新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="de303-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="de303-156">选择“ASP.NET Core Web 应用程序”，将项目命名为“BooksApi”，然后单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="de303-156">Select **ASP.NET Core Web Application**, name the project *BooksApi*, and click **OK**.</span></span>
1. <span data-ttu-id="de303-157">选择“.NET Core”目标框架和“ASP.NET Core 2.2”。</span><span class="sxs-lookup"><span data-stu-id="de303-157">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="de303-158">选择“API”项目模板，然后单击“确定”：</span><span class="sxs-lookup"><span data-stu-id="de303-158">Select the **API** project template, and click **OK**:</span></span>
1. <span data-ttu-id="de303-159">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="de303-159">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="de303-160">在“包管理器控制台”窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="de303-160">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="de303-161">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="de303-161">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="de303-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de303-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="de303-163">在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="de303-163">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="de303-164">将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="de303-164">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="de303-165">显示“'BooksApi' 中缺少进行生成和调试所需的资产。是否添加它们?”通知时，单击“是”。</span><span class="sxs-lookup"><span data-stu-id="de303-165">Click **Yes** when the *Required assets to build and debug are missing from 'BooksApi'. Add them?* notification appears.</span></span>
1. <span data-ttu-id="de303-166">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="de303-166">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="de303-167">打开“集成终端”并导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="de303-167">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="de303-168">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="de303-168">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="de303-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="de303-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="de303-170">转到“文件” > “新建解决方案” > “.NET Core” > “应用”。</span><span class="sxs-lookup"><span data-stu-id="de303-170">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="de303-171">选择“ASP.NET Core Web API”C# 项目模板，然后单击“下一步”。</span><span class="sxs-lookup"><span data-stu-id="de303-171">Select the **ASP.NET Core Web API** C# project template, and click **Next**.</span></span>
1. <span data-ttu-id="de303-172">从“目标框架”下拉列表中选择“.NET Core 2.2”，然后单击“下一步”。</span><span class="sxs-lookup"><span data-stu-id="de303-172">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and click **Next**.</span></span>
1. <span data-ttu-id="de303-173">输入“BooksApi”作为“项目名称”，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="de303-173">Enter *BooksApi* for the **Project Name**, and click **Create**.</span></span>
1. <span data-ttu-id="de303-174">在“解决方案”面板中，右键单击项目的“依赖项”节点，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="de303-174">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="de303-175">在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后单击“添加包”。</span><span class="sxs-lookup"><span data-stu-id="de303-175">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and click **Add Package**.</span></span>
1. <span data-ttu-id="de303-176">在“许可证接受”对话框中单击“接受”按钮。</span><span class="sxs-lookup"><span data-stu-id="de303-176">Click the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-a-model"></a><span data-ttu-id="de303-177">添加模型</span><span class="sxs-lookup"><span data-stu-id="de303-177">Add a model</span></span>

1. <span data-ttu-id="de303-178">将 Models 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="de303-178">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="de303-179">使用以下代码将 `Book` 类添加到 Models 目录：</span><span class="sxs-lookup"><span data-stu-id="de303-179">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

<span data-ttu-id="de303-180">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="de303-180">In the preceding class, the `Id` property:</span></span>

* <span data-ttu-id="de303-181">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="de303-181">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
* <span data-ttu-id="de303-182">使用 `[BsonId]` 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="de303-182">Is annotated with `[BsonId]` to designate this property as the document's primary key.</span></span>
* <span data-ttu-id="de303-183">使用 `[BsonRepresentation(BsonType.ObjectId)]` 进行批注，以允许将参数作为类型 `string` 而非 `ObjectId` 传递。</span><span class="sxs-lookup"><span data-stu-id="de303-183">Is annotated with `[BsonRepresentation(BsonType.ObjectId)]` to allow passing the parameter as type `string` instead of `ObjectId`.</span></span> <span data-ttu-id="de303-184">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="de303-184">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

<span data-ttu-id="de303-185">类中的其他属性使用 `[BsonElement]` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="de303-185">Other properties in the class are annotated with the `[BsonElement]` attribute.</span></span> <span data-ttu-id="de303-186">该属性的值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="de303-186">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-crud-operations-class"></a><span data-ttu-id="de303-187">添加 CRUD 操作类</span><span class="sxs-lookup"><span data-stu-id="de303-187">Add a CRUD operations class</span></span>

1. <span data-ttu-id="de303-188">将 Services 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="de303-188">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="de303-189">使用以下代码将 `BookService` 类添加到 Services 目录：</span><span class="sxs-lookup"><span data-stu-id="de303-189">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. <span data-ttu-id="de303-190">将 MongoDB 连接字符串添加到 appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="de303-190">Add the MongoDB connection string to *appsettings.json*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    <span data-ttu-id="de303-191">将在 `BookService` 类构造函数中访问前面的 `BookstoreDb` 属性。</span><span class="sxs-lookup"><span data-stu-id="de303-191">The preceding `BookstoreDb` property is accessed in the `BookService` class constructor.</span></span>

1. <span data-ttu-id="de303-192">在 `Startup.ConfigureServices` 中，向依赖关系注入系统注册 `BookService` 类：</span><span class="sxs-lookup"><span data-stu-id="de303-192">In `Startup.ConfigureServices`, register the `BookService` class with the Dependency Injection system:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    <span data-ttu-id="de303-193">必须执行前面的服务注册才能支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="de303-193">The preceding service registration is necessary to support constructor injection in consuming classes.</span></span>

<span data-ttu-id="de303-194">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="de303-194">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="de303-195">`MongoClient` &ndash; 读取执行数据库操作的服务器实例。</span><span class="sxs-lookup"><span data-stu-id="de303-195">`MongoClient` &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="de303-196">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="de303-196">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="de303-197">`IMongoDatabase` &ndash; 表示用于执行操作的 Mongo 数据库。</span><span class="sxs-lookup"><span data-stu-id="de303-197">`IMongoDatabase` &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="de303-198">本教程在界面上使用泛型 `GetCollection<T>(collection)` 方法来获取对特定集合中的数据的访问。</span><span class="sxs-lookup"><span data-stu-id="de303-198">This tutorial uses the generic `GetCollection<T>(collection)` method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="de303-199">调用此方法后，可以对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="de303-199">CRUD operations can be performed against the collection after this method is called.</span></span> <span data-ttu-id="de303-200">在 `GetCollection<T>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="de303-200">In the `GetCollection<T>(collection)` method call:</span></span>
  * <span data-ttu-id="de303-201">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="de303-201">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="de303-202">`T` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="de303-202">`T` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="de303-203">`GetCollection<T>(collection)` 返回 `MongoCollection` 对象，该对象表示集合。</span><span class="sxs-lookup"><span data-stu-id="de303-203">`GetCollection<T>(collection)` returns a `MongoCollection` object representing the collection.</span></span> <span data-ttu-id="de303-204">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="de303-204">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="de303-205">`Find<T>` &ndash; 返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="de303-205">`Find<T>` &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="de303-206">`InsertOne` &ndash; 插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="de303-206">`InsertOne` &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="de303-207">`ReplaceOne` &ndash; 将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="de303-207">`ReplaceOne` &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>
* <span data-ttu-id="de303-208">`DeleteOne` &ndash; 删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="de303-208">`DeleteOne` &ndash; Deletes a single document matching the provided search criteria.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="de303-209">添加控制器</span><span class="sxs-lookup"><span data-stu-id="de303-209">Add a controller</span></span>

1. <span data-ttu-id="de303-210">使用以下代码将 `BooksController` 类添加到 Controllers 目录：</span><span class="sxs-lookup"><span data-stu-id="de303-210">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    <span data-ttu-id="de303-211">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="de303-211">The preceding web API controller:</span></span>

    * <span data-ttu-id="de303-212">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="de303-212">Uses the `BookService` class to perform CRUD operations.</span></span>
    * <span data-ttu-id="de303-213">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="de303-213">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
    * <span data-ttu-id="de303-214"><xref:System.Web.Http.ApiController.CreatedAtRoute*> 方法返回 201 响应，这是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="de303-214">The <xref:System.Web.Http.ApiController.CreatedAtRoute*> method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="de303-215">`CreatedAtRoute` 还会向响应添加位置标头。</span><span class="sxs-lookup"><span data-stu-id="de303-215">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="de303-216">位置标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="de303-216">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="de303-217">请参阅 [10.2.2 201 已创建](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="de303-217">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
1. <span data-ttu-id="de303-218">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="de303-218">Build and run the app.</span></span>
1. <span data-ttu-id="de303-219">在浏览器中导航到 `http://localhost:<port>/api/books`。</span><span class="sxs-lookup"><span data-stu-id="de303-219">Navigate to `http://localhost:<port>/api/books` in your browser.</span></span> <span data-ttu-id="de303-220">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="de303-220">The following JSON response is displayed:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="de303-221">后续步骤</span><span class="sxs-lookup"><span data-stu-id="de303-221">Next steps</span></span>

<span data-ttu-id="de303-222">有关构建 ASP.NET Core Web API 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="de303-222">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="de303-223">本文的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="de303-223">Youtube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
