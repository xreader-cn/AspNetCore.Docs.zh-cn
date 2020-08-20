---
title: 第 4 部分，将模型添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: ASP.NET Core MVC 教程系列的第 4 部分。
ms.author: riande
ms.date: 01/13/2020
no-loc:
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: ddd517ef8fbf8cb4bb8765cb3caab4724c0205f0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631961"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="65e3e-103">第 4 部分，将模型添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="65e3e-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="65e3e-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="65e3e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="65e3e-105">在本部分中将添加用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="65e3e-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="65e3e-106">这些类将是 MVC 应用的“Model”部分 。</span><span class="sxs-lookup"><span data-stu-id="65e3e-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="65e3e-107">可以结合 [Entity Framework Core](/ef/core) (EF Core) 使用这些类来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="65e3e-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="65e3e-108">EF Core 是对象关系映射 (ORM) 框架，可以简化需要编写的数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="65e3e-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="65e3e-109">要创建的模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系   。</span><span class="sxs-lookup"><span data-stu-id="65e3e-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="65e3e-110">它们只定义将存储在数据库中的数据的属性。</span><span class="sxs-lookup"><span data-stu-id="65e3e-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="65e3e-111">在本教程中，首先要编写模型类，然后 EF Core 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="65e3e-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="65e3e-112">添加数据模型类</span><span class="sxs-lookup"><span data-stu-id="65e3e-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65e3e-114">右键单击 Models 文件夹，然后单击“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="65e3e-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="65e3e-115">将文件命名为 Movie.cs。</span><span class="sxs-lookup"><span data-stu-id="65e3e-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="65e3e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65e3e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="65e3e-117">将名为 Movie.cs 的文件添加到“Models”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="65e3e-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="65e3e-119">右键单击 Models 文件夹，然后单击“添加” > “新类” > “空类”。  </span><span class="sxs-lookup"><span data-stu-id="65e3e-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="65e3e-120">将文件命名为 Movie.cs。</span><span class="sxs-lookup"><span data-stu-id="65e3e-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="65e3e-121">使用以下代码更新 Movie.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="65e3e-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="65e3e-122">`Movie` 类包含一个 `Id` 字段，数据需要该字段作为主键。</span><span class="sxs-lookup"><span data-stu-id="65e3e-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="65e3e-123">`ReleaseDate` 上的 <xref:System.ComponentModel.DataAnnotations.DataType> 特性指定了数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="65e3e-124">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="65e3e-124">With this attribute:</span></span>

* <span data-ttu-id="65e3e-125">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="65e3e-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="65e3e-126">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="65e3e-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="65e3e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="65e3e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="65e3e-128">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="65e3e-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65e3e-130">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”(PMC)。 </span><span class="sxs-lookup"><span data-stu-id="65e3e-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 菜单](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="65e3e-132">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="65e3e-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="65e3e-133">前面的命令添加 EF Core SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="65e3e-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="65e3e-134">提供程序包将 EF Core 包作为依赖项进行安装。</span><span class="sxs-lookup"><span data-stu-id="65e3e-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="65e3e-135">在本教程后面的基架步骤中会自动安装其他包。</span><span class="sxs-lookup"><span data-stu-id="65e3e-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="65e3e-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65e3e-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="65e3e-138">从“项目”菜单中选择“管理 NuGet 程序包” 。</span><span class="sxs-lookup"><span data-stu-id="65e3e-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="65e3e-139">在右上方的“搜索”字段中，输入 `Microsoft.EntityFrameworkCore.SQLite` 并按回车键进行搜索。 </span><span class="sxs-lookup"><span data-stu-id="65e3e-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="65e3e-140">搜索匹配的 NuGet 程序包，并按“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="65e3e-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![添加 Entity Framework Core NuGet 包](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="65e3e-142">“选择项目”对话框将显示，并已选中 `MvcMovie` 项目。</span><span class="sxs-lookup"><span data-stu-id="65e3e-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="65e3e-143">按“确认”按钮。</span><span class="sxs-lookup"><span data-stu-id="65e3e-143">Press the **Ok** button.</span></span>

