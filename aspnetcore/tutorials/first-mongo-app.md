---
title: 使用 ASP.NET Core 和 MongoDB 创建 Web API
author: prkhandelwal
description: 本教程演示如何使用 MongoDB NoSQL 数据库创建 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mongo-app
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060737"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="b2df9-103">使用 ASP.NET Core 和 MongoDB 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="b2df9-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="b2df9-104">作者 [Pratik Khandelwal](https://twitter.com/K2Prk) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b2df9-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2df9-105">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2df9-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="b2df9-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b2df9-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b2df9-107">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-107">Configure MongoDB</span></span>
> * <span data-ttu-id="b2df9-108">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="b2df9-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="b2df9-109">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="b2df9-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="b2df9-110">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="b2df9-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="b2df9-111">自定义 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="b2df9-111">Customize JSON serialization</span></span>

<span data-ttu-id="b2df9-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b2df9-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b2df9-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="b2df9-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2df9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2df9-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="b2df9-115">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b2df9-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="b2df9-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="b2df9-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="b2df9-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2df9-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2df9-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="b2df9-119">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b2df9-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b2df9-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2df9-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="b2df9-121">用于 Visual Studio Code 的 C#</span><span class="sxs-lookup"><span data-stu-id="b2df9-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="b2df9-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2df9-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2df9-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="b2df9-124">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b2df9-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b2df9-125">Visual Studio for Mac 版本 7.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b2df9-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="b2df9-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="b2df9-127">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-127">Configure MongoDB</span></span>

<span data-ttu-id="b2df9-128">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。</span><span class="sxs-lookup"><span data-stu-id="b2df9-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="b2df9-129">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="b2df9-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="b2df9-130">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="b2df9-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="b2df9-131">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="b2df9-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="b2df9-132">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="b2df9-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="b2df9-133">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="b2df9-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="b2df9-134">例如，Windows 上的 C:\\BooksData。</span><span class="sxs-lookup"><span data-stu-id="b2df9-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="b2df9-135">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="b2df9-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="b2df9-136">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="b2df9-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="b2df9-137">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="b2df9-137">Open a command shell.</span></span> <span data-ttu-id="b2df9-138">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="b2df9-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="b2df9-139">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="b2df9-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="b2df9-140">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="b2df9-140">Open another command shell instance.</span></span> <span data-ttu-id="b2df9-141">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="b2df9-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="b2df9-142">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="b2df9-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="b2df9-143">如果它不存在，则将创建名为 BookstoreDb 的数据库。</span><span class="sxs-lookup"><span data-stu-id="b2df9-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="b2df9-144">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="b2df9-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="b2df9-145">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="b2df9-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="b2df9-146">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="b2df9-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="b2df9-147">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="b2df9-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="b2df9-148">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="b2df9-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="b2df9-149">本文所示的 ID 与运行此示例时的 ID 不匹配。</span><span class="sxs-lookup"><span data-stu-id="b2df9-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="b2df9-150">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="b2df9-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="b2df9-151">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="b2df9-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="b2df9-152">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="b2df9-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="b2df9-153">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="b2df9-153">The database is ready.</span></span> <span data-ttu-id="b2df9-154">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="b2df9-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="b2df9-155">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="b2df9-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2df9-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2df9-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b2df9-157">转到“文件”>“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="b2df9-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="b2df9-158">选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="b2df9-159">将项目命名为“BooksApi”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="b2df9-159">Name the project *BooksApi* , and select **Create**.</span></span>
1. <span data-ttu-id="b2df9-160">选择“.NET Core”目标框架和“ASP.NET Core 3.0”。</span><span class="sxs-lookup"><span data-stu-id="b2df9-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="b2df9-161">选择“API”项目模板，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="b2df9-162">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="b2df9-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="b2df9-163">在“包管理器控制台”窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="b2df9-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="b2df9-164">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="b2df9-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2df9-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2df9-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b2df9-166">在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b2df9-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="b2df9-167">将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="b2df9-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="b2df9-168">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。是否添加它们?”。</span><span class="sxs-lookup"><span data-stu-id="b2df9-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="b2df9-169">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-169">Select **Yes**.</span></span>
1. <span data-ttu-id="b2df9-170">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="b2df9-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="b2df9-171">打开“集成终端”并导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="b2df9-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="b2df9-172">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="b2df9-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2df9-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2df9-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b2df9-174">在早于版本 8.6 的 Visual Studio for Mac 中，从边栏中依次选择“文件” > “新建解决方案” > “.NET Core” > “应用”。   </span><span class="sxs-lookup"><span data-stu-id="b2df9-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="b2df9-175">在版本 8.6 或更高版本中，从边栏中依次选择“文件” > “新建解决方案” > “Web 和控制台” > “应用”。   </span><span class="sxs-lookup"><span data-stu-id="b2df9-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="b2df9-176">依次选择“ASP.NET Core”>“API”C# 项目模板，然后选择“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="b2df9-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="b2df9-177">从“目标框架”下拉列表中选择“.NET Core 3.1”，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="b2df9-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="b2df9-178">输入“BooksApi”作为“项目名称”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-178">Enter *BooksApi* for the **Project Name** , and select **Create**.</span></span>
1. <span data-ttu-id="b2df9-179">在“解决方案”面板中，右键单击项目的“依赖项”节点，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="b2df9-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="b2df9-180">在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="b2df9-181">在“许可证接受”对话框中，选择“接受”按钮 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="b2df9-182">添加实体模型</span><span class="sxs-lookup"><span data-stu-id="b2df9-182">Add an entity model</span></span>

1. <span data-ttu-id="b2df9-183">将 Models 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="b2df9-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="b2df9-184">使用以下代码将 `Book` 类添加到 Models 目录：</span><span class="sxs-lookup"><span data-stu-id="b2df9-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="b2df9-185">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="b2df9-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="b2df9-186">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="b2df9-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="b2df9-187">使用 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="b2df9-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="b2df9-188">使用 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而非 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构传递。</span><span class="sxs-lookup"><span data-stu-id="b2df9-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="b2df9-189">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="b2df9-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="b2df9-190">`BookName` 属性使用 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。</span><span class="sxs-lookup"><span data-stu-id="b2df9-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="b2df9-191">`Name` 的属性值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="b2df9-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="b2df9-192">添加配置模型</span><span class="sxs-lookup"><span data-stu-id="b2df9-192">Add a configuration model</span></span>

1. <span data-ttu-id="b2df9-193">向 appsettings.json 添加以下数据库配置值：</span><span class="sxs-lookup"><span data-stu-id="b2df9-193">Add the following database configuration values to *appsettings.json* :</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="b2df9-194">使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录 ：</span><span class="sxs-lookup"><span data-stu-id="b2df9-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="b2df9-195">前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值。</span><span class="sxs-lookup"><span data-stu-id="b2df9-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="b2df9-196">JSON 和 C# 具有相同的属性名称，目的是简化映射过程。</span><span class="sxs-lookup"><span data-stu-id="b2df9-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="b2df9-197">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="b2df9-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="b2df9-198">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="b2df9-198">In the preceding code:</span></span>

   * <span data-ttu-id="b2df9-199">appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册。</span><span class="sxs-lookup"><span data-stu-id="b2df9-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="b2df9-200">例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充。</span><span class="sxs-lookup"><span data-stu-id="b2df9-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="b2df9-201">`IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。</span><span class="sxs-lookup"><span data-stu-id="b2df9-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b2df9-202">在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。</span><span class="sxs-lookup"><span data-stu-id="b2df9-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="b2df9-203">在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用：</span><span class="sxs-lookup"><span data-stu-id="b2df9-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="b2df9-204">添加 CRUD 操作服务</span><span class="sxs-lookup"><span data-stu-id="b2df9-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="b2df9-205">将 Services 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="b2df9-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="b2df9-206">使用以下代码将 `BookService` 类添加到 Services 目录：</span><span class="sxs-lookup"><span data-stu-id="b2df9-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="b2df9-207">上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。</span><span class="sxs-lookup"><span data-stu-id="b2df9-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="b2df9-208">使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分中添加的 appsettings.json 配置值。</span><span class="sxs-lookup"><span data-stu-id="b2df9-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="b2df9-209">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="b2df9-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="b2df9-210">上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="b2df9-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="b2df9-211">单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="b2df9-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="b2df9-212">根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="b2df9-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="b2df9-213">在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用：</span><span class="sxs-lookup"><span data-stu-id="b2df9-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="b2df9-214">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="b2df9-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="b2df9-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：读取用于执行数据库操作的服务器实例。</span><span class="sxs-lookup"><span data-stu-id="b2df9-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="b2df9-216">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="b2df9-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="b2df9-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：表示用于执行操作的 Mongo 数据库。</span><span class="sxs-lookup"><span data-stu-id="b2df9-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="b2df9-218">本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。</span><span class="sxs-lookup"><span data-stu-id="b2df9-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="b2df9-219">调用此方法后，对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b2df9-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="b2df9-220">在 `GetCollection<TDocument>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="b2df9-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="b2df9-221">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="b2df9-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="b2df9-222">`TDocument` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="b2df9-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="b2df9-223">`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。</span><span class="sxs-lookup"><span data-stu-id="b2df9-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="b2df9-224">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="b2df9-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="b2df9-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="b2df9-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="b2df9-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="b2df9-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="b2df9-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="b2df9-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="b2df9-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="b2df9-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="b2df9-229">添加控制器</span><span class="sxs-lookup"><span data-stu-id="b2df9-229">Add a controller</span></span>

<span data-ttu-id="b2df9-230">使用以下代码将 `BooksController` 类添加到 Controllers 目录：</span><span class="sxs-lookup"><span data-stu-id="b2df9-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="b2df9-231">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="b2df9-231">The preceding web API controller:</span></span>

* <span data-ttu-id="b2df9-232">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b2df9-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="b2df9-233">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="b2df9-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="b2df9-234">在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。</span><span class="sxs-lookup"><span data-stu-id="b2df9-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="b2df9-235">状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="b2df9-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="b2df9-236">`CreatedAtRoute` 还会将 `Location` 标头添加到响应中。</span><span class="sxs-lookup"><span data-stu-id="b2df9-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="b2df9-237">`Location` 标头指定新建书籍的 URI。</span><span class="sxs-lookup"><span data-stu-id="b2df9-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="b2df9-238">测试 Web API</span><span class="sxs-lookup"><span data-stu-id="b2df9-238">Test the web API</span></span>

1. <span data-ttu-id="b2df9-239">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="b2df9-239">Build and run the app.</span></span>

1. <span data-ttu-id="b2df9-240">导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="b2df9-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="b2df9-241">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="b2df9-241">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="b2df9-242">导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="b2df9-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="b2df9-243">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="b2df9-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="b2df9-244">配置 JSON 序列化选项</span><span class="sxs-lookup"><span data-stu-id="b2df9-244">Configure JSON serialization options</span></span>

<span data-ttu-id="b2df9-245">关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：</span><span class="sxs-lookup"><span data-stu-id="b2df9-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="b2df9-246">应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。</span><span class="sxs-lookup"><span data-stu-id="b2df9-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="b2df9-247">`bookName` 属性应作为 `Name` 返回。</span><span class="sxs-lookup"><span data-stu-id="b2df9-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="b2df9-248">为满足上述要求，请进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="b2df9-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="b2df9-249">JSON.NET 已从 ASP.NET 共享框架中删除。</span><span class="sxs-lookup"><span data-stu-id="b2df9-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="b2df9-250">将包引用添加到 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)。</span><span class="sxs-lookup"><span data-stu-id="b2df9-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="b2df9-251">在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddControllers` 方法调用：</span><span class="sxs-lookup"><span data-stu-id="b2df9-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="b2df9-252">通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="b2df9-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="b2df9-253">例如，`Book` 类的 `Author` 属性序列化为 `Author`。</span><span class="sxs-lookup"><span data-stu-id="b2df9-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="b2df9-254">在 Models/Book.cs 中，使用以下 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性：</span><span class="sxs-lookup"><span data-stu-id="b2df9-254">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="b2df9-255">`[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="b2df9-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="b2df9-256">在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用：</span><span class="sxs-lookup"><span data-stu-id="b2df9-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="b2df9-257">重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。</span><span class="sxs-lookup"><span data-stu-id="b2df9-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="b2df9-258">注意 JSON 属性名称中的区别。</span><span class="sxs-lookup"><span data-stu-id="b2df9-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2df9-259">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="b2df9-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="b2df9-260">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b2df9-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b2df9-261">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-261">Configure MongoDB</span></span>
> * <span data-ttu-id="b2df9-262">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="b2df9-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="b2df9-263">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="b2df9-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="b2df9-264">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="b2df9-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="b2df9-265">自定义 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="b2df9-265">Customize JSON serialization</span></span>

<span data-ttu-id="b2df9-266">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b2df9-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b2df9-267">先决条件</span><span class="sxs-lookup"><span data-stu-id="b2df9-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2df9-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2df9-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="b2df9-269">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="b2df9-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="b2df9-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="b2df9-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="b2df9-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2df9-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2df9-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="b2df9-273">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="b2df9-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b2df9-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2df9-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="b2df9-275">用于 Visual Studio Code 的 C#</span><span class="sxs-lookup"><span data-stu-id="b2df9-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="b2df9-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2df9-277">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2df9-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="b2df9-278">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="b2df9-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="b2df9-279">Visual Studio for Mac 版本 7.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="b2df9-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="b2df9-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="b2df9-281">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="b2df9-281">Configure MongoDB</span></span>

<span data-ttu-id="b2df9-282">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。</span><span class="sxs-lookup"><span data-stu-id="b2df9-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="b2df9-283">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="b2df9-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="b2df9-284">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="b2df9-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="b2df9-285">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="b2df9-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="b2df9-286">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="b2df9-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="b2df9-287">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="b2df9-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="b2df9-288">例如，Windows 上的 C:\\BooksData。</span><span class="sxs-lookup"><span data-stu-id="b2df9-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="b2df9-289">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="b2df9-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="b2df9-290">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="b2df9-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="b2df9-291">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="b2df9-291">Open a command shell.</span></span> <span data-ttu-id="b2df9-292">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="b2df9-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="b2df9-293">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="b2df9-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="b2df9-294">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="b2df9-294">Open another command shell instance.</span></span> <span data-ttu-id="b2df9-295">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="b2df9-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="b2df9-296">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="b2df9-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="b2df9-297">如果它不存在，则将创建名为 BookstoreDb 的数据库。</span><span class="sxs-lookup"><span data-stu-id="b2df9-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="b2df9-298">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="b2df9-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="b2df9-299">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="b2df9-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="b2df9-300">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="b2df9-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="b2df9-301">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="b2df9-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="b2df9-302">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="b2df9-302">The following result is displayed:</span></span>

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
   > <span data-ttu-id="b2df9-303">本文所示的 ID 与运行此示例时的 ID 不匹配。</span><span class="sxs-lookup"><span data-stu-id="b2df9-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="b2df9-304">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="b2df9-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="b2df9-305">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="b2df9-305">The following result is displayed:</span></span>

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

   <span data-ttu-id="b2df9-306">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="b2df9-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="b2df9-307">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="b2df9-307">The database is ready.</span></span> <span data-ttu-id="b2df9-308">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="b2df9-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="b2df9-309">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="b2df9-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2df9-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2df9-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b2df9-311">转到“文件”>“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="b2df9-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="b2df9-312">选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="b2df9-313">将项目命名为“BooksApi”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="b2df9-313">Name the project *BooksApi* , and select **Create**.</span></span>
1. <span data-ttu-id="b2df9-314">选择“.NET Core”目标框架和“ASP.NET Core 2.2”。</span><span class="sxs-lookup"><span data-stu-id="b2df9-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="b2df9-315">选择“API”项目模板，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="b2df9-316">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="b2df9-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="b2df9-317">在“包管理器控制台”窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="b2df9-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="b2df9-318">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="b2df9-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b2df9-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b2df9-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b2df9-320">在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b2df9-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="b2df9-321">将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="b2df9-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="b2df9-322">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。是否添加它们?”。</span><span class="sxs-lookup"><span data-stu-id="b2df9-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="b2df9-323">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-323">Select **Yes**.</span></span>
1. <span data-ttu-id="b2df9-324">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="b2df9-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="b2df9-325">打开“集成终端”并导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="b2df9-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="b2df9-326">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="b2df9-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b2df9-327">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b2df9-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b2df9-328">在早于版本 8.6 的 Visual Studio for Mac 中，从边栏中依次选择“文件” > “新建解决方案” > “.NET Core” > “应用”。   </span><span class="sxs-lookup"><span data-stu-id="b2df9-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="b2df9-329">在版本 8.6 或更高版本中，从边栏中依次选择“文件” > “新建解决方案” > “Web 和控制台” > “应用”。   </span><span class="sxs-lookup"><span data-stu-id="b2df9-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="b2df9-330">选择“ASP.NET Core Web API”C# 项目模板，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="b2df9-331">从“目标框架”下拉列表中选择“.NET Core 2.2”，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="b2df9-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="b2df9-332">输入“BooksApi”作为“项目名称”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-332">Enter *BooksApi* for the **Project Name** , and select **Create**.</span></span>
1. <span data-ttu-id="b2df9-333">在“解决方案”面板中，右键单击项目的“依赖项”节点，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="b2df9-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="b2df9-334">在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包” 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="b2df9-335">在“许可证接受”对话框中，选择“接受”按钮 。</span><span class="sxs-lookup"><span data-stu-id="b2df9-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="b2df9-336">添加实体模型</span><span class="sxs-lookup"><span data-stu-id="b2df9-336">Add an entity model</span></span>

1. <span data-ttu-id="b2df9-337">将 Models 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="b2df9-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="b2df9-338">使用以下代码将 `Book` 类添加到 Models 目录：</span><span class="sxs-lookup"><span data-stu-id="b2df9-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="b2df9-339">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="b2df9-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="b2df9-340">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="b2df9-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="b2df9-341">使用 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="b2df9-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="b2df9-342">使用 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而非 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构传递。</span><span class="sxs-lookup"><span data-stu-id="b2df9-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="b2df9-343">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="b2df9-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="b2df9-344">`BookName` 属性使用 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。</span><span class="sxs-lookup"><span data-stu-id="b2df9-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="b2df9-345">`Name` 的属性值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="b2df9-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="b2df9-346">添加配置模型</span><span class="sxs-lookup"><span data-stu-id="b2df9-346">Add a configuration model</span></span>

1. <span data-ttu-id="b2df9-347">向 appsettings.json 添加以下数据库配置值：</span><span class="sxs-lookup"><span data-stu-id="b2df9-347">Add the following database configuration values to *appsettings.json* :</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="b2df9-348">使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录 ：</span><span class="sxs-lookup"><span data-stu-id="b2df9-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="b2df9-349">前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值。</span><span class="sxs-lookup"><span data-stu-id="b2df9-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="b2df9-350">JSON 和 C# 具有相同的属性名称，目的是简化映射过程。</span><span class="sxs-lookup"><span data-stu-id="b2df9-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="b2df9-351">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="b2df9-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="b2df9-352">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="b2df9-352">In the preceding code:</span></span>

   * <span data-ttu-id="b2df9-353">appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册。</span><span class="sxs-lookup"><span data-stu-id="b2df9-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="b2df9-354">例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充。</span><span class="sxs-lookup"><span data-stu-id="b2df9-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="b2df9-355">`IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。</span><span class="sxs-lookup"><span data-stu-id="b2df9-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b2df9-356">在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。</span><span class="sxs-lookup"><span data-stu-id="b2df9-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="b2df9-357">在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用：</span><span class="sxs-lookup"><span data-stu-id="b2df9-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="b2df9-358">添加 CRUD 操作服务</span><span class="sxs-lookup"><span data-stu-id="b2df9-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="b2df9-359">将 Services 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="b2df9-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="b2df9-360">使用以下代码将 `BookService` 类添加到 Services 目录：</span><span class="sxs-lookup"><span data-stu-id="b2df9-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="b2df9-361">上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。</span><span class="sxs-lookup"><span data-stu-id="b2df9-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="b2df9-362">使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分中添加的 appsettings.json 配置值。</span><span class="sxs-lookup"><span data-stu-id="b2df9-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="b2df9-363">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="b2df9-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="b2df9-364">上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="b2df9-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="b2df9-365">单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="b2df9-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="b2df9-366">根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="b2df9-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="b2df9-367">在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用：</span><span class="sxs-lookup"><span data-stu-id="b2df9-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="b2df9-368">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="b2df9-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="b2df9-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：读取用于执行数据库操作的服务器实例。</span><span class="sxs-lookup"><span data-stu-id="b2df9-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="b2df9-370">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="b2df9-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="b2df9-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：表示用于执行操作的 Mongo 数据库。</span><span class="sxs-lookup"><span data-stu-id="b2df9-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="b2df9-372">本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。</span><span class="sxs-lookup"><span data-stu-id="b2df9-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="b2df9-373">调用此方法后，对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b2df9-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="b2df9-374">在 `GetCollection<TDocument>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="b2df9-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="b2df9-375">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="b2df9-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="b2df9-376">`TDocument` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="b2df9-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="b2df9-377">`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。</span><span class="sxs-lookup"><span data-stu-id="b2df9-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="b2df9-378">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="b2df9-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="b2df9-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="b2df9-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="b2df9-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="b2df9-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="b2df9-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="b2df9-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="b2df9-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="b2df9-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="b2df9-383">添加控制器</span><span class="sxs-lookup"><span data-stu-id="b2df9-383">Add a controller</span></span>

<span data-ttu-id="b2df9-384">使用以下代码将 `BooksController` 类添加到 Controllers 目录：</span><span class="sxs-lookup"><span data-stu-id="b2df9-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="b2df9-385">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="b2df9-385">The preceding web API controller:</span></span>

* <span data-ttu-id="b2df9-386">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="b2df9-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="b2df9-387">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="b2df9-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="b2df9-388">在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。</span><span class="sxs-lookup"><span data-stu-id="b2df9-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="b2df9-389">状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="b2df9-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="b2df9-390">`CreatedAtRoute` 还会将 `Location` 标头添加到响应中。</span><span class="sxs-lookup"><span data-stu-id="b2df9-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="b2df9-391">`Location` 标头指定新建书籍的 URI。</span><span class="sxs-lookup"><span data-stu-id="b2df9-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="b2df9-392">测试 Web API</span><span class="sxs-lookup"><span data-stu-id="b2df9-392">Test the web API</span></span>

1. <span data-ttu-id="b2df9-393">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="b2df9-393">Build and run the app.</span></span>

1. <span data-ttu-id="b2df9-394">导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="b2df9-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="b2df9-395">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="b2df9-395">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="b2df9-396">导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="b2df9-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="b2df9-397">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="b2df9-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="b2df9-398">配置 JSON 序列化选项</span><span class="sxs-lookup"><span data-stu-id="b2df9-398">Configure JSON serialization options</span></span>

<span data-ttu-id="b2df9-399">关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：</span><span class="sxs-lookup"><span data-stu-id="b2df9-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="b2df9-400">应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。</span><span class="sxs-lookup"><span data-stu-id="b2df9-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="b2df9-401">`bookName` 属性应作为 `Name` 返回。</span><span class="sxs-lookup"><span data-stu-id="b2df9-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="b2df9-402">为满足上述要求，请进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="b2df9-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="b2df9-403">在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddMvc` 方法调用：</span><span class="sxs-lookup"><span data-stu-id="b2df9-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="b2df9-404">通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="b2df9-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="b2df9-405">例如，`Book` 类的 `Author` 属性序列化为 `Author`。</span><span class="sxs-lookup"><span data-stu-id="b2df9-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="b2df9-406">在 Models/Book.cs 中，使用以下 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性：</span><span class="sxs-lookup"><span data-stu-id="b2df9-406">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="b2df9-407">`[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="b2df9-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="b2df9-408">在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用：</span><span class="sxs-lookup"><span data-stu-id="b2df9-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="b2df9-409">重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。</span><span class="sxs-lookup"><span data-stu-id="b2df9-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="b2df9-410">注意 JSON 属性名称中的区别。</span><span class="sxs-lookup"><span data-stu-id="b2df9-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="b2df9-411">向 Web API 添加身份验证支持</span><span class="sxs-lookup"><span data-stu-id="b2df9-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="b2df9-412">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b2df9-412">Next steps</span></span>

<span data-ttu-id="b2df9-413">有关构建 ASP.NET Core Web API 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="b2df9-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="b2df9-414">本文的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="b2df9-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
