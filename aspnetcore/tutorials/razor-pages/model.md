---
title: 第 2 部分，添加模型
author: rick-anderson
description: Razor 页面教程系列的第 2 部分。 在此部分，将添加模型类。
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: 92bfda330399b43871b3ae0e6b609726f7ad4a91
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564046"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="5335d-104">第 2 部分，在 ASP.NET Core 中向 Razor 页面应用添加模型</span><span class="sxs-lookup"><span data-stu-id="5335d-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="5335d-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5335d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="5335d-106">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="5335d-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="5335d-107">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="5335d-108">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="5335d-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="5335d-109">首先要编写模型类，然后 EF Core 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="5335d-110">模型类称为 POCO 类（源自“简单传统 CLR 对象”   ），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5335d-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="5335d-111">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="5335d-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="5335d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="5335d-113">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="5335d-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5335d-115">在“解决方案资源管理器”中，右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5335d-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5335d-116">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5335d-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="5335d-117">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="5335d-118">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="5335d-119">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="5335d-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="5335d-120">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-121">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-122">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-123">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-124">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-124">With this attribute:</span></span>

  * <span data-ttu-id="5335d-125">用户无需在日期字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-126">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5335d-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5335d-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5335d-128">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="5335d-129">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="5335d-130">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-131">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-132">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-133">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-134">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-134">With this attribute:</span></span>

  * <span data-ttu-id="5335d-135">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-136">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="5335d-137">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="5335d-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="5335d-138">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="5335d-138">Add a database context class</span></span>

1. <span data-ttu-id="5335d-139">在 RazorPagesMovie 项目中，创建名为“Data”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="5335d-140">在“Data”文件夹中，使用以下代码添加一个名为“RazorPagesMovieContext.cs”的文件：</span><span class="sxs-lookup"><span data-stu-id="5335d-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="5335d-141">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="5335d-142">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="5335d-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="5335d-143">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="5335d-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="5335d-144">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="5335d-144">Add a database connection string</span></span>

<span data-ttu-id="5335d-145">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="5335d-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="5335d-146">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="5335d-146">Register the database context</span></span>

