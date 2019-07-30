---
title: 在 ASP.NET Core 中向 Razor Pages 应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
ms.date: 07/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: b7f77cfa51f8d86504939e31eade0dfda8a6b1c9
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371907"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="4cbc4-103">在 ASP.NET Core 中向 Razor Pages 应用添加模型</span><span class="sxs-lookup"><span data-stu-id="4cbc4-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="4cbc4-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4cbc4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4cbc4-105">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="4cbc4-106">这些类与 [Entity Framework Core](/ef/core)（EF Core）一起使用来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4cbc4-107">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="4cbc4-108">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4cbc4-109">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="4cbc4-110">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="4cbc4-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cbc4-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbc4-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cbc4-112">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="4cbc4-113">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-113">Name the folder *Models*.</span></span>

<span data-ttu-id="4cbc4-114">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-114">Right click the *Models* folder.</span></span> <span data-ttu-id="4cbc4-115">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="4cbc4-116">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cbc4-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cbc4-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4cbc4-118">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="4cbc4-119">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cbc4-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cbc4-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4cbc4-121">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="4cbc4-122">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="4cbc4-123">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="4cbc4-124">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="4cbc4-125">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="4cbc4-126">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="4cbc4-127">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="4cbc4-128">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4cbc4-129">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="4cbc4-129">Scaffold the movie model</span></span>

<span data-ttu-id="4cbc4-130">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4cbc4-131">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cbc4-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbc4-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cbc4-133">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="4cbc4-134">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="4cbc4-135">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="4cbc4-135">Name the folder *Movies*</span></span>

<span data-ttu-id="4cbc4-136">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="4cbc4-138">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”    。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="4cbc4-140">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="4cbc4-141">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="4cbc4-142">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models  .RazorPagesMovieContext 更改为 RazorPagesMovie.Data  .RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="4cbc4-143">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="4cbc4-144">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="4cbc4-145">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-145">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="4cbc4-147">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cbc4-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cbc4-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4cbc4-149">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4cbc4-150">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-150">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4cbc4-151">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-151">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="4cbc4-152">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-152">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cbc4-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cbc4-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4cbc4-154">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4cbc4-155">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-155">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4cbc4-156">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="4cbc4-157">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-157">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="4cbc4-158">创建的文件</span><span class="sxs-lookup"><span data-stu-id="4cbc4-158">Files created</span></span>

* <span data-ttu-id="4cbc4-159">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-159">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="4cbc4-160">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="4cbc4-160">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="4cbc4-161">文件已更新</span><span class="sxs-lookup"><span data-stu-id="4cbc4-161">File updated</span></span>

* <span data-ttu-id="4cbc4-162">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4cbc4-162">*Startup.cs*</span></span>

<span data-ttu-id="4cbc4-163">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-163">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4cbc4-164">初始迁移</span><span class="sxs-lookup"><span data-stu-id="4cbc4-164">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cbc4-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbc4-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cbc4-166">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-166">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="4cbc4-167">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-167">Add an initial migration.</span></span>
* <span data-ttu-id="4cbc4-168">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-168">Update the database with the initial migration.</span></span>

<span data-ttu-id="4cbc4-169">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-169">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4cbc4-171">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-171">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cbc4-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cbc4-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cbc4-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cbc4-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="4cbc4-174">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="4cbc4-174">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="4cbc4-175">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="4cbc4-175">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="4cbc4-176">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="4cbc4-176">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="4cbc4-177">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-177">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="4cbc4-178">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-178">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4cbc4-179">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-179">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4cbc4-180">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-180">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="4cbc4-181">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-181">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="4cbc4-182">`ef database update` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-182">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="4cbc4-183">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-183">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cbc4-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbc4-184">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4cbc4-185">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="4cbc4-185">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4cbc4-186">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-186">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4cbc4-187">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-187">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="4cbc4-188">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-188">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4cbc4-189">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-189">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="4cbc4-190">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-190">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="4cbc4-191">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-191">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4cbc4-192">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-192">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="4cbc4-193">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-193">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4cbc4-194">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-194">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4cbc4-195">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-195">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="4cbc4-196">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-196">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4cbc4-197">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-197">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4cbc4-198">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-198">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4cbc4-199">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-199">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4cbc4-200">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-200">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cbc4-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cbc4-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4cbc4-202">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-202">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cbc4-203">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cbc4-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4cbc4-204">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-204">Examine the `Up` method.</span></span>

---

<span data-ttu-id="4cbc4-205">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-205">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4cbc4-206">此架构的依据为 `RazorPagesMovieContext` 中指定的模型（在 Data/RazorPagesMovieContext.cs  文件中）。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-206">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4cbc4-207">`Initial` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-207">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="4cbc4-208">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-208">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="4cbc4-209">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-209">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="4cbc4-210">`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-210">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4cbc4-211">测试应用</span><span class="sxs-lookup"><span data-stu-id="4cbc4-211">Test the app</span></span>

* <span data-ttu-id="4cbc4-212">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4cbc4-213">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4cbc4-214">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4cbc4-215">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-215">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="4cbc4-217">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4cbc4-218">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4cbc4-219">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4cbc4-220">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="4cbc4-221">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4cbc4-222">其他资源</span><span class="sxs-lookup"><span data-stu-id="4cbc4-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4cbc4-223">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="4cbc4-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4cbc4-224">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-224">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="4cbc4-225">这些类与 [Entity Framework Core](/ef/core)（EF Core）一起使用来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-225">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4cbc4-226">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-226">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="4cbc4-227">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-227">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4cbc4-228">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-228">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="4cbc4-229">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="4cbc4-229">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cbc4-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbc4-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cbc4-231">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-231">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="4cbc4-232">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-232">Name the folder *Models*.</span></span>

