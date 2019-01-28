---
title: 在 ASP.NET Core 中向 Razor 页面应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
monikerRange: '>= aspnetcore-2.2'
ms.date: 12/3/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: 5693fb833d19375ef53e0f367bc6064e239b01d3
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444099"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="c81e8-103">在 ASP.NET Core 中向 Razor 页面应用添加模型</span><span class="sxs-lookup"><span data-stu-id="c81e8-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="c81e8-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c81e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c81e8-105">在此部分中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="c81e8-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="c81e8-106">可以结合 [Entity Framework Core](/ef/core) (EF Core) 使用这些类来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="c81e8-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="c81e8-107">EF Core 是对象关系映射 (ORM) 框架，可以简化数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="c81e8-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="c81e8-108">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="c81e8-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="c81e8-109">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="c81e8-109">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="c81e8-110">[查看或下载](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)示例。</span><span class="sxs-lookup"><span data-stu-id="c81e8-110">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) sample.</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="c81e8-111">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="c81e8-111">Add a data model</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c81e8-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c81e8-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c81e8-113">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="c81e8-114">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-114">Name the folder *Models*.</span></span>

<span data-ttu-id="c81e8-115">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="c81e8-115">Right click the *Models* folder.</span></span> <span data-ttu-id="c81e8-116">选择“添加” > “类”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="c81e8-117">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c81e8-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c81e8-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c81e8-119">添加名为“模型”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c81e8-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="c81e8-120">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="c81e8-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c81e8-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c81e8-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c81e8-122">在解决方案资源管理器中，右键单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="c81e8-123">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="c81e8-124">右键单击“Models”文件夹，然后选择“添加” > “新建文件”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="c81e8-125">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="c81e8-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="c81e8-126">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="c81e8-127">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-127">Select **Empty Class** in the center pain.</span></span>
  * <span data-ttu-id="c81e8-128">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- End of VS tabs -->

---

<span data-ttu-id="c81e8-129">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="c81e8-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="c81e8-130">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="c81e8-130">Scaffold the movie model</span></span>

<span data-ttu-id="c81e8-131">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="c81e8-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="c81e8-132">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="c81e8-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c81e8-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c81e8-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c81e8-134">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="c81e8-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="c81e8-135">右键单击 Pages 文件夹 >“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="c81e8-136">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="c81e8-136">Name the folder *Movies*</span></span>

<span data-ttu-id="c81e8-137">右键单击 Pages/Movies 文件夹 >“添加” > “新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="c81e8-139">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)” > “添加”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="c81e8-141">完成“使用实体框架(CRUD)添加 Razor Pages”对话框：</span><span class="sxs-lookup"><span data-stu-id="c81e8-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="c81e8-142">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)。</span><span class="sxs-lookup"><span data-stu-id="c81e8-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="c81e8-143">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-143">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="c81e8-144">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-144">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="c81e8-146">appsettings.json 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="c81e8-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c81e8-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c81e8-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="c81e8-148">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="c81e8-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="c81e8-149">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="c81e8-149">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="c81e8-150">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="c81e8-150">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="c81e8-151">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="c81e8-151">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c81e8-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c81e8-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c81e8-153">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="c81e8-153">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="c81e8-154">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="c81e8-154">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```
* <span data-ttu-id="c81e8-155">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="c81e8-155">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="c81e8-156">前面的命令生成以下警告：“未对实体类型‘Movie’上的十进制列‘Price’指定任何类型。</span><span class="sxs-lookup"><span data-stu-id="c81e8-156">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="c81e8-157">当值不符合默认精确度和小数位数时，会在无提示的情况下截断该值。</span><span class="sxs-lookup"><span data-stu-id="c81e8-157">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="c81e8-158">使用‘HasColumnType()’显式指定可以容纳所有值的 SQL Server 列类型。”</span><span class="sxs-lookup"><span data-stu-id="c81e8-158">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="c81e8-159">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="c81e8-159">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="c81e8-160">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="c81e8-160">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="c81e8-161">创建的文件</span><span class="sxs-lookup"><span data-stu-id="c81e8-161">Files created</span></span>

