---
title: 在 ASP.NET Core 中向 Razor Pages 应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 0934c94236b507f2f57200ded4344a71c483d356
ms.sourcegitcommit: 5fe17e54f7e4267a2fdecc6f9aa1d41166cecc34
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737853"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="e5ec9-103">在 ASP.NET Core 中向 Razor Pages 应用添加模型</span><span class="sxs-lookup"><span data-stu-id="e5ec9-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e5ec9-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e5ec9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e5ec9-105">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e5ec9-106">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e5ec9-107">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e5ec9-108">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e5ec9-109">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e5ec9-110">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e5ec9-111">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="e5ec9-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5ec9-113">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e5ec9-114">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-114">Name the folder *Models*.</span></span>

<span data-ttu-id="e5ec9-115">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-115">Right click the *Models* folder.</span></span> <span data-ttu-id="e5ec9-116">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="e5ec9-117">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5ec9-119">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e5ec9-120">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5ec9-122">在 Solution Pad 中，右键单击“RazorPagesMovie”  项目，然后选择“添加”  >“新建文件夹...”  。将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="e5ec9-123">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件...”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="e5ec9-124">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e5ec9-125">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e5ec9-126">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e5ec9-127">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="e5ec9-128">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e5ec9-129">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="e5ec9-129">Scaffold the movie model</span></span>

<span data-ttu-id="e5ec9-130">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e5ec9-131">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5ec9-133">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e5ec9-134">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e5ec9-135">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="e5ec9-135">Name the folder *Movies*</span></span>

<span data-ttu-id="e5ec9-136">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="e5ec9-138">在“添加基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“添加”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="e5ec9-140">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e5ec9-141">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e5ec9-142">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models  .RazorPagesMovieContext 更改为 RazorPagesMovie.Data  .RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e5ec9-143">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e5ec9-144">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e5ec9-145">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-145">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="e5ec9-147">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e5ec9-149">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e5ec9-150">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e5ec9-151">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e5ec9-152">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5ec9-154">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e5ec9-155">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e5ec9-156">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="e5ec9-156">Name the folder *Movies*</span></span>

<span data-ttu-id="e5ec9-157">右键单击“Pages/Movies”  文件夹 >“添加”  >“新基架...”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="e5ec9-159">在“新基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e5ec9-161">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e5ec9-162">在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e5ec9-163">在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。  </span><span class="sxs-lookup"><span data-stu-id="e5ec9-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e5ec9-164">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e5ec9-165">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e5ec9-166">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-166">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="e5ec9-168">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="e5ec9-169">创建的文件</span><span class="sxs-lookup"><span data-stu-id="e5ec9-169">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5ec9-171">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-171">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e5ec9-172">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-172">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e5ec9-173">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="e5ec9-173">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e5ec9-174">已更新</span><span class="sxs-lookup"><span data-stu-id="e5ec9-174">Updated</span></span>

* <span data-ttu-id="e5ec9-175">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e5ec9-175">*Startup.cs*</span></span>

<span data-ttu-id="e5ec9-176">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-176">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5ec9-178">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-178">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e5ec9-179">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-179">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e5ec9-180">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="e5ec9-180">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e5ec9-181">已更新</span><span class="sxs-lookup"><span data-stu-id="e5ec9-181">Updated</span></span>

* <span data-ttu-id="e5ec9-182">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e5ec9-182">*Startup.cs*</span></span>

<span data-ttu-id="e5ec9-183">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-183">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-184">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-184">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5ec9-185">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-185">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e5ec9-186">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-186">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e5ec9-187">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-187">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e5ec9-188">初始迁移</span><span class="sxs-lookup"><span data-stu-id="e5ec9-188">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5ec9-190">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-190">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e5ec9-191">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-191">Add an initial migration.</span></span>
* <span data-ttu-id="e5ec9-192">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-192">Update the database with the initial migration.</span></span>

<span data-ttu-id="e5ec9-193"> 从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-193">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e5ec9-195">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-195">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-197">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-197">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="e5ec9-198">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="e5ec9-198">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e5ec9-199">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="e5ec9-199">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e5ec9-200">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="e5ec9-200">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e5ec9-201">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-201">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="e5ec9-202">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-202">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e5ec9-203">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-203">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e5ec9-204">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-204">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e5ec9-205">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-205">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e5ec9-206">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-206">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e5ec9-207">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-207">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-208">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e5ec9-209">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="e5ec9-209">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e5ec9-210">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-210">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e5ec9-211">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-211">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e5ec9-212">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-212">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e5ec9-213">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-213">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e5ec9-214">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-214">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e5ec9-215">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-215">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e5ec9-216">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-216">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e5ec9-217">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-217">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e5ec9-218">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-218">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e5ec9-219">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-219">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e5ec9-220">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-220">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e5ec9-221">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-221">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e5ec9-222">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-222">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e5ec9-223">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-223">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e5ec9-224">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-224">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-225">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-225">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5ec9-226">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-226">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5ec9-228">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-228">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e5ec9-229">测试应用</span><span class="sxs-lookup"><span data-stu-id="e5ec9-229">Test the app</span></span>

