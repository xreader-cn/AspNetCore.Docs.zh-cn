---
title: 在 ASP.NET Core 中向 Razor Pages 应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
ms.date: 11/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 312b3d4eb13eb04453bf0c3256fc362918157a45
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634180"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="b5327-103">在 ASP.NET Core 中向 Razor Pages 应用添加模型</span><span class="sxs-lookup"><span data-stu-id="b5327-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="b5327-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b5327-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5327-105">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="b5327-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="b5327-106">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="b5327-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="b5327-107">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="b5327-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="b5327-108">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="b5327-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="b5327-109">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b5327-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="b5327-110">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="b5327-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="b5327-111">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="b5327-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5327-113">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="b5327-114">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-114">Name the folder *Models*.</span></span>

<span data-ttu-id="b5327-115">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="b5327-115">Right click the *Models* folder.</span></span> <span data-ttu-id="b5327-116">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="b5327-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="b5327-117">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b5327-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5327-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b5327-119">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="b5327-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="b5327-120">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="b5327-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5327-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b5327-122">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="b5327-123">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="b5327-124">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="b5327-125">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="b5327-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b5327-126">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="b5327-127">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="b5327-128">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="b5327-129">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="b5327-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="b5327-130">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="b5327-130">Scaffold the movie model</span></span>

<span data-ttu-id="b5327-131">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="b5327-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="b5327-132">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="b5327-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5327-134">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="b5327-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="b5327-135">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="b5327-136">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="b5327-136">Name the folder *Movies*</span></span>

<span data-ttu-id="b5327-137">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="b5327-139">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”    。</span><span class="sxs-lookup"><span data-stu-id="b5327-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="b5327-141">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="b5327-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b5327-142">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="b5327-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="b5327-143">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models  .RazorPagesMovieContext 更改为 RazorPagesMovie.Data  .RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="b5327-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="b5327-144">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="b5327-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="b5327-145">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="b5327-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="b5327-146">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="b5327-146">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="b5327-148">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="b5327-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b5327-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5327-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="b5327-150">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="b5327-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b5327-151">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="b5327-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b5327-152">**对于 Windows**：运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b5327-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="b5327-153">**对于 macOS 和 Linux**：运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b5327-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5327-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b5327-155">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="b5327-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b5327-156">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="b5327-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b5327-157">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b5327-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="b5327-158">创建的文件</span><span class="sxs-lookup"><span data-stu-id="b5327-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5327-160">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="b5327-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="b5327-161">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="b5327-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="b5327-162">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="b5327-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="b5327-163">已更新</span><span class="sxs-lookup"><span data-stu-id="b5327-163">Updated</span></span>

* <span data-ttu-id="b5327-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b5327-164">*Startup.cs*</span></span>

<span data-ttu-id="b5327-165">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="b5327-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="b5327-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="b5327-167">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="b5327-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="b5327-168">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="b5327-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="b5327-169">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="b5327-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="b5327-170">初始迁移</span><span class="sxs-lookup"><span data-stu-id="b5327-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5327-172">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="b5327-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="b5327-173">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="b5327-173">Add an initial migration.</span></span>
* <span data-ttu-id="b5327-174">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="b5327-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="b5327-175">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="b5327-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="b5327-177">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="b5327-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b5327-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5327-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5327-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="b5327-180">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="b5327-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="b5327-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="b5327-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="b5327-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="b5327-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="b5327-183">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="b5327-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="b5327-184">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="b5327-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="b5327-185">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="b5327-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="b5327-186">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="b5327-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="b5327-187">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="b5327-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="b5327-188">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5327-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="b5327-189">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="b5327-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b5327-191">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="b5327-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b5327-192">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="b5327-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b5327-193">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="b5327-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b5327-194">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="b5327-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b5327-195">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="b5327-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b5327-196">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="b5327-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b5327-197">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5327-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b5327-198">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="b5327-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="b5327-199">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="b5327-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="b5327-200">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="b5327-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b5327-201">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="b5327-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="b5327-202">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="b5327-202">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b5327-203">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="b5327-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b5327-204">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="b5327-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b5327-205">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="b5327-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b5327-206">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="b5327-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b5327-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5327-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b5327-208">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5327-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5327-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b5327-210">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5327-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="b5327-211">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="b5327-211">Test the app</span></span>

* <span data-ttu-id="b5327-212">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="b5327-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="b5327-213">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="b5327-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="b5327-214">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="b5327-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="b5327-215">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="b5327-215">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="b5327-217">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="b5327-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b5327-218">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="b5327-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b5327-219">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="b5327-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b5327-220">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="b5327-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="b5327-221">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="b5327-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5327-222">其他资源</span><span class="sxs-lookup"><span data-stu-id="b5327-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b5327-223">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="b5327-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b5327-224">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="b5327-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="b5327-225">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="b5327-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="b5327-226">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="b5327-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="b5327-227">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="b5327-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="b5327-228">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b5327-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="b5327-229">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="b5327-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="b5327-230">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="b5327-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5327-232">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="b5327-233">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-233">Name the folder *Models*.</span></span>

