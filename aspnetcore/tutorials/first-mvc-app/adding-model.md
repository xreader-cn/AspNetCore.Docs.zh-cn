---
title: 将模型添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: 将模型添加到简单的 ASP.NET Core 应用。
ms.author: riande
ms.date: 02/25/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 08d8e9679bfee11f03e61cb4b9ae9b5b36186049
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012820"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="3291f-103">将模型添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="3291f-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="3291f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3291f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3291f-105">在本部分中将添加用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="3291f-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="3291f-106">这些类将是 MVC 应用的“Model”部分。</span><span class="sxs-lookup"><span data-stu-id="3291f-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="3291f-107">可以结合 [Entity Framework Core](/ef/core) (EF Core) 使用这些类来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="3291f-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="3291f-108">EF Core 是对象关系映射 (ORM) 框架，可以简化需要编写的数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="3291f-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="3291f-109">要创建的模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3291f-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3291f-110">它们只定义将存储在数据库中的数据的属性。</span><span class="sxs-lookup"><span data-stu-id="3291f-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="3291f-111">在本教程中，首先要编写模型类，然后 EF Core 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="3291f-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="3291f-112">有一种备选方法（此处未介绍）：从现有数据库生成模型类。</span><span class="sxs-lookup"><span data-stu-id="3291f-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="3291f-113">有关此方法的信息，请参阅 [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db)（ASP.NET Core - 现有数据库）。</span><span class="sxs-lookup"><span data-stu-id="3291f-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

## <a name="add-a-data-model-class"></a><span data-ttu-id="3291f-114">添加数据模型类</span><span class="sxs-lookup"><span data-stu-id="3291f-114">Add a data model class</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="3291f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3291f-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3291f-116">右键单击 Models 文件夹，然后单击“添加” > “类”。</span><span class="sxs-lookup"><span data-stu-id="3291f-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="3291f-117">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="3291f-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# [<a name="visual-studio-code--visual-studio-for-mac"></a><span data-ttu-id="3291f-118">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3291f-118">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3291f-119">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="3291f-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3291f-120">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="3291f-120">Scaffold the movie model</span></span>

<span data-ttu-id="3291f-121">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="3291f-121">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3291f-122">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="3291f-122">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="3291f-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3291f-123">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3291f-124">在解决方案资源管理器中，右键单击“Controllers”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="3291f-124">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上述步骤的视图](adding-model/_static/add_controller21.png)

