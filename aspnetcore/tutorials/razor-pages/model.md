---
title: 在 ASP.NET Core 中向 Razor Pages 应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: dcbcf37dfd95d784ebe249ec6e9e4184a8853d3d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187173"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="4cc72-103">在 ASP.NET Core 中向 Razor Pages 应用添加模型</span><span class="sxs-lookup"><span data-stu-id="4cc72-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="4cc72-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4cc72-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4cc72-105">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="4cc72-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="4cc72-106">这些类与 [Entity Framework Core](/ef/core)（EF Core）一起使用来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="4cc72-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4cc72-107">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="4cc72-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="4cc72-108">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="4cc72-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4cc72-109">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="4cc72-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="4cc72-110">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="4cc72-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cc72-112">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="4cc72-113">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-113">Name the folder *Models*.</span></span>

<span data-ttu-id="4cc72-114">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-114">Right click the *Models* folder.</span></span> <span data-ttu-id="4cc72-115">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="4cc72-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="4cc72-116">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cc72-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cc72-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4cc72-118">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="4cc72-119">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4cc72-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cc72-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4cc72-121">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="4cc72-122">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="4cc72-123">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="4cc72-124">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="4cc72-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="4cc72-125">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="4cc72-126">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="4cc72-127">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="4cc72-128">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="4cc72-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4cc72-129">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="4cc72-129">Scaffold the movie model</span></span>

<span data-ttu-id="4cc72-130">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="4cc72-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4cc72-131">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="4cc72-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cc72-133">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="4cc72-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="4cc72-134">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="4cc72-135">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="4cc72-135">Name the folder *Movies*</span></span>

<span data-ttu-id="4cc72-136">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="4cc72-138">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”    。</span><span class="sxs-lookup"><span data-stu-id="4cc72-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="4cc72-140">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="4cc72-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="4cc72-141">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="4cc72-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="4cc72-142">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models  .RazorPagesMovieContext 更改为 RazorPagesMovie.Data  .RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="4cc72-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="4cc72-143">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="4cc72-144">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="4cc72-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="4cc72-145">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-145">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="4cc72-147">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="4cc72-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cc72-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cc72-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4cc72-149">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4cc72-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4cc72-150">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4cc72-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4cc72-151">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cc72-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="4cc72-152">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cc72-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cc72-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4cc72-154">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4cc72-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4cc72-155">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4cc72-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4cc72-156">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cc72-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="4cc72-157">创建的文件</span><span class="sxs-lookup"><span data-stu-id="4cc72-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cc72-159">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="4cc72-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="4cc72-160">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="4cc72-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="4cc72-161">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="4cc72-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="4cc72-162">已更新</span><span class="sxs-lookup"><span data-stu-id="4cc72-162">Updated</span></span>

* <span data-ttu-id="4cc72-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4cc72-163">*Startup.cs*</span></span>

<span data-ttu-id="4cc72-164">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="4cc72-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4cc72-165">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4cc72-166">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="4cc72-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="4cc72-167">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="4cc72-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="4cc72-168">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="4cc72-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4cc72-169">初始迁移</span><span class="sxs-lookup"><span data-stu-id="4cc72-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cc72-171">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="4cc72-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="4cc72-172">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="4cc72-172">Add an initial migration.</span></span>
* <span data-ttu-id="4cc72-173">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="4cc72-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="4cc72-174">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="4cc72-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4cc72-176">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="4cc72-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cc72-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cc72-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cc72-178">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="4cc72-179">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="4cc72-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="4cc72-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="4cc72-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="4cc72-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="4cc72-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="4cc72-182">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="4cc72-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="4cc72-183">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4cc72-183">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4cc72-184">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-184">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4cc72-185">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4cc72-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="4cc72-186">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4cc72-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="4cc72-187">`ef database update` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-187">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="4cc72-188">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="4cc72-188">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4cc72-190">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="4cc72-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4cc72-191">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="4cc72-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4cc72-192">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="4cc72-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="4cc72-193">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="4cc72-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4cc72-194">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="4cc72-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="4cc72-195">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="4cc72-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="4cc72-196">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cc72-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4cc72-197">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="4cc72-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="4cc72-198">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="4cc72-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4cc72-199">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4cc72-200">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="4cc72-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="4cc72-201">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="4cc72-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4cc72-202">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="4cc72-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4cc72-203">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="4cc72-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4cc72-204">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="4cc72-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4cc72-205">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="4cc72-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cc72-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cc72-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4cc72-207">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cc72-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cc72-208">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4cc72-209">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cc72-209">Examine the `Up` method.</span></span>