1. <span data-ttu-id="5335d-147">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="5335d-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="5335d-148">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="5335d-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5335d-149">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5335d-150">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5335d-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="5335d-151">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="5335d-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="5335d-152">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="5335d-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="5335d-153">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="5335d-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="5335d-154">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="5335d-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="5335d-155">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="5335d-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="5335d-156">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-157">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-158">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-159">`[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-160">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-160">With this attribute:</span></span>

  * <span data-ttu-id="5335d-161">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-162">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="5335d-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="5335d-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="5335d-164">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="5335d-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5335d-165">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="5335d-165">Scaffold the movie model</span></span>

<span data-ttu-id="5335d-166">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="5335d-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5335d-167">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="5335d-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5335d-169">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5335d-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="5335d-170">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5335d-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="5335d-171">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="5335d-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="5335d-172">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5335d-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![上述说明的图像。](model/_static/5/sca.png)

1. <span data-ttu-id="5335d-174">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5335d-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffold.png)

1. <span data-ttu-id="5335d-176">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5335d-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="5335d-177">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="5335d-178">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="5335d-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="5335d-179">在“添加数据上下文”对话框中，生成类名 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="5335d-179">In the **Add Data Context** dialog, the class name `RazorPagesMovie.Data.RazorPagesMovieContext` is generated.</span></span>
   1. <span data-ttu-id="5335d-180">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5335d-180">Select **Add**.</span></span>

   ![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="5335d-182">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5335d-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5335d-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5335d-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5335d-184">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令行界面。</span><span class="sxs-lookup"><span data-stu-id="5335d-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="5335d-185">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5335d-186">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="5335d-187">下表详细说明了 ASP.NET Core 代码生成器选项。</span><span class="sxs-lookup"><span data-stu-id="5335d-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="5335d-188">选项</span><span class="sxs-lookup"><span data-stu-id="5335d-188">Option</span></span>               | <span data-ttu-id="5335d-189">说明</span><span class="sxs-lookup"><span data-stu-id="5335d-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="5335d-190">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="5335d-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="5335d-191">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="5335d-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="5335d-192">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="5335d-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="5335d-193">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="5335d-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="5335d-194">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="5335d-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="5335d-195">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="5335d-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="5335d-196">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="5335d-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="5335d-197">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="5335d-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="5335d-198">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="5335d-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="5335d-199">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 `Startup` 中。</span><span class="sxs-lookup"><span data-stu-id="5335d-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="5335d-200">注入 `IWebHostEnvironment`，以便应用可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="5335d-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5335d-201">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5335d-202">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5335d-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="5335d-203">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5335d-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="5335d-204">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="5335d-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="5335d-205">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="5335d-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![上述说明的图像。](model/_static/scaMac.png)

1. <span data-ttu-id="5335d-207">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="5335d-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![上述说明的图像。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="5335d-209">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5335d-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="5335d-210">在“要使用的 DbContext 类:”行中，将类命名为 `RazorPagesMovie.Data.RazorPagesMovieContext`。</span><span class="sxs-lookup"><span data-stu-id="5335d-210">In the **DbContext Class to use:** row, name the class `RazorPagesMovie.Data.RazorPagesMovieContext`.</span></span>
   1. <span data-ttu-id="5335d-211">选择“完成”  。</span><span class="sxs-lookup"><span data-stu-id="5335d-211">Select **Finish**.</span></span>

   ![上述说明的图像。](model/_static/5/arpMac.png)

<span data-ttu-id="5335d-213">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5335d-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="5335d-214">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="5335d-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="5335d-215">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="5335d-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="5335d-216">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 `Startup` 中。</span><span class="sxs-lookup"><span data-stu-id="5335d-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="5335d-217">注入 `IWebHostEnvironment`，以便应用可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="5335d-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="5335d-218">创建的文件</span><span class="sxs-lookup"><span data-stu-id="5335d-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-220">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5335d-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5335d-221">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="5335d-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5335d-222">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5335d-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5335d-223">已更新</span><span class="sxs-lookup"><span data-stu-id="5335d-223">Updated</span></span>

* <span data-ttu-id="5335d-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5335d-224">*Startup.cs*</span></span>

<span data-ttu-id="5335d-225">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5335d-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5335d-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5335d-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5335d-227">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="5335d-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="5335d-228">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="5335d-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="5335d-229">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5335d-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5335d-230">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5335d-231">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5335d-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5335d-232">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="5335d-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5335d-233">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5335d-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5335d-234">已更新</span><span class="sxs-lookup"><span data-stu-id="5335d-234">Updated</span></span>

* <span data-ttu-id="5335d-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5335d-235">*Startup.cs*</span></span>

<span data-ttu-id="5335d-236">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5335d-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="5335d-237">使用 EF 的迁移功能创建初始数据库架构</span><span class="sxs-lookup"><span data-stu-id="5335d-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="5335d-238">Entity Framework Core 中的迁移功能提供了一种方法来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5335d-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="5335d-239">创建初始数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5335d-239">Create the initial database schema.</span></span>
* <span data-ttu-id="5335d-240">以增量的方式更新数据库架构，使其与应用程序的数据模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="5335d-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="5335d-241">保存数据库中的现有数据。</span><span class="sxs-lookup"><span data-stu-id="5335d-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-243">在此部分中，程序包管理器控制台 (PMC) 窗口用于：</span><span class="sxs-lookup"><span data-stu-id="5335d-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="5335d-244">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="5335d-244">Add an initial migration.</span></span>
* <span data-ttu-id="5335d-245">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="5335d-246">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC 菜单](model/_static/5/pmc.png)

1. <span data-ttu-id="5335d-248">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5335d-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="5335d-250">运行以下 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="5335d-251">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="5335d-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="5335d-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="5335d-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="5335d-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="5335d-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="5335d-254">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="5335d-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="5335d-255">`migrations` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="5335d-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5335d-256">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="5335d-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="5335d-257">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="5335d-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="5335d-258">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="5335d-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="5335d-259">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="5335d-260">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5335d-262">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="5335d-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5335d-263">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="5335d-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5335d-264">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="5335d-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5335d-265">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="5335d-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5335d-266">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="5335d-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5335d-267">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="5335d-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5335d-268">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5335d-269">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="5335d-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="5335d-270">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="5335d-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="5335d-271">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="5335d-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="5335d-272">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="5335d-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5335d-273">前面的代码为实体集创建 [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) 属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="5335d-274">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="5335d-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5335d-275">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="5335d-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5335d-276">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="5335d-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="5335d-277">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5335d-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5335d-278">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5335d-279">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="5335d-280">测试应用</span><span class="sxs-lookup"><span data-stu-id="5335d-280">Test the app</span></span>

1. <span data-ttu-id="5335d-281">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="5335d-282">如果收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="5335d-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="5335d-283">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="5335d-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="5335d-284">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="5335d-284">Test the **Create** link.</span></span>

   ![创建页面](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="5335d-286">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="5335d-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5335d-287">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="5335d-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5335d-288">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="5335d-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="5335d-289">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="5335d-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5335d-290">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="5335d-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5335d-291">其他资源</span><span class="sxs-lookup"><span data-stu-id="5335d-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5335d-292">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5335d-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="5335d-293">在该部分，会添加类管理影片。</span><span class="sxs-lookup"><span data-stu-id="5335d-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="5335d-294">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="5335d-295">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="5335d-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="5335d-296">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5335d-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5335d-297">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="5335d-298">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="5335d-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="5335d-299">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="5335d-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-301">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5335d-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5335d-302">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5335d-302">Name the folder *Models*.</span></span>

<span data-ttu-id="5335d-303">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="5335d-304">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="5335d-305">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="5335d-305">Name the class **Movie**.</span></span>

<span data-ttu-id="5335d-306">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-307">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-308">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-309">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-310">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-310">With this attribute:</span></span>

  * <span data-ttu-id="5335d-311">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-312">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="5335d-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="5335d-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5335d-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5335d-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5335d-315">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5335d-316">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="5335d-317">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-318">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-319">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-320">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-321">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-321">With this attribute:</span></span>

  * <span data-ttu-id="5335d-322">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-323">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="5335d-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="5335d-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="5335d-325">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="5335d-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="5335d-326">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="5335d-326">Add a database context class</span></span>

* <span data-ttu-id="5335d-327">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="5335d-328">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5335d-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5335d-329">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="5335d-330">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="5335d-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="5335d-331">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="5335d-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="5335d-332">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="5335d-332">Add a database connection string</span></span>

<span data-ttu-id="5335d-333">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="5335d-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="5335d-334">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="5335d-334">Register the database context</span></span>

<span data-ttu-id="5335d-335">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="5335d-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="5335d-336">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="5335d-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5335d-337">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5335d-338">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5335d-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="5335d-339">右键单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="5335d-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="5335d-340">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="5335d-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5335d-341">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="5335d-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5335d-342">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="5335d-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5335d-343">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="5335d-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="5335d-344">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-345">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-346">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-347">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-348">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-348">With this attribute:</span></span>

  * <span data-ttu-id="5335d-349">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-350">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="5335d-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="5335d-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="5335d-352">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="5335d-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5335d-353">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="5335d-353">Scaffold the movie model</span></span>

<span data-ttu-id="5335d-354">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="5335d-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5335d-355">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="5335d-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-357">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5335d-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5335d-358">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5335d-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5335d-359">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="5335d-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="5335d-360">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5335d-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="5335d-362">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5335d-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="5335d-364">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5335d-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5335d-365">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5335d-366">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models.RazorPagesMovieContext 更改为 RazorPagesMovie.Data.RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="5335d-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="5335d-367">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="5335d-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5335d-368">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="5335d-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5335d-369">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5335d-369">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="5335d-371">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5335d-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5335d-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5335d-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5335d-373">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="5335d-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="5335d-374">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5335d-375">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="5335d-376">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="5335d-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="5335d-377">选项</span><span class="sxs-lookup"><span data-stu-id="5335d-377">Option</span></span>               | <span data-ttu-id="5335d-378">说明</span><span class="sxs-lookup"><span data-stu-id="5335d-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="5335d-379">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="5335d-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="5335d-380">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="5335d-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="5335d-381">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="5335d-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="5335d-382">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="5335d-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="5335d-383">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="5335d-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="5335d-384">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="5335d-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="5335d-385">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="5335d-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="5335d-386">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="5335d-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="5335d-387">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="5335d-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="5335d-388">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="5335d-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="5335d-389">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="5335d-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5335d-390">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5335d-391">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5335d-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5335d-392">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5335d-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5335d-393">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="5335d-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="5335d-394">右键单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="5335d-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="5335d-396">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="5335d-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="5335d-398">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5335d-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5335d-399">在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5335d-400">在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="5335d-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="5335d-401">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="5335d-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5335d-402">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="5335d-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5335d-403">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5335d-403">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="5335d-405">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5335d-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="5335d-406">添加 EF 工具</span><span class="sxs-lookup"><span data-stu-id="5335d-406">Add EF tools</span></span>

<span data-ttu-id="5335d-407">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="5335d-408">前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="5335d-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="5335d-409">有关详细信息，请参阅 [Entity Framework Core 工具参考 - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="5335d-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="5335d-410">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="5335d-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="5335d-411">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="5335d-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="5335d-412">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="5335d-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="5335d-413">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="5335d-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="5335d-414">创建的文件</span><span class="sxs-lookup"><span data-stu-id="5335d-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-416">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5335d-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5335d-417">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="5335d-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5335d-418">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5335d-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5335d-419">已更新</span><span class="sxs-lookup"><span data-stu-id="5335d-419">Updated</span></span>

* <span data-ttu-id="5335d-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5335d-420">*Startup.cs*</span></span>

<span data-ttu-id="5335d-421">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5335d-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5335d-422">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5335d-423">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5335d-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5335d-424">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="5335d-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5335d-425">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5335d-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5335d-426">已更新</span><span class="sxs-lookup"><span data-stu-id="5335d-426">Updated</span></span>

* <span data-ttu-id="5335d-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5335d-427">*Startup.cs*</span></span>

<span data-ttu-id="5335d-428">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5335d-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5335d-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5335d-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5335d-430">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="5335d-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="5335d-431">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="5335d-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="5335d-432">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5335d-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5335d-433">初始迁移</span><span class="sxs-lookup"><span data-stu-id="5335d-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-435">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="5335d-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5335d-436">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="5335d-436">Add an initial migration.</span></span>
* <span data-ttu-id="5335d-437">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="5335d-438">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5335d-440">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5335d-441">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="5335d-442">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="5335d-443">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="5335d-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="5335d-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="5335d-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="5335d-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="5335d-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="5335d-446">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="5335d-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="5335d-447">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="5335d-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="5335d-448">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="5335d-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="5335d-449">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="5335d-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="5335d-450">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="5335d-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="5335d-451">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="5335d-452">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5335d-454">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="5335d-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5335d-455">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="5335d-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5335d-456">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="5335d-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5335d-457">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="5335d-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="5335d-458">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="5335d-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5335d-459">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="5335d-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5335d-460">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5335d-461">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="5335d-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="5335d-462">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="5335d-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="5335d-463">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="5335d-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="5335d-464">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="5335d-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5335d-465">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5335d-466">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="5335d-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5335d-467">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="5335d-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5335d-468">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="5335d-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="5335d-469">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5335d-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5335d-470">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5335d-471">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5335d-472">测试应用</span><span class="sxs-lookup"><span data-stu-id="5335d-472">Test the app</span></span>

* <span data-ttu-id="5335d-473">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5335d-474">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="5335d-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5335d-475">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="5335d-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5335d-476">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="5335d-476">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="5335d-478">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="5335d-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5335d-479">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="5335d-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5335d-480">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="5335d-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5335d-481">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="5335d-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5335d-482">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="5335d-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5335d-483">其他资源</span><span class="sxs-lookup"><span data-stu-id="5335d-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5335d-484">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5335d-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5335d-485">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="5335d-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="5335d-486">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="5335d-487">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="5335d-488">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="5335d-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="5335d-489">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5335d-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5335d-490">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="5335d-491">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="5335d-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="5335d-492">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="5335d-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-494">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5335d-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5335d-495">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5335d-495">Name the folder *Models*.</span></span>

<span data-ttu-id="5335d-496">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="5335d-497">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="5335d-498">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="5335d-498">Name the class **Movie**.</span></span>

<span data-ttu-id="5335d-499">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-500">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-501">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-502">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-503">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-503">With this attribute:</span></span>

  * <span data-ttu-id="5335d-504">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-505">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="5335d-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="5335d-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5335d-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5335d-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5335d-508">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5335d-509">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="5335d-510">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-511">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-512">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-513">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-514">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-514">With this attribute:</span></span>

  * <span data-ttu-id="5335d-515">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-516">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="5335d-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="5335d-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="5335d-518">添加 NuGet 包和 EF 工具</span><span class="sxs-lookup"><span data-stu-id="5335d-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="5335d-519">添加数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="5335d-519">Add a database context class</span></span>

<span data-ttu-id="5335d-520">在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="5335d-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="5335d-521">将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5335d-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5335d-522">前面的代码为实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="5335d-523">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="5335d-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="5335d-524">直至在后续步骤中添加依赖项，代码才可编译。</span><span class="sxs-lookup"><span data-stu-id="5335d-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="5335d-525">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="5335d-525">Add a database connection string</span></span>

<span data-ttu-id="5335d-526">向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="5335d-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="5335d-527">添加所需的 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="5335d-527">Add required NuGet packages</span></span>

<span data-ttu-id="5335d-528">运行以下 .NET Core CLI 命令，将 SQLite 和 CodeGeneration.Design 添加到项目中：</span><span class="sxs-lookup"><span data-stu-id="5335d-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="5335d-529">`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。</span><span class="sxs-lookup"><span data-stu-id="5335d-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="5335d-530">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="5335d-530">Register the database context</span></span>

