---
title: 在 ASP.NET Core 中向 Razor Pages 应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f6dbac81b4efceb30c379ab06dd715005d879228
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647172"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="7220f-103">在 ASP.NET Core 中向 Razor Pages 应用添加模型</span><span class="sxs-lookup"><span data-stu-id="7220f-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="7220f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7220f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="7220f-105">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="7220f-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="7220f-106">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="7220f-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="7220f-107">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="7220f-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="7220f-108">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="7220f-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="7220f-109">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="7220f-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7220f-110">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="7220f-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="7220f-111">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="7220f-111">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7220f-113">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7220f-114">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-114">Name the folder *Models*.</span></span>

<span data-ttu-id="7220f-115">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="7220f-115">Right click the *Models* folder.</span></span> <span data-ttu-id="7220f-116">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="7220f-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="7220f-117">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7220f-119">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="7220f-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="7220f-120">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="7220f-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7220f-122">在 Solution Pad 中，右键单击“RazorPagesMovie”  项目，然后选择“添加”  >“新建文件夹...”  。将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="7220f-123">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件...”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="7220f-124">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="7220f-125">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="7220f-126">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="7220f-127">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="7220f-128">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="7220f-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7220f-129">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="7220f-129">Scaffold the movie model</span></span>

<span data-ttu-id="7220f-130">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="7220f-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7220f-131">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="7220f-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7220f-133">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7220f-134">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7220f-135">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="7220f-135">Name the folder *Movies*</span></span>

<span data-ttu-id="7220f-136">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="7220f-138">在“添加基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="7220f-140">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7220f-141">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="7220f-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7220f-142">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models  .RazorPagesMovieContext 更改为 RazorPagesMovie.Data  .RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="7220f-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="7220f-143">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="7220f-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="7220f-144">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="7220f-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="7220f-145">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-145">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="7220f-147">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7220f-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7220f-149">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="7220f-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="7220f-150">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="7220f-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="7220f-151">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7220f-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7220f-152">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7220f-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7220f-154">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7220f-155">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7220f-156">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="7220f-156">Name the folder *Movies*</span></span>

<span data-ttu-id="7220f-157">右键单击“Pages/Movies”  文件夹 >“添加”  >“新基架...”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="7220f-159">在“新基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="7220f-161">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7220f-162">在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="7220f-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7220f-163">在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。  </span><span class="sxs-lookup"><span data-stu-id="7220f-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="7220f-164">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="7220f-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="7220f-165">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="7220f-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="7220f-166">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-166">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="7220f-168">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7220f-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="7220f-169">添加 EF 工具</span><span class="sxs-lookup"><span data-stu-id="7220f-169">Add EF tools</span></span>

<span data-ttu-id="7220f-170">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="7220f-170">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="7220f-171">前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="7220f-171">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="7220f-172">创建的文件</span><span class="sxs-lookup"><span data-stu-id="7220f-172">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7220f-174">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="7220f-174">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7220f-175">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="7220f-175">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7220f-176">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="7220f-176">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7220f-177">已更新</span><span class="sxs-lookup"><span data-stu-id="7220f-177">Updated</span></span>

* <span data-ttu-id="7220f-178">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7220f-178">*Startup.cs*</span></span>

<span data-ttu-id="7220f-179">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7220f-179">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7220f-181">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="7220f-181">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7220f-182">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="7220f-182">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7220f-183">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="7220f-183">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7220f-184">已更新</span><span class="sxs-lookup"><span data-stu-id="7220f-184">Updated</span></span>

* <span data-ttu-id="7220f-185">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7220f-185">*Startup.cs*</span></span>

<span data-ttu-id="7220f-186">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7220f-186">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7220f-188">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="7220f-188">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="7220f-189">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="7220f-189">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="7220f-190">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7220f-190">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7220f-191">初始迁移</span><span class="sxs-lookup"><span data-stu-id="7220f-191">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7220f-193">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="7220f-193">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="7220f-194">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="7220f-194">Add an initial migration.</span></span>
* <span data-ttu-id="7220f-195">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="7220f-195">Update the database with the initial migration.</span></span>

<span data-ttu-id="7220f-196"> 从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”   。</span><span class="sxs-lookup"><span data-stu-id="7220f-196">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7220f-198">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="7220f-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="7220f-201">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="7220f-201">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="7220f-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="7220f-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="7220f-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="7220f-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="7220f-204">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="7220f-204">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="7220f-205">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="7220f-205">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="7220f-206">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="7220f-206">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="7220f-207">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="7220f-207">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="7220f-208">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="7220f-208">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="7220f-209">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7220f-209">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="7220f-210">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="7220f-210">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-211">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7220f-212">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="7220f-212">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7220f-213">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="7220f-213">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7220f-214">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="7220f-214">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7220f-215">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="7220f-215">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7220f-216">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="7220f-216">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7220f-217">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="7220f-217">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7220f-218">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="7220f-218">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7220f-219">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="7220f-219">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7220f-220">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="7220f-220">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="7220f-221">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="7220f-221">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7220f-222">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="7220f-222">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7220f-223">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="7220f-223">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7220f-224">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="7220f-224">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7220f-225">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="7220f-225">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7220f-226">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="7220f-226">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7220f-227">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7220f-227">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7220f-229">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7220f-229">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-230">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7220f-231">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7220f-231">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7220f-232">测试应用</span><span class="sxs-lookup"><span data-stu-id="7220f-232">Test the app</span></span>