---

<span data-ttu-id="4cc72-210">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4cc72-210">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4cc72-211">此架构的依据为 `RazorPagesMovieContext` 中指定的模型（在 Data/RazorPagesMovieContext.cs  文件中）。</span><span class="sxs-lookup"><span data-stu-id="4cc72-211">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4cc72-212">`Initial` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4cc72-212">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="4cc72-213">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4cc72-213">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="4cc72-214">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="4cc72-214">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="4cc72-215">`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-215">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4cc72-216">测试应用</span><span class="sxs-lookup"><span data-stu-id="4cc72-216">Test the app</span></span>

* <span data-ttu-id="4cc72-217">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-217">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4cc72-218">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="4cc72-218">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4cc72-219">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-219">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4cc72-220">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="4cc72-220">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="4cc72-222">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="4cc72-222">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4cc72-223">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="4cc72-223">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4cc72-224">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-224">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4cc72-225">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="4cc72-225">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="4cc72-226">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="4cc72-226">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4cc72-227">其他资源</span><span class="sxs-lookup"><span data-stu-id="4cc72-227">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4cc72-228">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="4cc72-228">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4cc72-229">在本节中，添加了用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="4cc72-229">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="4cc72-230">这些类与 [Entity Framework Core](/ef/core)（EF Core）一起使用来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="4cc72-230">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4cc72-231">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="4cc72-231">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="4cc72-232">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="4cc72-232">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4cc72-233">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="4cc72-233">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="4cc72-234">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="4cc72-234">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cc72-236">右键单击“RazorPagesMovie”  项目 >“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-236">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="4cc72-237">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-237">Name the folder *Models*.</span></span>

<span data-ttu-id="4cc72-238">右键单击“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-238">Right click the *Models* folder.</span></span> <span data-ttu-id="4cc72-239">选择“添加” > “类”   。</span><span class="sxs-lookup"><span data-stu-id="4cc72-239">Select **Add** > **Class**.</span></span> <span data-ttu-id="4cc72-240">将类命名“Movie”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-240">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cc72-241">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cc72-241">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4cc72-242">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-242">Add a folder named *Models*.</span></span>
* <span data-ttu-id="4cc72-243">将类添加到名为“Movie.cs”  的“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4cc72-243">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cc72-244">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-244">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4cc72-245">在解决方案资源管理器中，右键单击“RazorPagesMovie”  项目，然后选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-245">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="4cc72-246">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-246">Name the folder *Models*.</span></span>
* <span data-ttu-id="4cc72-247">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-247">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="4cc72-248">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="4cc72-248">In the **New File** dialog:</span></span>

  * <span data-ttu-id="4cc72-249">在左侧窗格中，选择“常规”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-249">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="4cc72-250">在中间窗格中，选择“空类”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-250">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="4cc72-251">将此类命名为“Movie”  ，然后选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-251">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="4cc72-252">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="4cc72-252">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4cc72-253">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="4cc72-253">Scaffold the movie model</span></span>

<span data-ttu-id="4cc72-254">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="4cc72-254">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4cc72-255">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="4cc72-255">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cc72-257">创建“Pages/Movies”文件夹  ：</span><span class="sxs-lookup"><span data-stu-id="4cc72-257">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="4cc72-258">右键单击“Pages”  文件夹 >“添加”  >“新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-258">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="4cc72-259">将文件夹命名为“Movies” </span><span class="sxs-lookup"><span data-stu-id="4cc72-259">Name the folder *Movies*</span></span>

<span data-ttu-id="4cc72-260">右键单击“Pages/Movies”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-260">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="4cc72-262">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”    。</span><span class="sxs-lookup"><span data-stu-id="4cc72-262">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="4cc72-264">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="4cc72-264">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="4cc72-265">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)   。</span><span class="sxs-lookup"><span data-stu-id="4cc72-265">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="4cc72-266">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”    。</span><span class="sxs-lookup"><span data-stu-id="4cc72-266">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="4cc72-267">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-267">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="4cc72-269">appsettings.json  文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="4cc72-269">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cc72-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cc72-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4cc72-271">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4cc72-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4cc72-272">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4cc72-272">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4cc72-273">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cc72-273">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="4cc72-274">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cc72-274">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cc72-275">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-275">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4cc72-276">打开项目目录（包含 Program.cs  、Startup.cs  和 .csproj  文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="4cc72-276">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4cc72-277">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="4cc72-277">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4cc72-278">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="4cc72-278">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="4cc72-279">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="4cc72-279">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="4cc72-280">创建的文件</span><span class="sxs-lookup"><span data-stu-id="4cc72-280">Files created</span></span>

