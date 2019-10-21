---
title: 在 ASP.NET Core 中向 Razor Pages 应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 4f8b80cb51bd10eb3b136a780dc123c41d61c0a5
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519072"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="4038a-103">在 ASP.NET Core 中向 Razor Pages 应用添加模型</span><span class="sxs-lookup"><span data-stu-id="4038a-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="4038a-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4038a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4038a-105">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="4038a-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="4038a-106">这些类与 [Entity Framework Core](/ef/core)（EF Core）一起使用来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="4038a-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4038a-107">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="4038a-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="4038a-108">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="4038a-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4038a-109">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="4038a-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="4038a-110">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="4038a-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4038a-112">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="4038a-113">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-113">Name the folder *Models*.</span></span>

<span data-ttu-id="4038a-114">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4038a-114">Right click the *Models* folder.</span></span> <span data-ttu-id="4038a-115">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="4038a-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="4038a-116">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4038a-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4038a-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4038a-118">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4038a-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="4038a-119">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4038a-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4038a-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4038a-121">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="4038a-122">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="4038a-123">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="4038a-124">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="4038a-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="4038a-125">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="4038a-126">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="4038a-127">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="4038a-128">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="4038a-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4038a-129">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="4038a-129">Scaffold the movie model</span></span>

<span data-ttu-id="4038a-130">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="4038a-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4038a-131">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="4038a-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4038a-133">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="4038a-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="4038a-134">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="4038a-135">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="4038a-135">Name the folder *Movies*</span></span>

<span data-ttu-id="4038a-136">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="4038a-138">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”    。</span><span class="sxs-lookup"><span data-stu-id="4038a-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="4038a-140">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="4038a-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="4038a-141">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="4038a-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="4038a-142">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models  .RazorPagesMovieContext 更改为 RazorPagesMovie.Data  .RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="4038a-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="4038a-143">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="4038a-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="4038a-144">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="4038a-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="4038a-145">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-145">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="4038a-147">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="4038a-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4038a-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4038a-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4038a-149">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4038a-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4038a-150">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4038a-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4038a-151">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4038a-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="4038a-152">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4038a-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4038a-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4038a-154">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4038a-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4038a-155">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4038a-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4038a-156">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4038a-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="4038a-157">创建的文件</span><span class="sxs-lookup"><span data-stu-id="4038a-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4038a-159">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="4038a-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="4038a-160">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="4038a-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="4038a-161">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="4038a-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="4038a-162">已更新</span><span class="sxs-lookup"><span data-stu-id="4038a-162">Updated</span></span>

* <span data-ttu-id="4038a-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4038a-163">*Startup.cs*</span></span>

<span data-ttu-id="4038a-164">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="4038a-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4038a-165">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4038a-166">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="4038a-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="4038a-167">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="4038a-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="4038a-168">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="4038a-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4038a-169">初始迁移</span><span class="sxs-lookup"><span data-stu-id="4038a-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4038a-171">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="4038a-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="4038a-172">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="4038a-172">Add an initial migration.</span></span>
* <span data-ttu-id="4038a-173">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="4038a-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="4038a-174">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="4038a-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4038a-176">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="4038a-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4038a-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4038a-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4038a-178">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="4038a-179">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="4038a-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="4038a-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="4038a-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="4038a-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="4038a-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="4038a-182">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="4038a-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="4038a-183">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4038a-183">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="4038a-184">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="4038a-184">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="4038a-185">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4038a-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="4038a-186">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4038a-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="4038a-187">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4038a-187">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="4038a-188">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4038a-188">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4038a-190">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="4038a-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4038a-191">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="4038a-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4038a-192">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="4038a-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="4038a-193">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="4038a-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4038a-194">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="4038a-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="4038a-195">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="4038a-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="4038a-196">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="4038a-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4038a-197">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="4038a-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="4038a-198">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="4038a-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4038a-199">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="4038a-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4038a-200">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="4038a-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="4038a-201">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="4038a-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4038a-202">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="4038a-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4038a-203">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="4038a-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4038a-204">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="4038a-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4038a-205">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="4038a-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4038a-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4038a-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4038a-207">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4038a-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4038a-208">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4038a-209">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4038a-209">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4038a-210">测试应用</span><span class="sxs-lookup"><span data-stu-id="4038a-210">Test the app</span></span>

* <span data-ttu-id="4038a-211">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="4038a-211">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4038a-212">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="4038a-212">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4038a-213">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="4038a-213">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4038a-214">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="4038a-214">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="4038a-216">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="4038a-216">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4038a-217">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="4038a-217">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4038a-218">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="4038a-218">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4038a-219">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="4038a-219">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="4038a-220">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="4038a-220">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4038a-221">其他资源</span><span class="sxs-lookup"><span data-stu-id="4038a-221">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4038a-222">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="4038a-222">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4038a-223">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="4038a-223">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="4038a-224">这些类与 [Entity Framework Core](/ef/core)（EF Core）一起使用来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="4038a-224">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4038a-225">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="4038a-225">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="4038a-226">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="4038a-226">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4038a-227">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="4038a-227">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="4038a-228">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="4038a-228">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4038a-230">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-230">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="4038a-231">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-231">Name the folder *Models*.</span></span>