* <span data-ttu-id="e5ec9-230">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-230">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e5ec9-231">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-231">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e5ec9-232">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-232">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e5ec9-233">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-233">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e5ec9-235">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-235">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e5ec9-236">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-236">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e5ec9-237">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-237">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e5ec9-238">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-238">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e5ec9-239">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-239">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e5ec9-240">其他资源</span><span class="sxs-lookup"><span data-stu-id="e5ec9-240">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e5ec9-241">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e5ec9-241">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5ec9-242">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-242">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e5ec9-243">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-243">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e5ec9-244">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-244">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e5ec9-245">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-245">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e5ec9-246">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-246">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e5ec9-247">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-247">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e5ec9-248">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="e5ec9-248">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-249">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-249">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5ec9-250">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-250">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e5ec9-251">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-251">Name the folder *Models*.</span></span>

<span data-ttu-id="e5ec9-252">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-252">Right click the *Models* folder.</span></span> <span data-ttu-id="e5ec9-253">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-253">Select **Add** > **Class**.</span></span> <span data-ttu-id="e5ec9-254">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-254">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-255">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-255">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5ec9-256">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-256">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e5ec9-257">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-257">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-258">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-258">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5ec9-259">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-259">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5ec9-260">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-260">Name the folder *Models*.</span></span>
* <span data-ttu-id="e5ec9-261">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-261">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e5ec9-262">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-262">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e5ec9-263">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-263">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e5ec9-264">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-264">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e5ec9-265">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-265">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="e5ec9-266">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-266">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e5ec9-267">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="e5ec9-267">Scaffold the movie model</span></span>

<span data-ttu-id="e5ec9-268">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-268">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e5ec9-269">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-269">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-270">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5ec9-271">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-271">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e5ec9-272">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-272">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e5ec9-273">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="e5ec9-273">Name the folder *Movies*</span></span>

<span data-ttu-id="e5ec9-274">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-274">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="e5ec9-276">在“添加基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“添加”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-276">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="e5ec9-278">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-278">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e5ec9-279">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-279">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e5ec9-280">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”    。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-280">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e5ec9-281">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-281">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="e5ec9-283">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-283">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-284">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-284">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e5ec9-285">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-285">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="e5ec9-286">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-286">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e5ec9-287">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-287">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-288">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5ec9-289">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-289">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e5ec9-290">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-290">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e5ec9-291">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="e5ec9-291">Name the folder *Movies*</span></span>

<span data-ttu-id="e5ec9-292">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-292">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="e5ec9-294">在“添加新基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“添加”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-294">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e5ec9-296">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-296">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e5ec9-297">在“模型类”下拉列表中，选择或键入“Movie”   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-297">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="e5ec9-298">在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类。  </span><span class="sxs-lookup"><span data-stu-id="e5ec9-298">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="e5ec9-299">在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="e5ec9-299">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e5ec9-300">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-300">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="e5ec9-302">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-302">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="e5ec9-303">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-303">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e5ec9-304">创建的文件</span><span class="sxs-lookup"><span data-stu-id="e5ec9-304">Files created</span></span>

* <span data-ttu-id="e5ec9-305">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-305">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e5ec9-306">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="e5ec9-306">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e5ec9-307">文件已更新</span><span class="sxs-lookup"><span data-stu-id="e5ec9-307">File updated</span></span>

* <span data-ttu-id="e5ec9-308">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e5ec9-308">*Startup.cs*</span></span>

<span data-ttu-id="e5ec9-309">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-309">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e5ec9-310">初始迁移</span><span class="sxs-lookup"><span data-stu-id="e5ec9-310">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-311">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5ec9-312">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-312">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e5ec9-313">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-313">Add an initial migration.</span></span>
* <span data-ttu-id="e5ec9-314">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-314">Update the database with the initial migration.</span></span>

<span data-ttu-id="e5ec9-315"> 从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”   。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-315">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e5ec9-317">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-317">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e5ec9-318">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-318">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e5ec9-319">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-319">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="e5ec9-320">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-320">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="e5ec9-321">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-321">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e5ec9-322">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-322">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e5ec9-323">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-323">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e5ec9-324">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-324">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-326">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="e5ec9-327">前面的命令生成以下警告："*No type was specified for the decimal column 'Price' on entity type 'Movie'.This will cause values to be silently truncated if they do not fit in the default precision and scale.Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " 你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-327">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5ec9-328">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5ec9-328">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e5ec9-329">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="e5ec9-329">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e5ec9-330">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-330">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e5ec9-331">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-331">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e5ec9-332">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-332">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e5ec9-333">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-333">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e5ec9-334">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-334">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e5ec9-335">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-335">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e5ec9-336">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-336">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e5ec9-337">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-337">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e5ec9-338">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-338">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e5ec9-339">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-339">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e5ec9-340">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-340">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e5ec9-341">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-341">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e5ec9-342">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-342">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e5ec9-343">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-343">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e5ec9-344">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-344">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5ec9-345">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5ec9-345">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5ec9-346">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-346">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5ec9-347">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5ec9-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5ec9-348">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-348">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e5ec9-349">测试应用</span><span class="sxs-lookup"><span data-stu-id="e5ec9-349">Test the app</span></span>

* <span data-ttu-id="e5ec9-350">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-350">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e5ec9-351">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="e5ec9-351">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e5ec9-352">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-352">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e5ec9-353">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-353">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e5ec9-355">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-355">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e5ec9-356">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-356">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e5ec9-357">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-357">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e5ec9-358">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-358">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e5ec9-359">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="e5ec9-359">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e5ec9-360">其他资源</span><span class="sxs-lookup"><span data-stu-id="e5ec9-360">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e5ec9-361">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e5ec9-361">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