<span data-ttu-id="4cbc4-233">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-233">Right click the *Models* folder.</span></span> <span data-ttu-id="4cbc4-234">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-234">Select **Add** > **Class**.</span></span> <span data-ttu-id="4cbc4-235">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-235">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cbc4-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cbc4-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4cbc4-237">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-237">Add a folder named *Models*.</span></span>
* <span data-ttu-id="4cbc4-238">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-238">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cbc4-239">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cbc4-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4cbc4-240">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-240">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="4cbc4-241">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-241">Name the folder *Models*.</span></span>
* <span data-ttu-id="4cbc4-242">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-242">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="4cbc4-243">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-243">In the **New File** dialog:</span></span>

  * <span data-ttu-id="4cbc4-244">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-244">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="4cbc4-245">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-245">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="4cbc4-246">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-246">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="4cbc4-247">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-247">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4cbc4-248">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="4cbc4-248">Scaffold the movie model</span></span>

<span data-ttu-id="4cbc4-249">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-249">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4cbc4-250">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-250">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cbc4-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbc4-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cbc4-252">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-252">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="4cbc4-253">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-253">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="4cbc4-254">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="4cbc4-254">Name the folder *Movies*</span></span>

<span data-ttu-id="4cbc4-255">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-255">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="4cbc4-257">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”    。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-257">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="4cbc4-259">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-259">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="4cbc4-260">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-260">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="4cbc4-261">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”    。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-261">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="4cbc4-262">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-262">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="4cbc4-264">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-264">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cbc4-265">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cbc4-265">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4cbc4-266">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-266">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4cbc4-267">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-267">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4cbc4-268">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-268">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="4cbc4-269">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-269">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cbc4-270">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cbc4-270">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4cbc4-271">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4cbc4-272">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-272">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4cbc4-273">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-273">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="4cbc4-274">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-274">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="4cbc4-275">创建的文件</span><span class="sxs-lookup"><span data-stu-id="4cbc4-275">Files created</span></span>

* <span data-ttu-id="4cbc4-276">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-276">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="4cbc4-277">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="4cbc4-277">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="4cbc4-278">文件已更新</span><span class="sxs-lookup"><span data-stu-id="4cbc4-278">File updated</span></span>

* <span data-ttu-id="4cbc4-279">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4cbc4-279">*Startup.cs*</span></span>

<span data-ttu-id="4cbc4-280">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-280">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4cbc4-281">初始迁移</span><span class="sxs-lookup"><span data-stu-id="4cbc4-281">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cbc4-282">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbc4-282">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cbc4-283">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-283">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="4cbc4-284">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-284">Add an initial migration.</span></span>
* <span data-ttu-id="4cbc4-285">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-285">Update the database with the initial migration.</span></span>

<span data-ttu-id="4cbc4-286">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-286">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4cbc4-288">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-288">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cbc4-289">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cbc4-289">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cbc4-290">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cbc4-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="4cbc4-291">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="4cbc4-291">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="4cbc4-292">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="4cbc4-292">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="4cbc4-293">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="4cbc4-293">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="4cbc4-294">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-294">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="4cbc4-295">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-295">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4cbc4-296">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-296">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4cbc4-297">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-297">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="4cbc4-298">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-298">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="4cbc4-299">`ef database update` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-299">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="4cbc4-300">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-300">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cbc4-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cbc4-301">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4cbc4-302">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="4cbc4-302">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4cbc4-303">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-303">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4cbc4-304">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-304">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="4cbc4-305">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-305">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4cbc4-306">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-306">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="4cbc4-307">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-307">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="4cbc4-308">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-308">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4cbc4-309">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-309">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="4cbc4-310">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-310">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4cbc4-311">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-311">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4cbc4-312">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-312">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="4cbc4-313">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-313">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4cbc4-314">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-314">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4cbc4-315">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-315">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4cbc4-316">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-316">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4cbc4-317">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-317">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cbc4-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cbc4-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4cbc4-319">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-319">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cbc4-320">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cbc4-320">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4cbc4-321">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-321">Examine the `Up` method.</span></span>

---

<span data-ttu-id="4cbc4-322">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-322">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4cbc4-323">此架构的依据为 `RazorPagesMovieContext` 中指定的模型（在 Data/RazorPagesMovieContext.cs  文件中）。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-323">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4cbc4-324">`Initial` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-324">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="4cbc4-325">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-325">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="4cbc4-326">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-326">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="4cbc4-327">`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-327">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4cbc4-328">测试应用</span><span class="sxs-lookup"><span data-stu-id="4cbc4-328">Test the app</span></span>

* <span data-ttu-id="4cbc4-329">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-329">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4cbc4-330">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="4cbc4-330">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4cbc4-331">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-331">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4cbc4-332">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-332">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="4cbc4-334">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-334">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4cbc4-335">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-335">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4cbc4-336">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-336">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4cbc4-337">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-337">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="4cbc4-338">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="4cbc4-338">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4cbc4-339">其他资源</span><span class="sxs-lookup"><span data-stu-id="4cbc4-339">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4cbc4-340">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="4cbc4-340">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end