<span data-ttu-id="65e3e-144">“接受许可证”对话框将显示。</span><span class="sxs-lookup"><span data-stu-id="65e3e-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="65e3e-145">根据需要查看许可证，然后单击“接受”按钮。</span><span class="sxs-lookup"><span data-stu-id="65e3e-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="65e3e-146">重复上面的步骤，以安装以下 NuGet 程序包：</span><span class="sxs-lookup"><span data-stu-id="65e3e-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="65e3e-147">创建数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="65e3e-147">Create a database context class</span></span>

<span data-ttu-id="65e3e-148">需要一个数据库上下文类来协调 `Movie` 模型的 EF Core 功能（创建、读取、更新和删除）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="65e3e-149">数据库上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 并指定要包含在数据模型中的实体。</span><span class="sxs-lookup"><span data-stu-id="65e3e-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="65e3e-150">创建一个“Data”文件夹。</span><span class="sxs-lookup"><span data-stu-id="65e3e-150">Create a *Data* folder.</span></span>

<span data-ttu-id="65e3e-151">使用以下代码添加 Data/MvcMovieContext.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="65e3e-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="65e3e-152">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="65e3e-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="65e3e-153">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="65e3e-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="65e3e-154">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="65e3e-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="65e3e-155">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="65e3e-155">Register the database context</span></span>

<span data-ttu-id="65e3e-156">ASP.NET Core 通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 生成。</span><span class="sxs-lookup"><span data-stu-id="65e3e-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="65e3e-157">在应用程序启动过程中，必须向 DI 注册服务（如 EF Core DB 上下文）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="65e3e-158">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="65e3e-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="65e3e-159">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="65e3e-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="65e3e-160">本部分会将数据库上下文注册到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="65e3e-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="65e3e-161">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="65e3e-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="65e3e-162">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="65e3e-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-164">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="65e3e-165">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="65e3e-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="65e3e-166">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="65e3e-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="65e3e-167">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="65e3e-167">Add a database connection string</span></span>

<span data-ttu-id="65e3e-168">将连接字符串添加到 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="65e3e-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-170">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="65e3e-171">生成项目以检查编译器错误。</span><span class="sxs-lookup"><span data-stu-id="65e3e-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="65e3e-172">基架电影页面</span><span class="sxs-lookup"><span data-stu-id="65e3e-172">Scaffold movie pages</span></span>

<span data-ttu-id="65e3e-173">使用基架工具为电影模型生成“创建”、“读取”、“更新”和“删除”(CRUD) 页面。</span><span class="sxs-lookup"><span data-stu-id="65e3e-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65e3e-175">在解决方案资源管理器中，右键单击“Controllers”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="65e3e-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上述步骤的视图](adding-model/_static/add_controller21.png)

<span data-ttu-id="65e3e-177">在“添加基架”对话框中，选择“包含视图的 MVC 控制器(使用 Entity Framework)”>“添加” 。</span><span class="sxs-lookup"><span data-stu-id="65e3e-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![“添加基架”对话框](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="65e3e-179">填写“添加控制器”对话框：</span><span class="sxs-lookup"><span data-stu-id="65e3e-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="65e3e-180">模型类：Movie (MvcMovie.Models)</span><span class="sxs-lookup"><span data-stu-id="65e3e-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="65e3e-181">数据上下文类：MvcMovieContext (MvcMovie.Data)</span><span class="sxs-lookup"><span data-stu-id="65e3e-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![“添加数据”上下文](adding-model/_static/dc3.png)

* <span data-ttu-id="65e3e-183">视图：将每个选项保持为默认选中状态</span><span class="sxs-lookup"><span data-stu-id="65e3e-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="65e3e-184">控制器名称：保留默认的 MoviesController</span><span class="sxs-lookup"><span data-stu-id="65e3e-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="65e3e-185">选择“添加”</span><span class="sxs-lookup"><span data-stu-id="65e3e-185">Select **Add**</span></span>

<span data-ttu-id="65e3e-186">Visual Studio 将创建：</span><span class="sxs-lookup"><span data-stu-id="65e3e-186">Visual Studio creates:</span></span>