<span data-ttu-id="4038a-232">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4038a-232">Right click the *Models* folder.</span></span> <span data-ttu-id="4038a-233">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="4038a-233">Select **Add** > **Class**.</span></span> <span data-ttu-id="4038a-234">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-234">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4038a-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4038a-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4038a-236">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4038a-236">Add a folder named *Models*.</span></span>
* <span data-ttu-id="4038a-237">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4038a-237">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4038a-238">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-238">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4038a-239">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-239">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="4038a-240">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-240">Name the folder *Models*.</span></span>
* <span data-ttu-id="4038a-241">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-241">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="4038a-242">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="4038a-242">In the **New File** dialog:</span></span>

  * <span data-ttu-id="4038a-243">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-243">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="4038a-244">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-244">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="4038a-245">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-245">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="4038a-246">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="4038a-246">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4038a-247">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="4038a-247">Scaffold the movie model</span></span>

<span data-ttu-id="4038a-248">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="4038a-248">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4038a-249">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="4038a-249">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4038a-251">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="4038a-251">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="4038a-252">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-252">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="4038a-253">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="4038a-253">Name the folder *Movies*</span></span>

<span data-ttu-id="4038a-254">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-254">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="4038a-256">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”    。</span><span class="sxs-lookup"><span data-stu-id="4038a-256">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="4038a-258">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="4038a-258">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="4038a-259">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="4038a-259">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="4038a-260">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”    。</span><span class="sxs-lookup"><span data-stu-id="4038a-260">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="4038a-261">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="4038a-261">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="4038a-263">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="4038a-263">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4038a-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4038a-264">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4038a-265">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4038a-265">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4038a-266">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4038a-266">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4038a-267">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4038a-267">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="4038a-268">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4038a-268">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4038a-269">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-269">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4038a-270">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4038a-270">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4038a-271">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4038a-271">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4038a-272">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4038a-272">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="4038a-273">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="4038a-273">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="4038a-274">创建的文件</span><span class="sxs-lookup"><span data-stu-id="4038a-274">Files created</span></span>

* <span data-ttu-id="4038a-275">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="4038a-275">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="4038a-276">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="4038a-276">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="4038a-277">文件已更新</span><span class="sxs-lookup"><span data-stu-id="4038a-277">File updated</span></span>

* <span data-ttu-id="4038a-278">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4038a-278">*Startup.cs*</span></span>

<span data-ttu-id="4038a-279">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="4038a-279">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4038a-280">初始迁移</span><span class="sxs-lookup"><span data-stu-id="4038a-280">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4038a-282">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="4038a-282">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="4038a-283">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="4038a-283">Add an initial migration.</span></span>
* <span data-ttu-id="4038a-284">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="4038a-284">Update the database with the initial migration.</span></span>

<span data-ttu-id="4038a-285">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="4038a-285">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4038a-287">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="4038a-287">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="4038a-288">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4038a-288">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4038a-289">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）  。</span><span class="sxs-lookup"><span data-stu-id="4038a-289">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4038a-290">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4038a-290">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="4038a-291">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4038a-291">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="4038a-292">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="4038a-292">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="4038a-293">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4038a-293">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="4038a-294">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="4038a-294">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4038a-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4038a-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4038a-296">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-296">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="4038a-297">前面的命令生成以下警告："*No type was specified for the decimal column 'Price' on entity type 'Movie'.This will cause values to be silently truncated if they do not fit in the default precision and scale.Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " 你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="4038a-297">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4038a-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4038a-298">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4038a-299">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="4038a-299">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4038a-300">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="4038a-300">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4038a-301">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="4038a-301">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="4038a-302">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="4038a-302">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4038a-303">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="4038a-303">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="4038a-304">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="4038a-304">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="4038a-305">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="4038a-305">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4038a-306">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="4038a-306">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="4038a-307">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="4038a-307">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4038a-308">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="4038a-308">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4038a-309">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="4038a-309">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="4038a-310">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="4038a-310">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4038a-311">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="4038a-311">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4038a-312">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="4038a-312">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4038a-313">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="4038a-313">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4038a-314">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="4038a-314">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4038a-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4038a-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4038a-316">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4038a-316">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4038a-317">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4038a-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4038a-318">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4038a-318">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4038a-319">测试应用</span><span class="sxs-lookup"><span data-stu-id="4038a-319">Test the app</span></span>

* <span data-ttu-id="4038a-320">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="4038a-320">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4038a-321">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="4038a-321">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4038a-322">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="4038a-322">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4038a-323">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="4038a-323">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="4038a-325">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="4038a-325">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4038a-326">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="4038a-326">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4038a-327">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="4038a-327">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4038a-328">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="4038a-328">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="4038a-329">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="4038a-329">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4038a-330">其他资源</span><span class="sxs-lookup"><span data-stu-id="4038a-330">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4038a-331">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="4038a-331">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