* <span data-ttu-id="7220f-233">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="7220f-233">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7220f-234">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="7220f-234">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7220f-235">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="7220f-235">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7220f-236">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="7220f-236">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="7220f-238">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="7220f-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7220f-239">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="7220f-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7220f-240">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="7220f-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7220f-241">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="7220f-241">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7220f-242">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="7220f-242">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7220f-243">其他资源</span><span class="sxs-lookup"><span data-stu-id="7220f-243">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7220f-244">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7220f-244">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7220f-245">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="7220f-245">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="7220f-246">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="7220f-246">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="7220f-247">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="7220f-247">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="7220f-248">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="7220f-248">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="7220f-249">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="7220f-249">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7220f-250">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="7220f-250">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="7220f-251">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="7220f-251">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7220f-253">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-253">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7220f-254">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-254">Name the folder *Models*.</span></span>

<span data-ttu-id="7220f-255">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="7220f-255">Right click the *Models* folder.</span></span> <span data-ttu-id="7220f-256">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="7220f-256">Select **Add** > **Class**.</span></span> <span data-ttu-id="7220f-257">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-257">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-258">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-258">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7220f-259">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="7220f-259">Add a folder named *Models*.</span></span>
* <span data-ttu-id="7220f-260">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="7220f-260">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-261">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7220f-262">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-262">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="7220f-263">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-263">Name the folder *Models*.</span></span>
* <span data-ttu-id="7220f-264">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-264">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="7220f-265">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-265">In the **New File** dialog:</span></span>

  * <span data-ttu-id="7220f-266">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-266">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="7220f-267">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-267">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="7220f-268">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-268">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="7220f-269">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="7220f-269">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7220f-270">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="7220f-270">Scaffold the movie model</span></span>

<span data-ttu-id="7220f-271">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="7220f-271">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7220f-272">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="7220f-272">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-273">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-273">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7220f-274">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-274">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7220f-275">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-275">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7220f-276">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="7220f-276">Name the folder *Movies*</span></span>

<span data-ttu-id="7220f-277">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-277">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="7220f-279">在“添加基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-279">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="7220f-281">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-281">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="7220f-282">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="7220f-282">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7220f-283">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”    。</span><span class="sxs-lookup"><span data-stu-id="7220f-283">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="7220f-284">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-284">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="7220f-286">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7220f-286">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7220f-288">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="7220f-288">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="7220f-289">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7220f-289">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7220f-290">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7220f-290">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-291">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7220f-292">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-292">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7220f-293">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-293">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7220f-294">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="7220f-294">Name the folder *Movies*</span></span>

<span data-ttu-id="7220f-295">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-295">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="7220f-297">在“添加新基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-297">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="7220f-299">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="7220f-299">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7220f-300">在“模型类”下拉列表中，选择或键入“Movie”   。</span><span class="sxs-lookup"><span data-stu-id="7220f-300">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="7220f-301">在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类。  </span><span class="sxs-lookup"><span data-stu-id="7220f-301">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="7220f-302">在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="7220f-302">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="7220f-303">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="7220f-303">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="7220f-305">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="7220f-305">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="7220f-306">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="7220f-306">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="7220f-307">创建的文件</span><span class="sxs-lookup"><span data-stu-id="7220f-307">Files created</span></span>

* <span data-ttu-id="7220f-308">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="7220f-308">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7220f-309">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="7220f-309">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="7220f-310">文件已更新</span><span class="sxs-lookup"><span data-stu-id="7220f-310">File updated</span></span>

* <span data-ttu-id="7220f-311">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7220f-311">*Startup.cs*</span></span>

<span data-ttu-id="7220f-312">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="7220f-312">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7220f-313">初始迁移</span><span class="sxs-lookup"><span data-stu-id="7220f-313">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7220f-315">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="7220f-315">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="7220f-316">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="7220f-316">Add an initial migration.</span></span>
* <span data-ttu-id="7220f-317">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="7220f-317">Update the database with the initial migration.</span></span>

<span data-ttu-id="7220f-318"> 从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”   。</span><span class="sxs-lookup"><span data-stu-id="7220f-318">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7220f-320">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="7220f-320">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="7220f-321">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="7220f-321">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="7220f-322">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）  。</span><span class="sxs-lookup"><span data-stu-id="7220f-322">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="7220f-323">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="7220f-323">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="7220f-324">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="7220f-324">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="7220f-325">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="7220f-325">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="7220f-326">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="7220f-326">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="7220f-327">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="7220f-327">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-329">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-329">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="7220f-330">前面的命令生成以下警告："*No type was specified for the decimal column 'Price' on entity type 'Movie'.This will cause values to be silently truncated if they do not fit in the default precision and scale.Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " 你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="7220f-330">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7220f-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7220f-331">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7220f-332">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="7220f-332">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7220f-333">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="7220f-333">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7220f-334">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="7220f-334">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7220f-335">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="7220f-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7220f-336">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="7220f-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7220f-337">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="7220f-337">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7220f-338">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="7220f-338">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7220f-339">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="7220f-339">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="7220f-340">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="7220f-340">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="7220f-341">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="7220f-341">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7220f-342">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="7220f-342">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7220f-343">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="7220f-343">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7220f-344">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="7220f-344">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7220f-345">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="7220f-345">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7220f-346">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="7220f-346">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7220f-347">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7220f-347">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7220f-348">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7220f-348">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7220f-349">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7220f-349">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7220f-350">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7220f-350">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7220f-351">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="7220f-351">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7220f-352">测试应用</span><span class="sxs-lookup"><span data-stu-id="7220f-352">Test the app</span></span>

* <span data-ttu-id="7220f-353">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="7220f-353">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7220f-354">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="7220f-354">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7220f-355">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="7220f-355">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7220f-356">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="7220f-356">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="7220f-358">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="7220f-358">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7220f-359">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="7220f-359">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7220f-360">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="7220f-360">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7220f-361">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="7220f-361">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7220f-362">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="7220f-362">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7220f-363">其他资源</span><span class="sxs-lookup"><span data-stu-id="7220f-363">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7220f-364">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7220f-364">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