<span data-ttu-id="5335d-531">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="5335d-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="5335d-532">使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="5335d-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="5335d-533">生成项目以检查错误。</span><span class="sxs-lookup"><span data-stu-id="5335d-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5335d-534">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5335d-535">在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5335d-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="5335d-536">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5335d-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="5335d-537">按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="5335d-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="5335d-538">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="5335d-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5335d-539">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="5335d-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5335d-540">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="5335d-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5335d-541">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="5335d-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="5335d-542">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="5335d-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="5335d-543">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="5335d-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="5335d-544">数据库需要 `ID` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="5335d-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="5335d-545">`[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="5335d-546">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="5335d-546">With this attribute:</span></span>

  * <span data-ttu-id="5335d-547">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="5335d-548">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="5335d-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="5335d-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="5335d-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="5335d-550">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="5335d-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5335d-551">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="5335d-551">Scaffold the movie model</span></span>

<span data-ttu-id="5335d-552">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="5335d-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5335d-553">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="5335d-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-555">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5335d-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5335d-556">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5335d-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5335d-557">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="5335d-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="5335d-558">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5335d-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="5335d-560">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5335d-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="5335d-562">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5335d-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="5335d-563">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5335d-564">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”  。</span><span class="sxs-lookup"><span data-stu-id="5335d-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="5335d-565">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5335d-565">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="5335d-567">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5335d-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5335d-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5335d-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5335d-569">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="5335d-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="5335d-570">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5335d-571">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="5335d-572">下表详细说明了 ASP.NET Core 代码生成器选项：</span><span class="sxs-lookup"><span data-stu-id="5335d-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="5335d-573">选项</span><span class="sxs-lookup"><span data-stu-id="5335d-573">Option</span></span>               | <span data-ttu-id="5335d-574">说明</span><span class="sxs-lookup"><span data-stu-id="5335d-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="5335d-575">模型的名称。</span><span class="sxs-lookup"><span data-stu-id="5335d-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="5335d-576">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="5335d-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="5335d-577">使用默认布局。</span><span class="sxs-lookup"><span data-stu-id="5335d-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="5335d-578">用于创建视图的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="5335d-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="5335d-579">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`</span><span class="sxs-lookup"><span data-stu-id="5335d-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="5335d-580">使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="5335d-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="5335d-581">有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="5335d-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5335d-582">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5335d-583">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5335d-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5335d-584">按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5335d-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5335d-585">将文件夹命名为“Movies”。</span><span class="sxs-lookup"><span data-stu-id="5335d-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="5335d-586">按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5335d-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="5335d-588">在“添加新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5335d-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="5335d-590">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5335d-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5335d-591">在“模型类”下拉列表中，选择或键入“Movie” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="5335d-592">在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="5335d-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="5335d-593">在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="5335d-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="5335d-594">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5335d-594">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="5335d-596">*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5335d-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="5335d-597">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5335d-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="5335d-598">创建的文件</span><span class="sxs-lookup"><span data-stu-id="5335d-598">Files created</span></span>