<span data-ttu-id="3291f-126">在“添加基架”对话框中，选择“包含视图的 MVC 控制器(使用 Entity Framework)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="3291f-126">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![“添加基架”对话框](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="3291f-128">填写“添加控制器”对话框：</span><span class="sxs-lookup"><span data-stu-id="3291f-128">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="3291f-129">模型类：Movie (MvcMovie.Models)</span><span class="sxs-lookup"><span data-stu-id="3291f-129">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="3291f-130">数据上下文类：选择 + 图标并添加默认的 MvcMovie.Models.MvcMovieContext</span><span class="sxs-lookup"><span data-stu-id="3291f-130">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![“添加数据”上下文](adding-model/_static/dc.png)

* <span data-ttu-id="3291f-132">视图：将每个选项保持为默认选中状态</span><span class="sxs-lookup"><span data-stu-id="3291f-132">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="3291f-133">控制器名称：保留默认的 MoviesController</span><span class="sxs-lookup"><span data-stu-id="3291f-133">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="3291f-134">选择“添加”</span><span class="sxs-lookup"><span data-stu-id="3291f-134">Select **Add**</span></span>

![“添加控制器”对话框](adding-model/_static/add_controller2.png)

<span data-ttu-id="3291f-136">Visual Studio 将创建：</span><span class="sxs-lookup"><span data-stu-id="3291f-136">Visual Studio creates:</span></span>

* <span data-ttu-id="3291f-137">Entity Framework Core [数据库上下文类](xref:data/ef-mvc/intro#create-the-database-context) (Data/MvcMovieContext.cs)</span><span class="sxs-lookup"><span data-stu-id="3291f-137">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="3291f-138">电影控制器 (Controllers/MoviesController.cs)</span><span class="sxs-lookup"><span data-stu-id="3291f-138">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="3291f-139">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="3291f-139">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="3291f-140">自动创建数据库上下文和 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)（创建、读取、更新和删除）操作方法和视图的过程称为“搭建基架”。</span><span class="sxs-lookup"><span data-stu-id="3291f-140">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# [<a name="visual-studio-code"></a><span data-ttu-id="3291f-141">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3291f-141">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3291f-142">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="3291f-142">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="3291f-143">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="3291f-143">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="3291f-144">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="3291f-144">Run the following command:</span></span>

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# [<a name="visual-studio-for-mac"></a><span data-ttu-id="3291f-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3291f-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3291f-146">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="3291f-146">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="3291f-147">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="3291f-147">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="3291f-148">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="3291f-148">Run the following command:</span></span>

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="3291f-149">如果运行应用并单击“Mvc 电影”链接，则会出现以下类似的错误：</span><span class="sxs-lookup"><span data-stu-id="3291f-149">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

<span data-ttu-id="3291f-150">你需要创建数据库，并且使用 EF Core [迁移](xref:data/ef-mvc/migrations)功能来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="3291f-150">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="3291f-151">通过迁移可创建与数据模型匹配的数据库，并在数据模型更改时更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="3291f-151">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3291f-152">初始迁移</span><span class="sxs-lookup"><span data-stu-id="3291f-152">Initial migration</span></span>

<span data-ttu-id="3291f-153">在本节中，将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="3291f-153">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="3291f-154">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="3291f-154">Add an initial migration.</span></span>
* <span data-ttu-id="3291f-155">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="3291f-155">Update the database with the initial migration.</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="3291f-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3291f-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3291f-157">从“工具”菜单中，选择“NuGet 包管理器” > “包管理器控制台”(PMC)。</span><span class="sxs-lookup"><span data-stu-id="3291f-157">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC 菜单](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="3291f-159">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="3291f-159">In the PMC, enter the following commands:</span></span>

   ```console
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="3291f-160">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="3291f-160">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="3291f-161">数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="3291f-161">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="3291f-162">`Initial` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="3291f-162">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="3291f-163">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="3291f-163">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="3291f-164">有关更多信息，请参见<xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="3291f-164">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="3291f-165">`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="3291f-165">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# [<a name="visual-studio-code--visual-studio-for-mac"></a><span data-ttu-id="3291f-166">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3291f-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="3291f-167">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="3291f-167">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="3291f-168">数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="3291f-168">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="3291f-169">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="3291f-169">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="3291f-170">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="3291f-170">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3291f-171">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="3291f-171">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3291f-172">ASP.NET Core 通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 生成。</span><span class="sxs-lookup"><span data-stu-id="3291f-172">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3291f-173">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过 DI 注册。</span><span class="sxs-lookup"><span data-stu-id="3291f-173">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="3291f-174">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="3291f-174">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3291f-175">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="3291f-175">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="3291f-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3291f-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3291f-177">基架工具自动创建数据库上下文并将其注册到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="3291f-177">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="3291f-178">我们来研究以下 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="3291f-178">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3291f-179">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="3291f-179">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="3291f-180">`MvcMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="3291f-180">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="3291f-181">数据上下文 (`MvcMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="3291f-181">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3291f-182">数据上下文指定数据模型中包含哪些实体：</span><span class="sxs-lookup"><span data-stu-id="3291f-182">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="3291f-183">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="3291f-183">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3291f-184">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="3291f-184">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3291f-185">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="3291f-185">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3291f-186">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="3291f-186">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3291f-187">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="3291f-187">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# [<a name="visual-studio-code--visual-studio-for-mac"></a><span data-ttu-id="3291f-188">Visual Studio Code/Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3291f-188">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3291f-189">创建了数据库上下文并将其注册到了 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="3291f-189">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3291f-190">测试应用</span><span class="sxs-lookup"><span data-stu-id="3291f-190">Test the app</span></span>

* <span data-ttu-id="3291f-191">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="3291f-191">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3291f-192">如果收到如下所示数据库异常：</span><span class="sxs-lookup"><span data-stu-id="3291f-192">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3291f-193">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="3291f-193">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3291f-194">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="3291f-194">Test the **Create** link.</span></span> <span data-ttu-id="3291f-195">输入并提交数据。</span><span class="sxs-lookup"><span data-stu-id="3291f-195">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="3291f-196">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="3291f-196">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3291f-197">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="3291f-197">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3291f-198">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="3291f-198">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3291f-199">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="3291f-199">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="3291f-200">检查 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="3291f-200">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="3291f-201">上面突出显示的代码显示了要添加到[依赖关系注入](xref:fundamentals/dependency-injection)容器的电影数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="3291f-201">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* `services.AddDbContext<MvcMovieContext>(options =>` <span data-ttu-id="3291f-202">指定要使用的数据库和连接字符串。</span><span class="sxs-lookup"><span data-stu-id="3291f-202">specifies the database to use and the connection string.</span></span>
* `=>` <span data-ttu-id="3291f-203">是 [lambda 运算符](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="3291f-203">is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="3291f-204">打开 Controllers/MoviesController.cs 文件并检查构造函数：</span><span class="sxs-lookup"><span data-stu-id="3291f-204">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="3291f-205">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="3291f-205">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="3291f-206">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="3291f-206">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="3291f-207">强类型模型和 @model 关键词</span><span class="sxs-lookup"><span data-stu-id="3291f-207">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="3291f-208">在本教程之前的内容中，已经介绍了控制器如何使用 `ViewData` 字典将数据或对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="3291f-208">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="3291f-209">`ViewData` 字典是一个动态对象，提供了将信息传递给视图的方便的后期绑定方法。</span><span class="sxs-lookup"><span data-stu-id="3291f-209">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="3291f-210">MVC 还提供将强类型模型对象传递给视图的功能。</span><span class="sxs-lookup"><span data-stu-id="3291f-210">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="3291f-211">凭借此强类型方法可更好地对代码进行编译时检查。</span><span class="sxs-lookup"><span data-stu-id="3291f-211">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="3291f-212">基架机制在创建方法和视图时，通过 `MoviesController` 类和视图使用了此方法（即传递强类型模型）。</span><span class="sxs-lookup"><span data-stu-id="3291f-212">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="3291f-213">检查 Controllers/MoviesController.cs 文件中生成的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="3291f-213">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="3291f-214">`id` 参数通常作为路由数据传递。</span><span class="sxs-lookup"><span data-stu-id="3291f-214">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="3291f-215">例如 `https://localhost:5001/movies/details/1` 的设置如下：</span><span class="sxs-lookup"><span data-stu-id="3291f-215">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="3291f-216">控制器被设置为 `movies` 控制器（第一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="3291f-216">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="3291f-217">操作被设置为 `details`（第二个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="3291f-217">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="3291f-218">ID 被设置为 1（最后一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="3291f-218">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="3291f-219">还可以使用查询字符串传入 `id`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="3291f-219">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="3291f-220">在未提供 ID 值的情况下，`id` 参数可定义为[可以为 null 的类型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`)。</span><span class="sxs-lookup"><span data-stu-id="3291f-220">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="3291f-221">[Lambda 表达式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)会被传入 `FirstOrDefaultAsync` 以选择与路由数据或查询字符串值相匹配的电影实体。</span><span class="sxs-lookup"><span data-stu-id="3291f-221">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="3291f-222">如果找到了电影，`Movie` 模型的实例则会被传递到 `Details` 视图：</span><span class="sxs-lookup"><span data-stu-id="3291f-222">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="3291f-223">检查 Views/Movies/Details.cshtml 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="3291f-223">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="3291f-224">通过将 `@model` 语句包括在视图文件的顶端，可以指定视图期望的对象类型。</span><span class="sxs-lookup"><span data-stu-id="3291f-224">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="3291f-225">创建电影控制器时，会自动在 Details.cshtml 文件的顶端包括以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="3291f-225">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="3291f-226">此 `@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影。</span><span class="sxs-lookup"><span data-stu-id="3291f-226">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="3291f-227">例如，在 Details.cshtml 视图中，代码通过强类型的 `Model` 对象将每个电影字段传递给 `DisplayNameFor` 和 `DisplayFor`HTML 帮助程序。</span><span class="sxs-lookup"><span data-stu-id="3291f-227">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="3291f-228">`Create` 和 `Edit` 方法以及视图也传递一个 `Movie` 模型对象。</span><span class="sxs-lookup"><span data-stu-id="3291f-228">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="3291f-229">检查电影控制器中的 Index.cshtml 视图和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="3291f-229">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="3291f-230">请注意代码在调用 `View` 方法时是如何创建 `List` 对象的。</span><span class="sxs-lookup"><span data-stu-id="3291f-230">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="3291f-231">代码将此 `Movies` 列表从 `Index` 操作方法传递给视图：</span><span class="sxs-lookup"><span data-stu-id="3291f-231">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="3291f-232">创建电影控制器时，基架会自动在 Index.cshtml 文件的顶端包含以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="3291f-232">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="3291f-233">`@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影列表。</span><span class="sxs-lookup"><span data-stu-id="3291f-233">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="3291f-234">例如，在 Index.cshtml 视图中，代码使用 `foreach` 语句通过强类型 `Model` 对象对电影进行循环遍历：</span><span class="sxs-lookup"><span data-stu-id="3291f-234">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="3291f-235">因为 `Model` 对象为强类型（作为 `IEnumerable<Movie>` 对象），因此循环中的每个项都被类型化为 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="3291f-235">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="3291f-236">除其他优点之外，这意味着可对代码进行编译时检查：</span><span class="sxs-lookup"><span data-stu-id="3291f-236">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3291f-237">其他资源</span><span class="sxs-lookup"><span data-stu-id="3291f-237">Additional resources</span></span>

* [<span data-ttu-id="3291f-238">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="3291f-238">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="3291f-239">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="3291f-239">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="3291f-240">[上一篇：添加视图](adding-view.md)
> [下一篇：使用 SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="3291f-240">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>
