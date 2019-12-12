---
title: 在 ASP.NET Core 中向 Razor Pages 应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 95b6d3e016edcd2e13207c8e658cf0d2fb21f945
ms.sourcegitcommit: 4e3edff24ba6e43a103fee1b126c9826241bb37b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74959070"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="e5743-103">在 ASP.NET Core 中向 Razor Pages 应用添加模型</span><span class="sxs-lookup"><span data-stu-id="e5743-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e5743-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e5743-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e5743-105">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="e5743-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e5743-106">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="e5743-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e5743-107">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="e5743-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e5743-108">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="e5743-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e5743-109">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="e5743-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e5743-110">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="e5743-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e5743-111">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="e5743-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5743-113">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="e5743-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e5743-114">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="e5743-114">Name the folder *Models*.</span></span>

<span data-ttu-id="e5743-115">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="e5743-115">Right click the *Models* folder.</span></span> <span data-ttu-id="e5743-116">选择“添加” > “类”。</span><span class="sxs-lookup"><span data-stu-id="e5743-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="e5743-117">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="e5743-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5743-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5743-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5743-119">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="e5743-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e5743-120">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="e5743-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5743-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5743-122">在解决方案资源管理器中，右键单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="e5743-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5743-123">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="e5743-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="e5743-124">右键单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="e5743-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e5743-125">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="e5743-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e5743-126">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="e5743-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e5743-127">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="e5743-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e5743-128">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="e5743-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="e5743-129">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="e5743-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e5743-130">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="e5743-130">Scaffold the movie model</span></span>

<span data-ttu-id="e5743-131">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="e5743-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e5743-132">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="e5743-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5743-134">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="e5743-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e5743-135">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="e5743-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e5743-136">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="e5743-136">Name the folder *Movies*</span></span>

<span data-ttu-id="e5743-137">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="e5743-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="e5743-139">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="e5743-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="e5743-141">完成“使用实体框架(CRUD)添加 Razor Pages”对话框：</span><span class="sxs-lookup"><span data-stu-id="e5743-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e5743-142">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)。</span><span class="sxs-lookup"><span data-stu-id="e5743-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e5743-143">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models.RazorPagesMovieContext 更改为 RazorPagesMovie.Data.RazorPagesMovieContext。</span><span class="sxs-lookup"><span data-stu-id="e5743-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e5743-144">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="e5743-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e5743-145">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="e5743-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e5743-146">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="e5743-146">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="e5743-148">appsettings.json 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="e5743-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5743-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5743-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e5743-150">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="e5743-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e5743-151">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="e5743-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e5743-152">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5743-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e5743-153">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5743-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5743-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5743-155">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="e5743-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e5743-156">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="e5743-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e5743-157">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5743-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="e5743-158">创建的文件</span><span class="sxs-lookup"><span data-stu-id="e5743-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5743-160">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="e5743-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e5743-161">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="e5743-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e5743-162">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="e5743-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e5743-163">已更新</span><span class="sxs-lookup"><span data-stu-id="e5743-163">Updated</span></span>

* <span data-ttu-id="e5743-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e5743-164">*Startup.cs*</span></span>

<span data-ttu-id="e5743-165">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="e5743-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="e5743-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e5743-167">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="e5743-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e5743-168">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="e5743-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e5743-169">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="e5743-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e5743-170">初始迁移</span><span class="sxs-lookup"><span data-stu-id="e5743-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5743-172">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="e5743-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e5743-173">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="e5743-173">Add an initial migration.</span></span>
* <span data-ttu-id="e5743-174">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="e5743-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="e5743-175">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”。</span><span class="sxs-lookup"><span data-stu-id="e5743-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e5743-177">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="e5743-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5743-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5743-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5743-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="e5743-180">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="e5743-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e5743-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="e5743-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e5743-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="e5743-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e5743-183">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="e5743-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="e5743-184">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="e5743-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e5743-185">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="e5743-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e5743-186">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="e5743-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e5743-187">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="e5743-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e5743-188">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e5743-189">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e5743-191">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="e5743-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e5743-192">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="e5743-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e5743-193">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="e5743-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e5743-194">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="e5743-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e5743-195">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="e5743-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e5743-196">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="e5743-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e5743-197">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e5743-198">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="e5743-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e5743-199">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="e5743-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e5743-200">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="e5743-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e5743-201">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="e5743-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e5743-202">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="e5743-202">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e5743-203">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="e5743-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e5743-204">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="e5743-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e5743-205">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="e5743-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e5743-206">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e5743-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5743-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5743-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5743-208">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5743-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5743-210">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e5743-211">测试应用</span><span class="sxs-lookup"><span data-stu-id="e5743-211">Test the app</span></span>