* <span data-ttu-id="c81e8-162">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-162">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="c81e8-163">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="c81e8-163">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="c81e8-164">文件已更新</span><span class="sxs-lookup"><span data-stu-id="c81e8-164">File updated</span></span>

* <span data-ttu-id="c81e8-165">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="c81e8-165">*Startup.cs*</span></span>

<span data-ttu-id="c81e8-166">下一部分中说明了创建和更新的文件。</span><span class="sxs-lookup"><span data-stu-id="c81e8-166">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="c81e8-167">初始迁移</span><span class="sxs-lookup"><span data-stu-id="c81e8-167">Initial migration</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c81e8-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c81e8-168">Visual Studio</span></span>](#tab/visual-studio)

<!-- VS -------------------------->

<span data-ttu-id="c81e8-169">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="c81e8-169">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="c81e8-170">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="c81e8-170">Add an initial migration.</span></span>
* <span data-ttu-id="c81e8-171">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="c81e8-171">Update the database with the initial migration.</span></span>

<span data-ttu-id="c81e8-172">从“工具”菜单中，选择“NuGet 包管理器” > “包管理器控制台”。</span><span class="sxs-lookup"><span data-stu-id="c81e8-172">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="c81e8-174">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="c81e8-174">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c81e8-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c81e8-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c81e8-176">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c81e8-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="c81e8-177">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="c81e8-177">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="c81e8-178">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="c81e8-178">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="c81e8-179">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="c81e8-179">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="c81e8-180">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="c81e8-180">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="c81e8-181">`ef database update` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="c81e8-181">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="c81e8-182">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="c81e8-182">The `Up` method creates the database.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c81e8-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c81e8-183">Visual Studio</span></span>](#tab/visual-studio)

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="c81e8-184">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="c81e8-184">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="c81e8-185">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="c81e8-185">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c81e8-186">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="c81e8-186">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="c81e8-187">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="c81e8-187">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c81e8-188">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="c81e8-188">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="c81e8-189">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="c81e8-189">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="c81e8-190">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="c81e8-190">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c81e8-191">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="c81e8-191">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="c81e8-192">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="c81e8-192">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="c81e8-193">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="c81e8-193">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="c81e8-194">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="c81e8-194">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="c81e8-195">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="c81e8-195">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="c81e8-196">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="c81e8-196">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="c81e8-197">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="c81e8-197">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c81e8-198">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="c81e8-198">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c81e8-199">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c81e8-199">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>
<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c81e8-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c81e8-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c81e8-201">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c81e8-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- End of VS tabs -->

---

<span data-ttu-id="c81e8-202">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="c81e8-202">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="c81e8-203">此架构的依据为 `RazorPagesMovieContext` 中指定的模型（在 Data/RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="c81e8-203">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="c81e8-204">`Initial` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="c81e8-204">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="c81e8-205">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="c81e8-205">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="c81e8-206">有关详细信息，请参阅[迁移简介](xref:data/ef-mvc/migrations#introduction-to-migrations)。</span><span class="sxs-lookup"><span data-stu-id="c81e8-206">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="c81e8-207">`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="c81e8-207">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="c81e8-208">测试应用</span><span class="sxs-lookup"><span data-stu-id="c81e8-208">Test the app</span></span>

* <span data-ttu-id="c81e8-209">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="c81e8-209">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="c81e8-210">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="c81e8-210">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="c81e8-211">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="c81e8-211">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="c81e8-212">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="c81e8-212">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)
  
  > [!NOTE]
  > <span data-ttu-id="c81e8-214">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="c81e8-214">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="c81e8-215">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="c81e8-215">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="c81e8-216">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="c81e8-216">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="c81e8-217">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="c81e8-217">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="c81e8-218">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="c81e8-218">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c81e8-219">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="c81e8-219">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>