* <span data-ttu-id="65e3e-187">电影控制器 (Controllers/MoviesController.cs)</span><span class="sxs-lookup"><span data-stu-id="65e3e-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="65e3e-188">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="65e3e-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="65e3e-189">自动创建这些文件称为“基架”。</span><span class="sxs-lookup"><span data-stu-id="65e3e-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="65e3e-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65e3e-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="65e3e-191">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="65e3e-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="65e3e-192">在 Linux 上，导出基架工具路径：</span><span class="sxs-lookup"><span data-stu-id="65e3e-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="65e3e-193">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="65e3e-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="65e3e-195">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="65e3e-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="65e3e-196">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="65e3e-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="65e3e-197">你还不能使用基架页面，因为该数据库不存在。</span><span class="sxs-lookup"><span data-stu-id="65e3e-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="65e3e-198">如果运行应用并单击“Movie App”链接，则会出现“无法打开数据库”或“无此类表：Movie”错误消息。</span><span class="sxs-lookup"><span data-stu-id="65e3e-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="65e3e-199">初始迁移</span><span class="sxs-lookup"><span data-stu-id="65e3e-199">Initial migration</span></span>

<span data-ttu-id="65e3e-200">使用 EF Core [迁移](xref:data/ef-mvc/migrations)功能来创建数据库。</span><span class="sxs-lookup"><span data-stu-id="65e3e-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="65e3e-201">迁移是可用于创建和更新数据库以匹配数据模型的一组工具。</span><span class="sxs-lookup"><span data-stu-id="65e3e-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65e3e-203">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”(PMC)。 </span><span class="sxs-lookup"><span data-stu-id="65e3e-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="65e3e-204">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="65e3e-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="65e3e-205">`Add-Migration InitialCreate`：生成 Migrations/{timestamp}_InitialCreate.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="65e3e-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="65e3e-206">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="65e3e-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="65e3e-207">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="65e3e-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="65e3e-208">因为这是首次迁移，所以生成的类包含用于创建数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="65e3e-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="65e3e-209">数据库架构基于在 `MvcMovieContext` 类中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="65e3e-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="65e3e-210">`Update-Database`：将数据库更新到上一个命令创建的最新迁移。</span><span class="sxs-lookup"><span data-stu-id="65e3e-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="65e3e-211">此命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="65e3e-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="65e3e-212">数据库更新命令生成以下警告：</span><span class="sxs-lookup"><span data-stu-id="65e3e-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="65e3e-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="65e3e-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="65e3e-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="65e3e-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="65e3e-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="65e3e-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="65e3e-216">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="65e3e-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-217">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="65e3e-218">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="65e3e-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="65e3e-219">`ef migrations add InitialCreate`：生成 Migrations/{timestamp}_InitialCreate.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="65e3e-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="65e3e-220">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="65e3e-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="65e3e-221">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="65e3e-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="65e3e-222">因为这是首次迁移，所以生成的类包含用于创建数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="65e3e-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="65e3e-223">数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="65e3e-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="65e3e-224">`ef database update`：将数据库更新到上一个命令创建的最新迁移。</span><span class="sxs-lookup"><span data-stu-id="65e3e-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="65e3e-225">此命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="65e3e-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="65e3e-226">InitialCreate 类</span><span class="sxs-lookup"><span data-stu-id="65e3e-226">The InitialCreate class</span></span>

<span data-ttu-id="65e3e-227">检查 Migrations/{timestamp}_InitialCreate.cs 迁移文件：</span><span class="sxs-lookup"><span data-stu-id="65e3e-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="65e3e-228">`Up` 方法创建 Movie 表，并将 `Id` 配置为主键。</span><span class="sxs-lookup"><span data-stu-id="65e3e-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="65e3e-229">`Down` 方法可还原 `Up` 迁移所做的架构更改。</span><span class="sxs-lookup"><span data-stu-id="65e3e-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="65e3e-230">测试应用</span><span class="sxs-lookup"><span data-stu-id="65e3e-230">Test the app</span></span>