* <span data-ttu-id="4cc72-281">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="4cc72-281">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="4cc72-282">Data/RazorPagesMovieContext.cs </span><span class="sxs-lookup"><span data-stu-id="4cc72-282">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="4cc72-283">文件已更新</span><span class="sxs-lookup"><span data-stu-id="4cc72-283">File updated</span></span>

* <span data-ttu-id="4cc72-284">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4cc72-284">*Startup.cs*</span></span>

<span data-ttu-id="4cc72-285">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="4cc72-285">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4cc72-286">初始迁移</span><span class="sxs-lookup"><span data-stu-id="4cc72-286">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-287">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4cc72-288">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="4cc72-288">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="4cc72-289">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="4cc72-289">Add an initial migration.</span></span>
* <span data-ttu-id="4cc72-290">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="4cc72-290">Update the database with the initial migration.</span></span>

<span data-ttu-id="4cc72-291">从“工具”菜单中，选择“NuGet 程序包管理器”>“包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="4cc72-291">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4cc72-293">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="4cc72-293">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cc72-294">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cc72-294">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cc72-295">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="4cc72-296">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="4cc72-296">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="4cc72-297">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="4cc72-297">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="4cc72-298">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="4cc72-298">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="4cc72-299">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="4cc72-299">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="4cc72-300">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4cc72-300">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4cc72-301">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-301">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4cc72-302">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4cc72-302">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="4cc72-303">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4cc72-303">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="4cc72-304">`ef database update` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-304">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="4cc72-305">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="4cc72-305">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4cc72-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4cc72-306">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4cc72-307">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="4cc72-307">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4cc72-308">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="4cc72-308">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4cc72-309">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="4cc72-309">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="4cc72-310">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="4cc72-310">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4cc72-311">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="4cc72-311">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="4cc72-312">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="4cc72-312">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="4cc72-313">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cc72-313">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4cc72-314">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="4cc72-314">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="4cc72-315">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="4cc72-315">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4cc72-316">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-316">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4cc72-317">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="4cc72-317">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="4cc72-318">前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="4cc72-318">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4cc72-319">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="4cc72-319">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4cc72-320">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="4cc72-320">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4cc72-321">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="4cc72-321">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4cc72-322">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="4cc72-322">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4cc72-323">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4cc72-323">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4cc72-324">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cc72-324">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4cc72-325">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4cc72-325">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4cc72-326">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="4cc72-326">Examine the `Up` method.</span></span>

---

<span data-ttu-id="4cc72-327">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="4cc72-327">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4cc72-328">此架构的依据为 `RazorPagesMovieContext` 中指定的模型（在 Data/RazorPagesMovieContext.cs  文件中）。</span><span class="sxs-lookup"><span data-stu-id="4cc72-328">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4cc72-329">`Initial` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="4cc72-329">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="4cc72-330">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="4cc72-330">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="4cc72-331">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="4cc72-331">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="4cc72-332">`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法  。</span><span class="sxs-lookup"><span data-stu-id="4cc72-332">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4cc72-333">测试应用</span><span class="sxs-lookup"><span data-stu-id="4cc72-333">Test the app</span></span>

* <span data-ttu-id="4cc72-334">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-334">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4cc72-335">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="4cc72-335">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4cc72-336">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-336">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4cc72-337">测试“创建”  链接。</span><span class="sxs-lookup"><span data-stu-id="4cc72-337">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="4cc72-339">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="4cc72-339">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4cc72-340">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="4cc72-340">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4cc72-341">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="4cc72-341">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4cc72-342">测试“编辑”  、“详细信息”  和“删除”  链接。</span><span class="sxs-lookup"><span data-stu-id="4cc72-342">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="4cc72-343">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="4cc72-343">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4cc72-344">其他资源</span><span class="sxs-lookup"><span data-stu-id="4cc72-344">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4cc72-345">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="4cc72-345">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