* <span data-ttu-id="e5743-212">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="e5743-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e5743-213">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="e5743-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e5743-214">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="e5743-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e5743-215">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="e5743-215">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e5743-217">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="e5743-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e5743-218">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="e5743-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e5743-219">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="e5743-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e5743-220">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="e5743-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e5743-221">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="e5743-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e5743-222">其他资源</span><span class="sxs-lookup"><span data-stu-id="e5743-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e5743-223">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e5743-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5743-224">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="e5743-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e5743-225">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="e5743-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e5743-226">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="e5743-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e5743-227">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="e5743-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e5743-228">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="e5743-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e5743-229">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="e5743-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e5743-230">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="e5743-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5743-232">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="e5743-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e5743-233">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="e5743-233">Name the folder *Models*.</span></span>

<span data-ttu-id="e5743-234">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="e5743-234">Right click the *Models* folder.</span></span> <span data-ttu-id="e5743-235">选择“添加” > “类”。</span><span class="sxs-lookup"><span data-stu-id="e5743-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="e5743-236">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="e5743-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5743-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5743-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e5743-238">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="e5743-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e5743-239">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="e5743-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5743-240">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5743-241">在解决方案资源管理器中，右键单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="e5743-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e5743-242">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="e5743-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="e5743-243">右键单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="e5743-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e5743-244">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="e5743-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e5743-245">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="e5743-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e5743-246">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="e5743-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e5743-247">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="e5743-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="e5743-248">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="e5743-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e5743-249">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="e5743-249">Scaffold the movie model</span></span>

<span data-ttu-id="e5743-250">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="e5743-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e5743-251">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="e5743-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5743-253">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="e5743-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e5743-254">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="e5743-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e5743-255">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="e5743-255">Name the folder *Movies*</span></span>

<span data-ttu-id="e5743-256">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="e5743-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="e5743-258">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="e5743-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="e5743-260">完成“使用实体框架(CRUD)添加 Razor Pages”对话框：</span><span class="sxs-lookup"><span data-stu-id="e5743-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e5743-261">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)。</span><span class="sxs-lookup"><span data-stu-id="e5743-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e5743-262">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="e5743-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e5743-263">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="e5743-263">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="e5743-265">appsettings.json 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="e5743-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5743-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5743-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e5743-267">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="e5743-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="e5743-268">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5743-268">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e5743-269">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5743-269">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5743-270">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-270">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e5743-271">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="e5743-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e5743-272">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="e5743-272">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="e5743-273">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="e5743-273">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e5743-274">创建的文件</span><span class="sxs-lookup"><span data-stu-id="e5743-274">Files created</span></span>

* <span data-ttu-id="e5743-275">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="e5743-275">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e5743-276">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="e5743-276">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e5743-277">文件已更新</span><span class="sxs-lookup"><span data-stu-id="e5743-277">File updated</span></span>

* <span data-ttu-id="e5743-278">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e5743-278">*Startup.cs*</span></span>

<span data-ttu-id="e5743-279">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="e5743-279">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e5743-280">初始迁移</span><span class="sxs-lookup"><span data-stu-id="e5743-280">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5743-282">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="e5743-282">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e5743-283">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="e5743-283">Add an initial migration.</span></span>
* <span data-ttu-id="e5743-284">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="e5743-284">Update the database with the initial migration.</span></span>

<span data-ttu-id="e5743-285">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”。</span><span class="sxs-lookup"><span data-stu-id="e5743-285">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e5743-287">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="e5743-287">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e5743-288">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="e5743-288">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e5743-289">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="e5743-289">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="e5743-290">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="e5743-290">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="e5743-291">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="e5743-291">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e5743-292">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="e5743-292">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e5743-293">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-293">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e5743-294">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="e5743-294">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5743-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5743-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5743-296">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-296">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="e5743-297">前面的命令生成以下警告："*No type was specified for the decimal column 'Price' on entity type 'Movie'.This will cause values to be silently truncated if they do not fit in the default precision and scale.Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" 你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="e5743-297">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5743-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5743-298">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e5743-299">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="e5743-299">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e5743-300">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="e5743-300">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e5743-301">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="e5743-301">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e5743-302">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="e5743-302">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e5743-303">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="e5743-303">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e5743-304">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="e5743-304">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e5743-305">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-305">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e5743-306">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="e5743-306">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e5743-307">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="e5743-307">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e5743-308">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="e5743-308">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e5743-309">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="e5743-309">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e5743-310">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="e5743-310">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e5743-311">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="e5743-311">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e5743-312">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="e5743-312">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e5743-313">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="e5743-313">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e5743-314">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e5743-314">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e5743-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e5743-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e5743-316">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-316">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e5743-317">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e5743-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e5743-318">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="e5743-318">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e5743-319">测试应用</span><span class="sxs-lookup"><span data-stu-id="e5743-319">Test the app</span></span>

* <span data-ttu-id="e5743-320">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="e5743-320">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e5743-321">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="e5743-321">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e5743-322">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="e5743-322">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e5743-323">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="e5743-323">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e5743-325">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="e5743-325">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e5743-326">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="e5743-326">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e5743-327">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="e5743-327">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e5743-328">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="e5743-328">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e5743-329">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="e5743-329">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e5743-330">其他资源</span><span class="sxs-lookup"><span data-stu-id="e5743-330">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e5743-331">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e5743-331">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
