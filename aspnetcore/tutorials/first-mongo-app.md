---
title: 使用 ASP.NET Core 和 MongoDB 创建 Web API
author: prkhandelwal
description: 本教程演示如何使用 MongoDB NoSQL 数据库创建 ASP.NET Core Web API。
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 06/04/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 6a8c5d75f562b38015101e039a2f5d96a5491595
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692559"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="d1f1b-103">使用 ASP.NET Core 和 MongoDB 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="d1f1b-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="d1f1b-104">作者 [Pratik Khandelwal](https://twitter.com/K2Prk) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d1f1b-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d1f1b-105">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="d1f1b-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d1f1b-107">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="d1f1b-107">Configure MongoDB</span></span>
> * <span data-ttu-id="d1f1b-108">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="d1f1b-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="d1f1b-109">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="d1f1b-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="d1f1b-110">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="d1f1b-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="d1f1b-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d1f1b-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d1f1b-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="d1f1b-112">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1f1b-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1f1b-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="d1f1b-114">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="d1f1b-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="d1f1b-115">包含“ASP.NET 和 Web 开发”  工作负荷的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="d1f1b-115">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="d1f1b-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d1f1b-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1f1b-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1f1b-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="d1f1b-118">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="d1f1b-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="d1f1b-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1f1b-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="d1f1b-120">用于 Visual Studio Code 的 C#</span><span class="sxs-lookup"><span data-stu-id="d1f1b-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="d1f1b-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d1f1b-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1f1b-122">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1f1b-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d1f1b-123">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="d1f1b-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="d1f1b-124">Visual Studio for Mac 版本 7.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="d1f1b-124">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="d1f1b-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d1f1b-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="d1f1b-126">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="d1f1b-126">Configure MongoDB</span></span>

<span data-ttu-id="d1f1b-127">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB  中。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-127">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="d1f1b-128">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin  添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-128">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="d1f1b-129">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="d1f1b-130">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="d1f1b-131">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="d1f1b-132">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="d1f1b-133">例如，Windows 上的 C:\\BooksData  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-133">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="d1f1b-134">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="d1f1b-135">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="d1f1b-136">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-136">Open a command shell.</span></span> <span data-ttu-id="d1f1b-137">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="d1f1b-138">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="d1f1b-139">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-139">Open another command shell instance.</span></span> <span data-ttu-id="d1f1b-140">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="d1f1b-141">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="d1f1b-142">如果该命令尚不存在，则将创建名为 BookstoreDb  的数据库。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="d1f1b-143">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="d1f1b-144">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="d1f1b-145">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="d1f1b-146">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="d1f1b-147">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="d1f1b-148">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="d1f1b-149">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-149">The following result is displayed:</span></span>

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

    <span data-ttu-id="d1f1b-150">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="d1f1b-151">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-151">The database is ready.</span></span> <span data-ttu-id="d1f1b-152">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="d1f1b-153">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="d1f1b-153">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1f1b-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1f1b-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d1f1b-155">转到“文件”   > “新建”   > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="d1f1b-156">选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-156">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="d1f1b-157">将项目命名为“BooksApi”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-157">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="d1f1b-158">选择“.NET Core”  目标框架和“ASP.NET Core 2.2”  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-158">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="d1f1b-159">选择“API”项目模板，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-159">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="d1f1b-160">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-160">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d1f1b-161">在“包管理器控制台”  窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-161">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="d1f1b-162">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-162">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1f1b-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1f1b-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d1f1b-164">在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-164">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="d1f1b-165">将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-165">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="d1f1b-166">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。  是否添加它们?”。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-166">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="d1f1b-167">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-167">Select **Yes**.</span></span>
1. <span data-ttu-id="d1f1b-168">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-168">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d1f1b-169">打开“集成终端”  并导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-169">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="d1f1b-170">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-170">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1f1b-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1f1b-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d1f1b-172">转到“文件”   > “新建解决方案”   > “.NET Core”   > “应用”  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-172">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="d1f1b-173">选择“ASP.NET Core Web API”C# 项目模板，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-173">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="d1f1b-174">从“目标框架”下拉列表中选择“.NET Core 2.2”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-174">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="d1f1b-175">输入“BooksApi”作为“项目名称”，然后选择“创建”    。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-175">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="d1f1b-176">在“解决方案”  面板中，右键单击项目的“依赖项”  节点，然后选择“添加包”  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-176">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="d1f1b-177">在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包”    。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-177">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="d1f1b-178">在“许可证接受”对话框中，选择“接受”按钮   。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-178">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="d1f1b-179">添加实体模型</span><span class="sxs-lookup"><span data-stu-id="d1f1b-179">Add an entity model</span></span>

1. <span data-ttu-id="d1f1b-180">将 Models  目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-180">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="d1f1b-181">使用以下代码将 `Book` 类添加到 Models  目录：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-181">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

    <span data-ttu-id="d1f1b-182">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-182">In the preceding class, the `Id` property:</span></span>
    
    * <span data-ttu-id="d1f1b-183">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-183">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="d1f1b-184">使用 [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-184">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="d1f1b-185">使用 [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而不是 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构进行传递。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-185">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="d1f1b-186">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-186">Mongo handles the conversion from `string` to `ObjectId`.</span></span>
    
    <span data-ttu-id="d1f1b-187">类中的其他属性使用 [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-187">Other properties in the class are annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="d1f1b-188">该属性的值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-188">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="d1f1b-189">添加配置模型</span><span class="sxs-lookup"><span data-stu-id="d1f1b-189">Add a configuration model</span></span>

1. <span data-ttu-id="d1f1b-190">向 appsettings.json 添加以下数据库配置值  ：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-190">Add the following database configuration values to *appsettings.json*:</span></span>

    [!code-json[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="d1f1b-191">使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录   ：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-191">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/BookstoreDatabaseSettings.cs)]

    <span data-ttu-id="d1f1b-192">前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-192">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="d1f1b-193">JSON 和 C# 具有相同的属性名称，目的是简化映射过程。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-193">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="d1f1b-194">在调用 `AddMvc` 前，将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-194">Add the following code to `Startup.ConfigureServices`, before the call to `AddMvc`:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureDatabaseSettings)]

    <span data-ttu-id="d1f1b-195">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-195">In the preceding code:</span></span>

    * <span data-ttu-id="d1f1b-196">appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-196">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="d1f1b-197">例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-197">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="d1f1b-198">`IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-198">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d1f1b-199">在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-199">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="d1f1b-200">在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用  ：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-200">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="d1f1b-201">添加 CRUD 操作服务</span><span class="sxs-lookup"><span data-stu-id="d1f1b-201">Add a CRUD operations service</span></span>

1. <span data-ttu-id="d1f1b-202">将 Services  目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-202">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="d1f1b-203">使用以下代码将 `BookService` 类添加到 Services  目录：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-203">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

    <span data-ttu-id="d1f1b-204">上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-204">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="d1f1b-205">使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分添加的 appsettings.json 配置值  。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-205">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="d1f1b-206">在 `Startup.ConfigureServices` 中，向 DI 注册 `BookService` 类：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-206">In `Startup.ConfigureServices`, register the `BookService` class with DI:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=9)]

    <span data-ttu-id="d1f1b-207">上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-207">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="d1f1b-208">单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-208">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="d1f1b-209">根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-209">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="d1f1b-210">在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用  ：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-210">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="d1f1b-211">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-211">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="d1f1b-212">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; 读取服务器实例，以执行数据库操作。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-212">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="d1f1b-213">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-213">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="d1f1b-214">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 表示 Mongo 数据库，以执行操作。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-214">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="d1f1b-215">本教程在界面上使用泛型 [GetCollection<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-215">This tutorial uses the generic [GetCollection<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="d1f1b-216">调用此方法后，对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-216">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="d1f1b-217">在 `GetCollection<TDocument>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-217">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="d1f1b-218">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-218">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="d1f1b-219">`TDocument` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-219">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="d1f1b-220">`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-220">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="d1f1b-221">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-221">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="d1f1b-222">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-222">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="d1f1b-223">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-223">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="d1f1b-224">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-224">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="d1f1b-225">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-225">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d1f1b-226">添加控制器</span><span class="sxs-lookup"><span data-stu-id="d1f1b-226">Add a controller</span></span>

<span data-ttu-id="d1f1b-227">使用以下代码将 `BooksController` 类添加到 Controllers  目录：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-227">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

<span data-ttu-id="d1f1b-228">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-228">The preceding web API controller:</span></span>

* <span data-ttu-id="d1f1b-229">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-229">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="d1f1b-230">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-230">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="d1f1b-231">在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-231">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="d1f1b-232">状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-232">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="d1f1b-233">`CreatedAtRoute` 还会将 `Location` 标头添加到响应中。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-233">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="d1f1b-234">`Location` 标头指定新建书籍的 URI。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-234">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="d1f1b-235">测试 Web API</span><span class="sxs-lookup"><span data-stu-id="d1f1b-235">Test the web API</span></span>

1. <span data-ttu-id="d1f1b-236">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-236">Build and run the app.</span></span>

1. <span data-ttu-id="d1f1b-237">导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-237">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="d1f1b-238">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-238">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="d1f1b-239">导航到 `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e`，测试控制器的重载 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="d1f1b-239">Navigate to `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="d1f1b-240">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-240">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"5bfd996f7b8e48dc15ff215e",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="next-steps"></a><span data-ttu-id="d1f1b-241">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d1f1b-241">Next steps</span></span>

<span data-ttu-id="d1f1b-242">有关构建 ASP.NET Core Web API 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="d1f1b-242">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="d1f1b-243">本文的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="d1f1b-243">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