* <span data-ttu-id="65e3e-231">运行应用并单击“Movie App”链接。</span><span class="sxs-lookup"><span data-stu-id="65e3e-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="65e3e-232">如果遇到类似于以下情况的异常：</span><span class="sxs-lookup"><span data-stu-id="65e3e-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-234">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="65e3e-235">可能缺失[迁移步骤](#migration)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="65e3e-236">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="65e3e-236">Test the **Create** page.</span></span> <span data-ttu-id="65e3e-237">输入并提交数据。</span><span class="sxs-lookup"><span data-stu-id="65e3e-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="65e3e-238">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="65e3e-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="65e3e-239">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="65e3e-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="65e3e-240">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="65e3e-241">测试“编辑”、“详细信息”和“删除”页  。</span><span class="sxs-lookup"><span data-stu-id="65e3e-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="65e3e-242">控制器中的依赖项注入</span><span class="sxs-lookup"><span data-stu-id="65e3e-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65e3e-244">打开 Controllers/MoviesController.cs 文件并检查构造函数：</span><span class="sxs-lookup"><span data-stu-id="65e3e-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="65e3e-245">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="65e3e-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="65e3e-246">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="65e3e-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-247">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="65e3e-248">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="65e3e-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="65e3e-249">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="65e3e-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="65e3e-250">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="65e3e-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="65e3e-251">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="65e3e-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="65e3e-252">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="65e3e-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="65e3e-253">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="65e3e-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="65e3e-254">强类型模型和 @model 关键词</span><span class="sxs-lookup"><span data-stu-id="65e3e-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="65e3e-255">在本教程之前的内容中，已经介绍了控制器如何使用 `ViewData` 字典将数据或对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="65e3e-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="65e3e-256">`ViewData` 字典是一个动态对象，提供了将信息传递给视图的方便的后期绑定方法。</span><span class="sxs-lookup"><span data-stu-id="65e3e-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="65e3e-257">MVC 还提供将强类型模型对象传递给视图的功能。</span><span class="sxs-lookup"><span data-stu-id="65e3e-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="65e3e-258">此强类型方法启用编译时代码检查。</span><span class="sxs-lookup"><span data-stu-id="65e3e-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="65e3e-259">基架机制通过 `MoviesController` 类和视图使用了此方法（即传递强类型模型）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="65e3e-260">检查 Controllers/MoviesController.cs 文件中生成的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="65e3e-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="65e3e-261">`id` 参数通常作为路由数据传递。</span><span class="sxs-lookup"><span data-stu-id="65e3e-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="65e3e-262">例如 `https://localhost:5001/movies/details/1` 的设置如下：</span><span class="sxs-lookup"><span data-stu-id="65e3e-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="65e3e-263">控制器被设置为 `movies` 控制器（第一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="65e3e-264">操作被设置为 `details`（第二个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="65e3e-265">ID 被设置为 1（最后一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="65e3e-266">还可以使用查询字符串传入 `id`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="65e3e-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="65e3e-267">在未提供 ID 值的情况下，`id` 参数可定义为[可以为 null 的类型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="65e3e-268">[Lambda 表达式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)会被传入 `FirstOrDefaultAsync` 以选择与路由数据或查询字符串值相匹配的电影实体。</span><span class="sxs-lookup"><span data-stu-id="65e3e-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="65e3e-269">如果找到了电影，`Movie` 模型的实例则会被传递到 `Details` 视图：</span><span class="sxs-lookup"><span data-stu-id="65e3e-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="65e3e-270">检查 Views/Movies/Details.cshtml 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="65e3e-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="65e3e-271">视图文件顶部的 `@model` 语句可指定视图期望的对象类型。</span><span class="sxs-lookup"><span data-stu-id="65e3e-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="65e3e-272">创建影片控制器时，将包含以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="65e3e-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="65e3e-273">此 `@model` 指令允许访问控制器传递给视图的影片。</span><span class="sxs-lookup"><span data-stu-id="65e3e-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="65e3e-274">`Model` 对象为强类型对象。</span><span class="sxs-lookup"><span data-stu-id="65e3e-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="65e3e-275">例如，在 Details.cshtml 视图中，代码通过强类型的 `Model` 对象将每个电影字段传递给 `DisplayNameFor` 和 `DisplayFor`HTML 帮助程序。</span><span class="sxs-lookup"><span data-stu-id="65e3e-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="65e3e-276">`Create` 和 `Edit` 方法以及视图也传递一个 `Movie` 模型对象。</span><span class="sxs-lookup"><span data-stu-id="65e3e-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="65e3e-277">检查电影控制器中的 Index.cshtml 视图和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="65e3e-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="65e3e-278">请注意代码在调用 `View` 方法时是如何创建 `List` 对象的。</span><span class="sxs-lookup"><span data-stu-id="65e3e-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="65e3e-279">代码将此 `Movies` 列表从 `Index` 操作方法传递给视图：</span><span class="sxs-lookup"><span data-stu-id="65e3e-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="65e3e-280">创建影片控制器后，基架将以下 `@model` 语句包含在 Index.cshtml 文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="65e3e-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="65e3e-281">`@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影列表。</span><span class="sxs-lookup"><span data-stu-id="65e3e-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="65e3e-282">例如，在 Index.cshtml 视图中，代码使用 `foreach` 语句通过强类型 `Model` 对象对电影进行循环遍历：</span><span class="sxs-lookup"><span data-stu-id="65e3e-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="65e3e-283">因为 `Model` 对象为强类型（作为 `IEnumerable<Movie>` 对象），因此循环中的每个项都被类型化为 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="65e3e-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="65e3e-284">除其他优点之外，这意味着可对代码进行编译时检查。</span><span class="sxs-lookup"><span data-stu-id="65e3e-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65e3e-285">其他资源</span><span class="sxs-lookup"><span data-stu-id="65e3e-285">Additional resources</span></span>

* [<span data-ttu-id="65e3e-286">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="65e3e-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="65e3e-287">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="65e3e-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="65e3e-288">[上一篇：添加视图](adding-view.md)
> [下一篇：使用 SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="65e3e-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="65e3e-289">添加数据模型类</span><span class="sxs-lookup"><span data-stu-id="65e3e-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65e3e-291">右键单击 Models 文件夹，然后单击“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="65e3e-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="65e3e-292">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="65e3e-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-293">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="65e3e-294">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="65e3e-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="65e3e-295">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="65e3e-295">Scaffold the movie model</span></span>

<span data-ttu-id="65e3e-296">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="65e3e-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="65e3e-297">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="65e3e-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65e3e-299">在解决方案资源管理器中，右键单击“Controllers”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="65e3e-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上述步骤的视图](adding-model/_static/add_controller21.png)

<span data-ttu-id="65e3e-301">在“添加基架”对话框中，选择“包含视图的 MVC 控制器(使用 Entity Framework)”>“添加” 。</span><span class="sxs-lookup"><span data-stu-id="65e3e-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![“添加基架”对话框](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="65e3e-303">填写“添加控制器”对话框：</span><span class="sxs-lookup"><span data-stu-id="65e3e-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="65e3e-304">模型类：Movie (MvcMovie.Models)</span><span class="sxs-lookup"><span data-stu-id="65e3e-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="65e3e-305">数据上下文类：选择 + 图标并添加默认的 MvcMovie.Models.MvcMovieContext </span><span class="sxs-lookup"><span data-stu-id="65e3e-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![“添加数据”上下文](adding-model/_static/dc.png)

* <span data-ttu-id="65e3e-307">视图：将每个选项保持为默认选中状态</span><span class="sxs-lookup"><span data-stu-id="65e3e-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="65e3e-308">控制器名称：保留默认的 MoviesController</span><span class="sxs-lookup"><span data-stu-id="65e3e-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="65e3e-309">选择“添加”</span><span class="sxs-lookup"><span data-stu-id="65e3e-309">Select **Add**</span></span>

![“添加控制器”对话框](adding-model/_static/add_controller2.png)

<span data-ttu-id="65e3e-311">Visual Studio 将创建：</span><span class="sxs-lookup"><span data-stu-id="65e3e-311">Visual Studio creates:</span></span>

* <span data-ttu-id="65e3e-312">Entity Framework Core [数据库上下文类](xref:data/ef-mvc/intro#create-the-database-context) (Data/MvcMovieContext.cs)</span><span class="sxs-lookup"><span data-stu-id="65e3e-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="65e3e-313">电影控制器 (Controllers/MoviesController.cs)</span><span class="sxs-lookup"><span data-stu-id="65e3e-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="65e3e-314">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="65e3e-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="65e3e-315">自动创建数据库上下文和 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)（创建、读取、更新和删除）操作方法和视图的过程称为“搭建基架”。</span><span class="sxs-lookup"><span data-stu-id="65e3e-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="65e3e-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65e3e-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="65e3e-317">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="65e3e-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="65e3e-318">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="65e3e-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="65e3e-319">在 Linux 上，导出基架工具路径：</span><span class="sxs-lookup"><span data-stu-id="65e3e-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="65e3e-320">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="65e3e-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-321">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="65e3e-322">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="65e3e-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="65e3e-323">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="65e3e-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="65e3e-324">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="65e3e-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="65e3e-325">如果运行应用并单击“Mvc 电影”链接，则会出现以下类似的错误：</span><span class="sxs-lookup"><span data-stu-id="65e3e-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-327">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="65e3e-328">你需要创建数据库，并且使用 EF Core [迁移](xref:data/ef-mvc/migrations)功能来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="65e3e-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="65e3e-329">通过迁移可创建与数据模型匹配的数据库，并在数据模型更改时更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="65e3e-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="65e3e-330">初始迁移</span><span class="sxs-lookup"><span data-stu-id="65e3e-330">Initial migration</span></span>

<span data-ttu-id="65e3e-331">在本节中，将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="65e3e-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="65e3e-332">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="65e3e-332">Add an initial migration.</span></span>
* <span data-ttu-id="65e3e-333">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="65e3e-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="65e3e-335">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”(PMC)。 </span><span class="sxs-lookup"><span data-stu-id="65e3e-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC 菜单](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="65e3e-337">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="65e3e-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="65e3e-338">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="65e3e-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="65e3e-339">数据库架构基于在 `MvcMovieContext` 类中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="65e3e-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="65e3e-340">`Initial` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="65e3e-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="65e3e-341">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="65e3e-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="65e3e-342">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="65e3e-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="65e3e-343">`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="65e3e-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-344">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="65e3e-345">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="65e3e-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="65e3e-346">数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="65e3e-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="65e3e-347">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="65e3e-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="65e3e-348">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="65e3e-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="65e3e-349">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="65e3e-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="65e3e-350">ASP.NET Core 通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 生成。</span><span class="sxs-lookup"><span data-stu-id="65e3e-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="65e3e-351">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过 DI 注册。</span><span class="sxs-lookup"><span data-stu-id="65e3e-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="65e3e-352">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="65e3e-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="65e3e-353">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="65e3e-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65e3e-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65e3e-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65e3e-355">基架工具自动创建数据库上下文并将其注册到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="65e3e-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="65e3e-356">我们来研究以下 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="65e3e-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="65e3e-357">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="65e3e-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="65e3e-358">`MvcMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="65e3e-359">数据上下文 (`MvcMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="65e3e-360">数据上下文指定数据模型中包含哪些实体：</span><span class="sxs-lookup"><span data-stu-id="65e3e-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="65e3e-361">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="65e3e-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="65e3e-362">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="65e3e-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="65e3e-363">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="65e3e-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="65e3e-364">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="65e3e-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="65e3e-365">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="65e3e-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65e3e-366">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65e3e-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="65e3e-367">创建了数据库上下文并将其注册到了 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="65e3e-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="65e3e-368">测试应用</span><span class="sxs-lookup"><span data-stu-id="65e3e-368">Test the app</span></span>

* <span data-ttu-id="65e3e-369">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="65e3e-370">如果收到如下所示数据库异常：</span><span class="sxs-lookup"><span data-stu-id="65e3e-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="65e3e-371">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="65e3e-372">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="65e3e-372">Test the **Create** link.</span></span> <span data-ttu-id="65e3e-373">输入并提交数据。</span><span class="sxs-lookup"><span data-stu-id="65e3e-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="65e3e-374">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="65e3e-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="65e3e-375">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="65e3e-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="65e3e-376">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="65e3e-377">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="65e3e-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="65e3e-378">检查 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="65e3e-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="65e3e-379">上面突出显示的代码显示了要添加到[依赖关系注入](xref:fundamentals/dependency-injection)容器的电影数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="65e3e-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="65e3e-380">`services.AddDbContext<MvcMovieContext>(options =>` 指定要使用的数据库和连接字符串。</span><span class="sxs-lookup"><span data-stu-id="65e3e-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="65e3e-381">`=>` 是 [lambda 运算符](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="65e3e-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="65e3e-382">打开 Controllers/MoviesController.cs 文件并检查构造函数：</span><span class="sxs-lookup"><span data-stu-id="65e3e-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="65e3e-383">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="65e3e-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="65e3e-384">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="65e3e-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="65e3e-385">强类型模型和 @model 关键词</span><span class="sxs-lookup"><span data-stu-id="65e3e-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="65e3e-386">在本教程之前的内容中，已经介绍了控制器如何使用 `ViewData` 字典将数据或对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="65e3e-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="65e3e-387">`ViewData` 字典是一个动态对象，提供了将信息传递给视图的方便的后期绑定方法。</span><span class="sxs-lookup"><span data-stu-id="65e3e-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="65e3e-388">MVC 还提供将强类型模型对象传递给视图的功能。</span><span class="sxs-lookup"><span data-stu-id="65e3e-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="65e3e-389">凭借此强类型方法可更好地对代码进行编译时检查。</span><span class="sxs-lookup"><span data-stu-id="65e3e-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="65e3e-390">基架机制在创建方法和视图时，通过 `MoviesController` 类和视图使用了此方法（即传递强类型模型）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="65e3e-391">检查 Controllers/MoviesController.cs 文件中生成的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="65e3e-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="65e3e-392">`id` 参数通常作为路由数据传递。</span><span class="sxs-lookup"><span data-stu-id="65e3e-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="65e3e-393">例如 `https://localhost:5001/movies/details/1` 的设置如下：</span><span class="sxs-lookup"><span data-stu-id="65e3e-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="65e3e-394">控制器被设置为 `movies` 控制器（第一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="65e3e-395">操作被设置为 `details`（第二个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="65e3e-396">ID 被设置为 1（最后一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="65e3e-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="65e3e-397">还可以使用查询字符串传入 `id`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="65e3e-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="65e3e-398">在未提供 ID 值的情况下，`id` 参数可定义为[可以为 null 的类型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`)。</span><span class="sxs-lookup"><span data-stu-id="65e3e-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="65e3e-399">[Lambda 表达式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)会被传入 `FirstOrDefaultAsync` 以选择与路由数据或查询字符串值相匹配的电影实体。</span><span class="sxs-lookup"><span data-stu-id="65e3e-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="65e3e-400">如果找到了电影，`Movie` 模型的实例则会被传递到 `Details` 视图：</span><span class="sxs-lookup"><span data-stu-id="65e3e-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="65e3e-401">检查 Views/Movies/Details.cshtml 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="65e3e-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="65e3e-402">通过将 `@model` 语句包括在视图文件的顶端，可以指定视图期望的对象类型。</span><span class="sxs-lookup"><span data-stu-id="65e3e-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="65e3e-403">创建电影控制器时，会自动在 Details.cshtml 文件的顶端包括以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="65e3e-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="65e3e-404">此 `@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影。</span><span class="sxs-lookup"><span data-stu-id="65e3e-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="65e3e-405">例如，在 Details.cshtml 视图中，代码通过强类型的 `Model` 对象将每个电影字段传递给 `DisplayNameFor` 和 `DisplayFor`HTML 帮助程序。</span><span class="sxs-lookup"><span data-stu-id="65e3e-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="65e3e-406">`Create` 和 `Edit` 方法以及视图也传递一个 `Movie` 模型对象。</span><span class="sxs-lookup"><span data-stu-id="65e3e-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="65e3e-407">检查电影控制器中的 Index.cshtml 视图和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="65e3e-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="65e3e-408">请注意代码在调用 `View` 方法时是如何创建 `List` 对象的。</span><span class="sxs-lookup"><span data-stu-id="65e3e-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="65e3e-409">代码将此 `Movies` 列表从 `Index` 操作方法传递给视图：</span><span class="sxs-lookup"><span data-stu-id="65e3e-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="65e3e-410">创建电影控制器时，基架会自动在 Index.cshtml 文件的顶端包含以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="65e3e-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="65e3e-411">`@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影列表。</span><span class="sxs-lookup"><span data-stu-id="65e3e-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="65e3e-412">例如，在 Index.cshtml 视图中，代码使用 `foreach` 语句通过强类型 `Model` 对象对电影进行循环遍历：</span><span class="sxs-lookup"><span data-stu-id="65e3e-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="65e3e-413">因为 `Model` 对象为强类型（作为 `IEnumerable<Movie>` 对象），因此循环中的每个项都被类型化为 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="65e3e-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="65e3e-414">除其他优点之外，这意味着可对代码进行编译时检查：</span><span class="sxs-lookup"><span data-stu-id="65e3e-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65e3e-415">其他资源</span><span class="sxs-lookup"><span data-stu-id="65e3e-415">Additional resources</span></span>

* [<span data-ttu-id="65e3e-416">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="65e3e-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="65e3e-417">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="65e3e-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="65e3e-418">[上一篇：添加视图](adding-view.md)
> [下一篇：使用数据库](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="65e3e-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
