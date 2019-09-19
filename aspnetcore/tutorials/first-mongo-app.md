---
title: 使用 ASP.NET Core 和 MongoDB 创建 Web API
author: prkhandelwal
description: 本教程演示如何使用 MongoDB NoSQL 数据库创建 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: acf2ded8b92a8f77678af7b772ac2a69264a642c
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082380"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="0364e-103">使用 ASP.NET Core 和 MongoDB 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="0364e-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="0364e-104">作者 [Pratik Khandelwal](https://twitter.com/K2Prk) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="0364e-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0364e-105">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="0364e-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="0364e-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="0364e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0364e-107">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-107">Configure MongoDB</span></span>
> * <span data-ttu-id="0364e-108">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="0364e-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="0364e-109">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="0364e-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="0364e-110">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="0364e-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="0364e-111">自定义 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="0364e-111">Customize JSON serialization</span></span>

<span data-ttu-id="0364e-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0364e-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0364e-113">系统必备</span><span class="sxs-lookup"><span data-stu-id="0364e-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0364e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0364e-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="0364e-115">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0364e-115">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="0364e-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发  工作负载</span><span class="sxs-lookup"><span data-stu-id="0364e-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="0364e-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0364e-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0364e-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="0364e-119">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0364e-119">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="0364e-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0364e-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="0364e-121">用于 Visual Studio Code 的 C#</span><span class="sxs-lookup"><span data-stu-id="0364e-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="0364e-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0364e-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0364e-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="0364e-124">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0364e-124">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="0364e-125">Visual Studio for Mac 版本 7.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0364e-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="0364e-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="0364e-127">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-127">Configure MongoDB</span></span>

<span data-ttu-id="0364e-128">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB  中。</span><span class="sxs-lookup"><span data-stu-id="0364e-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="0364e-129">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin  添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="0364e-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="0364e-130">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="0364e-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="0364e-131">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="0364e-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="0364e-132">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="0364e-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="0364e-133">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="0364e-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="0364e-134">例如，Windows 上的 C:\\BooksData  。</span><span class="sxs-lookup"><span data-stu-id="0364e-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="0364e-135">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="0364e-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="0364e-136">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="0364e-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="0364e-137">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="0364e-137">Open a command shell.</span></span> <span data-ttu-id="0364e-138">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="0364e-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="0364e-139">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="0364e-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="0364e-140">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="0364e-140">Open another command shell instance.</span></span> <span data-ttu-id="0364e-141">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="0364e-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="0364e-142">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="0364e-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="0364e-143">如果它不存在，则将创建名为 BookstoreDb  的数据库。</span><span class="sxs-lookup"><span data-stu-id="0364e-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="0364e-144">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="0364e-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="0364e-145">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="0364e-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="0364e-146">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="0364e-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="0364e-147">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="0364e-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="0364e-148">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="0364e-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="0364e-149">本文所示的 ID 与运行此示例时的 ID 不匹配。</span><span class="sxs-lookup"><span data-stu-id="0364e-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="0364e-150">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="0364e-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="0364e-151">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="0364e-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="0364e-152">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="0364e-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="0364e-153">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="0364e-153">The database is ready.</span></span> <span data-ttu-id="0364e-154">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="0364e-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="0364e-155">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="0364e-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0364e-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0364e-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0364e-157">转到“文件”  >“新建”  >“项目”  。</span><span class="sxs-lookup"><span data-stu-id="0364e-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="0364e-158">选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="0364e-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="0364e-159">将项目命名为“BooksApi”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="0364e-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="0364e-160">选择“.NET Core”  目标框架和“ASP.NET Core 3.0”  。</span><span class="sxs-lookup"><span data-stu-id="0364e-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="0364e-161">选择“API”项目模板，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="0364e-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="0364e-162">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="0364e-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="0364e-163">在“包管理器控制台”  窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="0364e-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="0364e-164">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="0364e-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0364e-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0364e-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0364e-166">在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0364e-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="0364e-167">将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="0364e-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="0364e-168">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。  是否添加它们?”。</span><span class="sxs-lookup"><span data-stu-id="0364e-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="0364e-169">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="0364e-169">Select **Yes**.</span></span>
1. <span data-ttu-id="0364e-170">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="0364e-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="0364e-171">打开“集成终端”  并导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="0364e-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="0364e-172">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="0364e-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0364e-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0364e-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0364e-174">转到“文件”  >“新建解决方案”  >“.NET Core”  >“应用”  。</span><span class="sxs-lookup"><span data-stu-id="0364e-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="0364e-175">选择“ASP.NET Core Web API”C# 项目模板，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="0364e-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="0364e-176">从“目标框架”下拉列表中选择“.NET Core 3.0”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="0364e-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="0364e-177">输入“BooksApi”作为“项目名称”，然后选择“创建”    。</span><span class="sxs-lookup"><span data-stu-id="0364e-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="0364e-178">在“解决方案”  面板中，右键单击项目的“依赖项”  节点，然后选择“添加包”  。</span><span class="sxs-lookup"><span data-stu-id="0364e-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="0364e-179">在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包”    。</span><span class="sxs-lookup"><span data-stu-id="0364e-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="0364e-180">在“许可证接受”对话框中，选择“接受”按钮   。</span><span class="sxs-lookup"><span data-stu-id="0364e-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="0364e-181">添加实体模型</span><span class="sxs-lookup"><span data-stu-id="0364e-181">Add an entity model</span></span>

1. <span data-ttu-id="0364e-182">将 Models  目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="0364e-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="0364e-183">使用以下代码将 `Book` 类添加到 Models  目录：</span><span class="sxs-lookup"><span data-stu-id="0364e-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="0364e-184">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="0364e-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="0364e-185">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="0364e-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="0364e-186">使用 [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="0364e-186">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="0364e-187">使用 [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而不是 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构进行传递。</span><span class="sxs-lookup"><span data-stu-id="0364e-187">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="0364e-188">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="0364e-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="0364e-189">`BookName` 属性使用 [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。</span><span class="sxs-lookup"><span data-stu-id="0364e-189">The `BookName` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="0364e-190">`Name` 的属性值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="0364e-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="0364e-191">添加配置模型</span><span class="sxs-lookup"><span data-stu-id="0364e-191">Add a configuration model</span></span>

1. <span data-ttu-id="0364e-192">向 appsettings.json 添加以下数据库配置值  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="0364e-193">使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录   ：</span><span class="sxs-lookup"><span data-stu-id="0364e-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="0364e-194">前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值  。</span><span class="sxs-lookup"><span data-stu-id="0364e-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="0364e-195">JSON 和 C# 具有相同的属性名称，目的是简化映射过程。</span><span class="sxs-lookup"><span data-stu-id="0364e-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="0364e-196">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="0364e-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="0364e-197">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="0364e-197">In the preceding code:</span></span>

   * <span data-ttu-id="0364e-198">appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册  。</span><span class="sxs-lookup"><span data-stu-id="0364e-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="0364e-199">例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充  。</span><span class="sxs-lookup"><span data-stu-id="0364e-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="0364e-200">`IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。</span><span class="sxs-lookup"><span data-stu-id="0364e-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="0364e-201">在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。</span><span class="sxs-lookup"><span data-stu-id="0364e-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="0364e-202">在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="0364e-203">添加 CRUD 操作服务</span><span class="sxs-lookup"><span data-stu-id="0364e-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="0364e-204">将 Services  目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="0364e-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="0364e-205">使用以下代码将 `BookService` 类添加到 Services  目录：</span><span class="sxs-lookup"><span data-stu-id="0364e-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="0364e-206">上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。</span><span class="sxs-lookup"><span data-stu-id="0364e-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="0364e-207">使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分添加的 appsettings.json 配置值  。</span><span class="sxs-lookup"><span data-stu-id="0364e-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="0364e-208">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="0364e-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="0364e-209">上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="0364e-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="0364e-210">单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="0364e-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="0364e-211">根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="0364e-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="0364e-212">在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="0364e-213">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="0364e-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="0364e-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; 读取服务器实例，以执行数据库操作。</span><span class="sxs-lookup"><span data-stu-id="0364e-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="0364e-215">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="0364e-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="0364e-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 表示 Mongo 数据库，以执行操作。</span><span class="sxs-lookup"><span data-stu-id="0364e-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="0364e-217">本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。</span><span class="sxs-lookup"><span data-stu-id="0364e-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="0364e-218">调用此方法后，对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="0364e-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="0364e-219">在 `GetCollection<TDocument>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="0364e-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="0364e-220">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="0364e-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="0364e-221">`TDocument` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="0364e-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="0364e-222">`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。</span><span class="sxs-lookup"><span data-stu-id="0364e-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="0364e-223">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="0364e-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="0364e-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="0364e-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="0364e-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="0364e-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="0364e-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="0364e-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="0364e-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="0364e-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="0364e-228">添加控制器</span><span class="sxs-lookup"><span data-stu-id="0364e-228">Add a controller</span></span>

<span data-ttu-id="0364e-229">使用以下代码将 `BooksController` 类添加到 Controllers  目录：</span><span class="sxs-lookup"><span data-stu-id="0364e-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="0364e-230">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="0364e-230">The preceding web API controller:</span></span>

* <span data-ttu-id="0364e-231">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="0364e-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="0364e-232">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="0364e-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="0364e-233">在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。</span><span class="sxs-lookup"><span data-stu-id="0364e-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="0364e-234">状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="0364e-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="0364e-235">`CreatedAtRoute` 还会将 `Location` 标头添加到响应中。</span><span class="sxs-lookup"><span data-stu-id="0364e-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="0364e-236">`Location` 标头指定新建书籍的 URI。</span><span class="sxs-lookup"><span data-stu-id="0364e-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="0364e-237">测试 Web API</span><span class="sxs-lookup"><span data-stu-id="0364e-237">Test the web API</span></span>

1. <span data-ttu-id="0364e-238">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="0364e-238">Build and run the app.</span></span>

1. <span data-ttu-id="0364e-239">导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="0364e-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="0364e-240">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="0364e-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="0364e-241">导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="0364e-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="0364e-242">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="0364e-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="0364e-243">配置 JSON 序列化选项</span><span class="sxs-lookup"><span data-stu-id="0364e-243">Configure JSON serialization options</span></span>

<span data-ttu-id="0364e-244">关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：</span><span class="sxs-lookup"><span data-stu-id="0364e-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="0364e-245">应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。</span><span class="sxs-lookup"><span data-stu-id="0364e-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="0364e-246">`bookName` 属性应作为 `Name` 返回。</span><span class="sxs-lookup"><span data-stu-id="0364e-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="0364e-247">为满足上述要求，请进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="0364e-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="0364e-248">JSON.NET 已从 ASP.NET 共享框架中删除。</span><span class="sxs-lookup"><span data-stu-id="0364e-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="0364e-249">将包引用添加到 [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)。</span><span class="sxs-lookup"><span data-stu-id="0364e-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="0364e-250">在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddMvc` 方法调用：</span><span class="sxs-lookup"><span data-stu-id="0364e-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="0364e-251">通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="0364e-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="0364e-252">例如，`Book` 类的 `Author` 属性序列化为 `Author`。</span><span class="sxs-lookup"><span data-stu-id="0364e-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="0364e-253">在 Models/Book.cs 中，使用以下 [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-253">In *Models/Book.cs*, annotate the `BookName` property with the following [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="0364e-254">`[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="0364e-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="0364e-255">在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="0364e-256">重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。</span><span class="sxs-lookup"><span data-stu-id="0364e-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="0364e-257">注意 JSON 属性名称中的区别。</span><span class="sxs-lookup"><span data-stu-id="0364e-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0364e-258">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="0364e-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="0364e-259">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="0364e-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0364e-260">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-260">Configure MongoDB</span></span>
> * <span data-ttu-id="0364e-261">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="0364e-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="0364e-262">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="0364e-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="0364e-263">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="0364e-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="0364e-264">自定义 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="0364e-264">Customize JSON serialization</span></span>

<span data-ttu-id="0364e-265">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0364e-265">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0364e-266">系统必备</span><span class="sxs-lookup"><span data-stu-id="0364e-266">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0364e-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0364e-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="0364e-268">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="0364e-268">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="0364e-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发  工作负载</span><span class="sxs-lookup"><span data-stu-id="0364e-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="0364e-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0364e-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0364e-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="0364e-272">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="0364e-272">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="0364e-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0364e-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="0364e-274">用于 Visual Studio Code 的 C#</span><span class="sxs-lookup"><span data-stu-id="0364e-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="0364e-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0364e-276">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0364e-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="0364e-277">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="0364e-277">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="0364e-278">Visual Studio for Mac 版本 7.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0364e-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="0364e-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="0364e-280">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="0364e-280">Configure MongoDB</span></span>

<span data-ttu-id="0364e-281">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB  中。</span><span class="sxs-lookup"><span data-stu-id="0364e-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="0364e-282">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin  添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="0364e-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="0364e-283">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="0364e-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="0364e-284">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="0364e-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="0364e-285">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="0364e-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="0364e-286">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="0364e-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="0364e-287">例如，Windows 上的 C:\\BooksData  。</span><span class="sxs-lookup"><span data-stu-id="0364e-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="0364e-288">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="0364e-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="0364e-289">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="0364e-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="0364e-290">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="0364e-290">Open a command shell.</span></span> <span data-ttu-id="0364e-291">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="0364e-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="0364e-292">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="0364e-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="0364e-293">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="0364e-293">Open another command shell instance.</span></span> <span data-ttu-id="0364e-294">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="0364e-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="0364e-295">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="0364e-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="0364e-296">如果它不存在，则将创建名为 BookstoreDb  的数据库。</span><span class="sxs-lookup"><span data-stu-id="0364e-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="0364e-297">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="0364e-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="0364e-298">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="0364e-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="0364e-299">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="0364e-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="0364e-300">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="0364e-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="0364e-301">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="0364e-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="0364e-302">本文所示的 ID 与运行此示例时的 ID 不匹配。</span><span class="sxs-lookup"><span data-stu-id="0364e-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="0364e-303">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="0364e-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="0364e-304">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="0364e-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="0364e-305">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="0364e-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="0364e-306">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="0364e-306">The database is ready.</span></span> <span data-ttu-id="0364e-307">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="0364e-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="0364e-308">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="0364e-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0364e-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0364e-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0364e-310">转到“文件”  >“新建”  >“项目”  。</span><span class="sxs-lookup"><span data-stu-id="0364e-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="0364e-311">选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="0364e-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="0364e-312">将项目命名为“BooksApi”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="0364e-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="0364e-313">选择“.NET Core”  目标框架和“ASP.NET Core 2.2”  。</span><span class="sxs-lookup"><span data-stu-id="0364e-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="0364e-314">选择“API”项目模板，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="0364e-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="0364e-315">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="0364e-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="0364e-316">在“包管理器控制台”  窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="0364e-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="0364e-317">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="0364e-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0364e-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0364e-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0364e-319">在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0364e-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="0364e-320">将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="0364e-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="0364e-321">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。  是否添加它们?”。</span><span class="sxs-lookup"><span data-stu-id="0364e-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="0364e-322">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="0364e-322">Select **Yes**.</span></span>
1. <span data-ttu-id="0364e-323">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="0364e-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="0364e-324">打开“集成终端”  并导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="0364e-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="0364e-325">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="0364e-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0364e-326">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0364e-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0364e-327">转到“文件”  >“新建解决方案”  >“.NET Core”  >“应用”  。</span><span class="sxs-lookup"><span data-stu-id="0364e-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="0364e-328">选择“ASP.NET Core Web API”C# 项目模板，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="0364e-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="0364e-329">从“目标框架”下拉列表中选择“.NET Core 2.2”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="0364e-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="0364e-330">输入“BooksApi”作为“项目名称”，然后选择“创建”    。</span><span class="sxs-lookup"><span data-stu-id="0364e-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="0364e-331">在“解决方案”  面板中，右键单击项目的“依赖项”  节点，然后选择“添加包”  。</span><span class="sxs-lookup"><span data-stu-id="0364e-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="0364e-332">在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包”    。</span><span class="sxs-lookup"><span data-stu-id="0364e-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="0364e-333">在“许可证接受”对话框中，选择“接受”按钮   。</span><span class="sxs-lookup"><span data-stu-id="0364e-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="0364e-334">添加实体模型</span><span class="sxs-lookup"><span data-stu-id="0364e-334">Add an entity model</span></span>

1. <span data-ttu-id="0364e-335">将 Models  目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="0364e-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="0364e-336">使用以下代码将 `Book` 类添加到 Models  目录：</span><span class="sxs-lookup"><span data-stu-id="0364e-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="0364e-337">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="0364e-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="0364e-338">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="0364e-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="0364e-339">使用 [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="0364e-339">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="0364e-340">使用 [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而不是 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构进行传递。</span><span class="sxs-lookup"><span data-stu-id="0364e-340">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="0364e-341">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="0364e-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="0364e-342">`BookName` 属性使用 [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。</span><span class="sxs-lookup"><span data-stu-id="0364e-342">The `BookName` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="0364e-343">`Name` 的属性值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="0364e-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="0364e-344">添加配置模型</span><span class="sxs-lookup"><span data-stu-id="0364e-344">Add a configuration model</span></span>

1. <span data-ttu-id="0364e-345">向 appsettings.json 添加以下数据库配置值  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="0364e-346">使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录   ：</span><span class="sxs-lookup"><span data-stu-id="0364e-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="0364e-347">前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值  。</span><span class="sxs-lookup"><span data-stu-id="0364e-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="0364e-348">JSON 和 C# 具有相同的属性名称，目的是简化映射过程。</span><span class="sxs-lookup"><span data-stu-id="0364e-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="0364e-349">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="0364e-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="0364e-350">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="0364e-350">In the preceding code:</span></span>

   * <span data-ttu-id="0364e-351">appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册  。</span><span class="sxs-lookup"><span data-stu-id="0364e-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="0364e-352">例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充  。</span><span class="sxs-lookup"><span data-stu-id="0364e-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="0364e-353">`IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。</span><span class="sxs-lookup"><span data-stu-id="0364e-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="0364e-354">在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。</span><span class="sxs-lookup"><span data-stu-id="0364e-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="0364e-355">在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="0364e-356">添加 CRUD 操作服务</span><span class="sxs-lookup"><span data-stu-id="0364e-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="0364e-357">将 Services  目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="0364e-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="0364e-358">使用以下代码将 `BookService` 类添加到 Services  目录：</span><span class="sxs-lookup"><span data-stu-id="0364e-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="0364e-359">上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。</span><span class="sxs-lookup"><span data-stu-id="0364e-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="0364e-360">使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分添加的 appsettings.json 配置值  。</span><span class="sxs-lookup"><span data-stu-id="0364e-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="0364e-361">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="0364e-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="0364e-362">上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="0364e-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="0364e-363">单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="0364e-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="0364e-364">根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="0364e-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="0364e-365">在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="0364e-366">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="0364e-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="0364e-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; 读取服务器实例，以执行数据库操作。</span><span class="sxs-lookup"><span data-stu-id="0364e-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="0364e-368">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="0364e-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="0364e-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 表示 Mongo 数据库，以执行操作。</span><span class="sxs-lookup"><span data-stu-id="0364e-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="0364e-370">本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。</span><span class="sxs-lookup"><span data-stu-id="0364e-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="0364e-371">调用此方法后，对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="0364e-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="0364e-372">在 `GetCollection<TDocument>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="0364e-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="0364e-373">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="0364e-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="0364e-374">`TDocument` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="0364e-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="0364e-375">`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。</span><span class="sxs-lookup"><span data-stu-id="0364e-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="0364e-376">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="0364e-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="0364e-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="0364e-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="0364e-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="0364e-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="0364e-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="0364e-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="0364e-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="0364e-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="0364e-381">添加控制器</span><span class="sxs-lookup"><span data-stu-id="0364e-381">Add a controller</span></span>

<span data-ttu-id="0364e-382">使用以下代码将 `BooksController` 类添加到 Controllers  目录：</span><span class="sxs-lookup"><span data-stu-id="0364e-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="0364e-383">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="0364e-383">The preceding web API controller:</span></span>

* <span data-ttu-id="0364e-384">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="0364e-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="0364e-385">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="0364e-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="0364e-386">在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。</span><span class="sxs-lookup"><span data-stu-id="0364e-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="0364e-387">状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="0364e-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="0364e-388">`CreatedAtRoute` 还会将 `Location` 标头添加到响应中。</span><span class="sxs-lookup"><span data-stu-id="0364e-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="0364e-389">`Location` 标头指定新建书籍的 URI。</span><span class="sxs-lookup"><span data-stu-id="0364e-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="0364e-390">测试 Web API</span><span class="sxs-lookup"><span data-stu-id="0364e-390">Test the web API</span></span>

1. <span data-ttu-id="0364e-391">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="0364e-391">Build and run the app.</span></span>

1. <span data-ttu-id="0364e-392">导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="0364e-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="0364e-393">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="0364e-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="0364e-394">导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="0364e-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="0364e-395">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="0364e-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="0364e-396">配置 JSON 序列化选项</span><span class="sxs-lookup"><span data-stu-id="0364e-396">Configure JSON serialization options</span></span>

<span data-ttu-id="0364e-397">关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：</span><span class="sxs-lookup"><span data-stu-id="0364e-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="0364e-398">应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。</span><span class="sxs-lookup"><span data-stu-id="0364e-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="0364e-399">`bookName` 属性应作为 `Name` 返回。</span><span class="sxs-lookup"><span data-stu-id="0364e-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="0364e-400">为满足上述要求，请进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="0364e-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="0364e-401">在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddMvc` 方法调用：</span><span class="sxs-lookup"><span data-stu-id="0364e-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="0364e-402">通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="0364e-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="0364e-403">例如，`Book` 类的 `Author` 属性序列化为 `Author`。</span><span class="sxs-lookup"><span data-stu-id="0364e-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="0364e-404">在 Models/Book.cs 中，使用以下 [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-404">In *Models/Book.cs*, annotate the `BookName` property with the following [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="0364e-405">`[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="0364e-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="0364e-406">在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用  ：</span><span class="sxs-lookup"><span data-stu-id="0364e-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="0364e-407">重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。</span><span class="sxs-lookup"><span data-stu-id="0364e-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="0364e-408">注意 JSON 属性名称中的区别。</span><span class="sxs-lookup"><span data-stu-id="0364e-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="next-steps"></a><span data-ttu-id="0364e-409">后续步骤</span><span class="sxs-lookup"><span data-stu-id="0364e-409">Next steps</span></span>

<span data-ttu-id="0364e-410">有关构建 ASP.NET Core Web API 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="0364e-410">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="0364e-411">本文的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="0364e-411">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