<span data-ttu-id="b5327-234">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="b5327-234">Right click the *Models* folder.</span></span> <span data-ttu-id="b5327-235">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="b5327-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="b5327-236">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b5327-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5327-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b5327-238">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="b5327-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="b5327-239">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="b5327-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5327-240">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b5327-241">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="b5327-242">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="b5327-243">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="b5327-244">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="b5327-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="b5327-245">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="b5327-246">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="b5327-247">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="b5327-248">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="b5327-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="b5327-249">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="b5327-249">Scaffold the movie model</span></span>

<span data-ttu-id="b5327-250">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="b5327-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="b5327-251">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="b5327-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5327-253">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="b5327-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="b5327-254">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="b5327-255">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="b5327-255">Name the folder *Movies*</span></span>

<span data-ttu-id="b5327-256">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="b5327-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="b5327-258">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”    。</span><span class="sxs-lookup"><span data-stu-id="b5327-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="b5327-260">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="b5327-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="b5327-261">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="b5327-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="b5327-262">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”    。</span><span class="sxs-lookup"><span data-stu-id="b5327-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="b5327-263">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="b5327-263">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="b5327-265">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="b5327-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b5327-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5327-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="b5327-267">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="b5327-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b5327-268">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="b5327-268">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b5327-269">**对于 Windows**：运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b5327-269">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="b5327-270">**对于 macOS 和 Linux**：运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b5327-270">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5327-271">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-271">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b5327-272">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="b5327-272">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="b5327-273">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="b5327-273">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="b5327-274">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b5327-274">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="b5327-275">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="b5327-275">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="b5327-276">创建的文件</span><span class="sxs-lookup"><span data-stu-id="b5327-276">Files created</span></span>

* <span data-ttu-id="b5327-277">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="b5327-277">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="b5327-278">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="b5327-278">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="b5327-279">文件已更新</span><span class="sxs-lookup"><span data-stu-id="b5327-279">File updated</span></span>

* <span data-ttu-id="b5327-280">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b5327-280">*Startup.cs*</span></span>

<span data-ttu-id="b5327-281">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="b5327-281">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="b5327-282">初始迁移</span><span class="sxs-lookup"><span data-stu-id="b5327-282">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-283">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b5327-284">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="b5327-284">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="b5327-285">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="b5327-285">Add an initial migration.</span></span>
* <span data-ttu-id="b5327-286">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="b5327-286">Update the database with the initial migration.</span></span>

<span data-ttu-id="b5327-287">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="b5327-287">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="b5327-289">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="b5327-289">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="b5327-290">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="b5327-290">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="b5327-291">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）  。</span><span class="sxs-lookup"><span data-stu-id="b5327-291">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="b5327-292">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="b5327-292">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="b5327-293">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="b5327-293">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="b5327-294">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="b5327-294">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="b5327-295">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="b5327-295">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="b5327-296">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="b5327-296">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b5327-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5327-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5327-298">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="b5327-299">前面的命令生成以下警告："*No type was specified for the decimal column 'Price' on entity type 'Movie'.This will cause values to be silently truncated if they do not fit in the default precision and scale.Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " 你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="b5327-299">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5327-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5327-300">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b5327-301">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="b5327-301">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b5327-302">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="b5327-302">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b5327-303">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="b5327-303">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b5327-304">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="b5327-304">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b5327-305">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="b5327-305">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b5327-306">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="b5327-306">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b5327-307">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5327-307">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b5327-308">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="b5327-308">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="b5327-309">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="b5327-309">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="b5327-310">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="b5327-310">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b5327-311">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="b5327-311">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="b5327-312">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="b5327-312">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="b5327-313">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="b5327-313">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="b5327-314">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="b5327-314">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b5327-315">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="b5327-315">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b5327-316">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="b5327-316">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b5327-317">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b5327-317">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b5327-318">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5327-318">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5327-319">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b5327-319">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b5327-320">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5327-320">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="b5327-321">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="b5327-321">Test the app</span></span>

* <span data-ttu-id="b5327-322">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="b5327-322">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="b5327-323">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="b5327-323">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="b5327-324">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="b5327-324">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="b5327-325">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="b5327-325">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="b5327-327">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="b5327-327">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="b5327-328">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="b5327-328">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="b5327-329">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="b5327-329">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="b5327-330">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="b5327-330">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="b5327-331">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="b5327-331">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5327-332">其他资源</span><span class="sxs-lookup"><span data-stu-id="b5327-332">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b5327-333">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="b5327-333">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