* <span data-ttu-id="5335d-599">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。</span><span class="sxs-lookup"><span data-stu-id="5335d-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5335d-600">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5335d-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="5335d-601">文件已更新</span><span class="sxs-lookup"><span data-stu-id="5335d-601">File updated</span></span>

* <span data-ttu-id="5335d-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5335d-602">*Startup.cs*</span></span>

<span data-ttu-id="5335d-603">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5335d-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5335d-604">初始迁移</span><span class="sxs-lookup"><span data-stu-id="5335d-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5335d-606">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="5335d-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5335d-607">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="5335d-607">Add an initial migration.</span></span>
* <span data-ttu-id="5335d-608">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="5335d-609">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="5335d-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5335d-611">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="5335d-612">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="5335d-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5335d-613">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="5335d-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="5335d-614">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="5335d-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="5335d-615">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="5335d-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="5335d-616">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="5335d-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="5335d-617">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="5335d-618">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="5335d-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5335d-619">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="5335d-620">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="5335d-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="5335d-621">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="5335d-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="5335d-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="5335d-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="5335d-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="5335d-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="5335d-624">忽略警告，因为它将在后面的步骤中得到解决。</span><span class="sxs-lookup"><span data-stu-id="5335d-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5335d-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5335d-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5335d-626">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="5335d-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5335d-627">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="5335d-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5335d-628">在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="5335d-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5335d-629">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="5335d-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5335d-630">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="5335d-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5335d-631">基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="5335d-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5335d-632">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5335d-633">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="5335d-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="5335d-634">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="5335d-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="5335d-635">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。</span><span class="sxs-lookup"><span data-stu-id="5335d-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="5335d-636">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="5335d-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5335d-637">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="5335d-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5335d-638">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="5335d-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5335d-639">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="5335d-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5335d-640">通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="5335d-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="5335d-641">进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5335d-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5335d-642">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5335d-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5335d-643">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5335d-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5335d-644">测试应用</span><span class="sxs-lookup"><span data-stu-id="5335d-644">Test the app</span></span>

* <span data-ttu-id="5335d-645">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="5335d-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5335d-646">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="5335d-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5335d-647">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="5335d-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5335d-648">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="5335d-648">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5335d-650">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="5335d-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5335d-651">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="5335d-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5335d-652">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="5335d-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5335d-653">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="5335d-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5335d-654">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="5335d-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5335d-655">其他资源</span><span class="sxs-lookup"><span data-stu-id="5335d-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5335d-656">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5335d-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
