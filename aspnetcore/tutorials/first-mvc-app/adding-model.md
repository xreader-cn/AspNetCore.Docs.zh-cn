---
title: 第 4 部分，将模型添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: ASP.NET Core MVC 教程系列的第 4 部分。
ms.author: riande
ms.date: 11/16/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 16cef6cc9e772f494515942072c2aaf58913ce91
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688444"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="8691d-103">第 4 部分，将模型添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="8691d-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="8691d-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="8691d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="8691d-105">在本部分中将添加用于管理数据库中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="8691d-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="8691d-106">这些类将是 MVC 应用的“Model”部分 。</span><span class="sxs-lookup"><span data-stu-id="8691d-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="8691d-107">可以结合 [Entity Framework Core](/ef/core) (EF Core) 使用这些类来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="8691d-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="8691d-108">EF Core 是对象关系映射 (ORM) 框架，可以简化需要编写的数据访问代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="8691d-109">要创建的模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系   。</span><span class="sxs-lookup"><span data-stu-id="8691d-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8691d-110">它们只定义将存储在数据库中的数据的属性。</span><span class="sxs-lookup"><span data-stu-id="8691d-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="8691d-111">在本教程中，首先要编写模型类，然后 EF Core 将创建数据库。</span><span class="sxs-lookup"><span data-stu-id="8691d-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="8691d-112">添加数据模型类</span><span class="sxs-lookup"><span data-stu-id="8691d-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-114">右键单击 Models 文件夹，然后单击“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="8691d-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="8691d-115">将文件命名为 Movie.cs。</span><span class="sxs-lookup"><span data-stu-id="8691d-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8691d-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8691d-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8691d-117">将名为 Movie.cs 的文件添加到“Models”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="8691d-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8691d-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8691d-119">右键单击 Models 文件夹，然后单击“添加” > “新类” > “空类”。  </span><span class="sxs-lookup"><span data-stu-id="8691d-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="8691d-120">将文件命名为 Movie.cs。</span><span class="sxs-lookup"><span data-stu-id="8691d-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="8691d-121">使用以下代码更新 Movie.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="8691d-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="8691d-122">`Movie` 类包含一个 `Id` 字段，数据需要该字段作为主键。</span><span class="sxs-lookup"><span data-stu-id="8691d-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="8691d-123">`ReleaseDate` 上的 <xref:System.ComponentModel.DataAnnotations.DataType> 特性指定了数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="8691d-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8691d-124">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="8691d-124">With this attribute:</span></span>

* <span data-ttu-id="8691d-125">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="8691d-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="8691d-126">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="8691d-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8691d-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="8691d-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="8691d-128">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="8691d-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-130">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”(PMC)。 </span><span class="sxs-lookup"><span data-stu-id="8691d-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 菜单](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8691d-132">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="8691d-133">前面的命令添加 EF Core SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="8691d-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="8691d-134">提供程序包将 EF Core 包作为依赖项进行安装。</span><span class="sxs-lookup"><span data-stu-id="8691d-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="8691d-135">在本教程后面的基架步骤中会自动安装其他包。</span><span class="sxs-lookup"><span data-stu-id="8691d-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8691d-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8691d-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8691d-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8691d-138">从“项目”菜单中选择“管理 NuGet 程序包” 。</span><span class="sxs-lookup"><span data-stu-id="8691d-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="8691d-139">在右上方的“搜索”字段中，输入 `Microsoft.EntityFrameworkCore.SQLite` 并按回车键进行搜索。 </span><span class="sxs-lookup"><span data-stu-id="8691d-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="8691d-140">搜索匹配的 NuGet 程序包，并按“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="8691d-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![添加 Entity Framework Core NuGet 包](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="8691d-142">“选择项目”对话框将显示，并已选中 `MvcMovie` 项目。</span><span class="sxs-lookup"><span data-stu-id="8691d-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="8691d-143">按“确认”按钮。</span><span class="sxs-lookup"><span data-stu-id="8691d-143">Press the **Ok** button.</span></span>

<span data-ttu-id="8691d-144">“接受许可证”对话框将显示。</span><span class="sxs-lookup"><span data-stu-id="8691d-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="8691d-145">根据需要查看许可证，然后单击“接受”按钮。</span><span class="sxs-lookup"><span data-stu-id="8691d-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="8691d-146">重复上面的步骤，以安装以下 NuGet 程序包：</span><span class="sxs-lookup"><span data-stu-id="8691d-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="8691d-147">运行以下 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="8691d-148">上述命令会添加 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="8691d-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="8691d-149">创建数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="8691d-149">Create a database context class</span></span>

<span data-ttu-id="8691d-150">需要一个数据库上下文类来协调 `Movie` 模型的 EF Core 功能（创建、读取、更新和删除）。</span><span class="sxs-lookup"><span data-stu-id="8691d-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="8691d-151">数据库上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 并指定要包含在数据模型中的实体。</span><span class="sxs-lookup"><span data-stu-id="8691d-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="8691d-152">创建一个“Data”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8691d-152">Create a *Data* folder.</span></span>

<span data-ttu-id="8691d-153">使用以下代码添加 Data/MvcMovieContext.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="8691d-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="8691d-154">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="8691d-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8691d-155">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="8691d-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8691d-156">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="8691d-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="8691d-157">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="8691d-157">Register the database context</span></span>

<span data-ttu-id="8691d-158">ASP.NET Core 通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 生成。</span><span class="sxs-lookup"><span data-stu-id="8691d-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8691d-159">在应用程序启动过程中，必须向 DI 注册服务（如 EF Core DB 上下文）。</span><span class="sxs-lookup"><span data-stu-id="8691d-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="8691d-160">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="8691d-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8691d-161">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="8691d-162">本部分会将数据库上下文注册到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="8691d-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="8691d-163">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="8691d-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="8691d-164">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="8691d-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="8691d-167">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="8691d-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8691d-168">进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8691d-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="8691d-169">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="8691d-169">Add a database connection string</span></span>

<span data-ttu-id="8691d-170">将连接字符串添加到 *appsettings.json* 文件中：</span><span class="sxs-lookup"><span data-stu-id="8691d-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-172">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="8691d-173">生成项目以检查编译器错误。</span><span class="sxs-lookup"><span data-stu-id="8691d-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="8691d-174">基架电影页面</span><span class="sxs-lookup"><span data-stu-id="8691d-174">Scaffold movie pages</span></span>

<span data-ttu-id="8691d-175">使用基架工具为电影模型生成“创建”、“读取”、“更新”和“删除”(CRUD) 页面。</span><span class="sxs-lookup"><span data-stu-id="8691d-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-177">在解决方案资源管理器中，右键单击“Controllers”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="8691d-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上述步骤的视图](adding-model/_static/add_controller21.png)

<span data-ttu-id="8691d-179">在“添加基架”对话框中，选择“包含视图的 MVC 控制器(使用 Entity Framework)”>“添加” 。</span><span class="sxs-lookup"><span data-stu-id="8691d-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![“添加基架”对话框](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="8691d-181">填写“添加控制器”对话框：</span><span class="sxs-lookup"><span data-stu-id="8691d-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="8691d-182">模型类：Movie (MvcMovie.Models)</span><span class="sxs-lookup"><span data-stu-id="8691d-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="8691d-183">数据上下文类：MvcMovieContext (MvcMovie.Data)</span><span class="sxs-lookup"><span data-stu-id="8691d-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![“添加数据”上下文](adding-model/_static/dc5.png)

* <span data-ttu-id="8691d-185">视图：将每个选项保持为默认选中状态</span><span class="sxs-lookup"><span data-stu-id="8691d-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="8691d-186">控制器名称：保留默认的 MoviesController</span><span class="sxs-lookup"><span data-stu-id="8691d-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="8691d-187">选择“添加”</span><span class="sxs-lookup"><span data-stu-id="8691d-187">Select **Add**</span></span>

<span data-ttu-id="8691d-188">Visual Studio 将创建：</span><span class="sxs-lookup"><span data-stu-id="8691d-188">Visual Studio creates:</span></span>

* <span data-ttu-id="8691d-189">电影控制器 (Controllers/MoviesController.cs)</span><span class="sxs-lookup"><span data-stu-id="8691d-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="8691d-190">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="8691d-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="8691d-191">自动创建这些文件称为“基架”。</span><span class="sxs-lookup"><span data-stu-id="8691d-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="8691d-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8691d-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="8691d-193">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="8691d-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8691d-194">在 Linux 上，导出基架工具路径：</span><span class="sxs-lookup"><span data-stu-id="8691d-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="8691d-195">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8691d-196">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8691d-197">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="8691d-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8691d-198">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="8691d-199">你还不能使用基架页面，因为该数据库不存在。</span><span class="sxs-lookup"><span data-stu-id="8691d-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="8691d-200">如果运行应用并单击“Movie App”链接，则会出现“无法打开数据库”或“无此类表：Movie”错误消息。</span><span class="sxs-lookup"><span data-stu-id="8691d-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="8691d-201">初始迁移</span><span class="sxs-lookup"><span data-stu-id="8691d-201">Initial migration</span></span>

<span data-ttu-id="8691d-202">使用 EF Core [迁移](xref:data/ef-mvc/migrations)功能来创建数据库。</span><span class="sxs-lookup"><span data-stu-id="8691d-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="8691d-203">迁移是可用于创建和更新数据库以匹配数据模型的一组工具。</span><span class="sxs-lookup"><span data-stu-id="8691d-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-205">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”(PMC)。 </span><span class="sxs-lookup"><span data-stu-id="8691d-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="8691d-206">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="8691d-207">`Add-Migration InitialCreate`：生成 Migrations/{timestamp}_InitialCreate.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="8691d-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="8691d-208">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8691d-209">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="8691d-210">因为这是首次迁移，所以生成的类包含用于创建数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="8691d-211">数据库架构基于在 `MvcMovieContext` 类中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="8691d-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="8691d-212">`Update-Database`：将数据库更新到上一个命令创建的最新迁移。</span><span class="sxs-lookup"><span data-stu-id="8691d-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="8691d-213">此命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="8691d-214">数据库更新命令生成以下警告：</span><span class="sxs-lookup"><span data-stu-id="8691d-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="8691d-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="8691d-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8691d-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="8691d-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8691d-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="8691d-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="8691d-218">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="8691d-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-219">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="8691d-220">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="8691d-221">`ef migrations add InitialCreate`：生成 Migrations/{timestamp}_InitialCreate.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="8691d-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="8691d-222">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8691d-223">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="8691d-224">因为这是首次迁移，所以生成的类包含用于创建数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="8691d-225">数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="8691d-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="8691d-226">`ef database update`：将数据库更新到上一个命令创建的最新迁移。</span><span class="sxs-lookup"><span data-stu-id="8691d-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="8691d-227">此命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="8691d-228">InitialCreate 类</span><span class="sxs-lookup"><span data-stu-id="8691d-228">The InitialCreate class</span></span>

<span data-ttu-id="8691d-229">检查 Migrations/{timestamp}_InitialCreate.cs 迁移文件：</span><span class="sxs-lookup"><span data-stu-id="8691d-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="8691d-230">`Up` 方法创建 Movie 表，并将 `Id` 配置为主键。</span><span class="sxs-lookup"><span data-stu-id="8691d-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="8691d-231">`Down` 方法可还原 `Up` 迁移所做的架构更改。</span><span class="sxs-lookup"><span data-stu-id="8691d-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="8691d-232">测试应用</span><span class="sxs-lookup"><span data-stu-id="8691d-232">Test the app</span></span>

* <span data-ttu-id="8691d-233">运行应用并单击“Movie App”链接。</span><span class="sxs-lookup"><span data-stu-id="8691d-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="8691d-234">如果遇到类似于以下情况的异常：</span><span class="sxs-lookup"><span data-stu-id="8691d-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="8691d-237">可能缺失[迁移步骤](#migration)。</span><span class="sxs-lookup"><span data-stu-id="8691d-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="8691d-238">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="8691d-238">Test the **Create** page.</span></span> <span data-ttu-id="8691d-239">输入并提交数据。</span><span class="sxs-lookup"><span data-stu-id="8691d-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8691d-240">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="8691d-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8691d-241">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="8691d-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8691d-242">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="8691d-242">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8691d-243">测试“编辑”、“详细信息”和“删除”页  。</span><span class="sxs-lookup"><span data-stu-id="8691d-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="8691d-244">控制器中的依赖项注入</span><span class="sxs-lookup"><span data-stu-id="8691d-244">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-246">打开 Controllers/MoviesController.cs 文件并检查构造函数：</span><span class="sxs-lookup"><span data-stu-id="8691d-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8691d-247">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="8691d-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8691d-248">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="8691d-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8691d-250">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="8691d-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8691d-251">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="8691d-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="8691d-252">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="8691d-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="8691d-253">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="8691d-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="8691d-254">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="8691d-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="8691d-255">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="8691d-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="8691d-256">强类型模型和 @model 关键词</span><span class="sxs-lookup"><span data-stu-id="8691d-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="8691d-257">在本教程之前的内容中，已经介绍了控制器如何使用 `ViewData` 字典将数据或对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="8691d-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="8691d-258">`ViewData` 字典是一个动态对象，提供了将信息传递给视图的方便的后期绑定方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="8691d-259">MVC 还提供将强类型模型对象传递给视图的功能。</span><span class="sxs-lookup"><span data-stu-id="8691d-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="8691d-260">此强类型方法启用编译时代码检查。</span><span class="sxs-lookup"><span data-stu-id="8691d-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="8691d-261">基架机制通过 `MoviesController` 类和视图使用了此方法（即传递强类型模型）。</span><span class="sxs-lookup"><span data-stu-id="8691d-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="8691d-262">检查 Controllers/MoviesController.cs 文件中生成的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="8691d-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="8691d-263">`id` 参数通常作为路由数据传递。</span><span class="sxs-lookup"><span data-stu-id="8691d-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="8691d-264">例如 `https://localhost:5001/movies/details/1` 的设置如下：</span><span class="sxs-lookup"><span data-stu-id="8691d-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="8691d-265">控制器被设置为 `movies` 控制器（第一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="8691d-266">操作被设置为 `details`（第二个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="8691d-267">ID 被设置为 1（最后一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="8691d-268">还可以使用查询字符串传入 `id`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="8691d-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="8691d-269">在未提供 ID 值的情况下，`id` 参数可定义为[可以为 null 的类型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`)。</span><span class="sxs-lookup"><span data-stu-id="8691d-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="8691d-270">[Lambda 表达式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)会被传入 `FirstOrDefaultAsync` 以选择与路由数据或查询字符串值相匹配的电影实体。</span><span class="sxs-lookup"><span data-stu-id="8691d-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="8691d-271">如果找到了电影，`Movie` 模型的实例则会被传递到 `Details` 视图：</span><span class="sxs-lookup"><span data-stu-id="8691d-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="8691d-272">检查 Views/Movies/Details.cshtml 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="8691d-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="8691d-273">视图文件顶部的 `@model` 语句可指定视图期望的对象类型。</span><span class="sxs-lookup"><span data-stu-id="8691d-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="8691d-274">创建影片控制器时，将包含以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="8691d-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="8691d-275">此 `@model` 指令允许访问控制器传递给视图的影片。</span><span class="sxs-lookup"><span data-stu-id="8691d-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="8691d-276">`Model` 对象为强类型对象。</span><span class="sxs-lookup"><span data-stu-id="8691d-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="8691d-277">例如，在 Details.cshtml 视图中，代码通过强类型的 `Model` 对象将每个电影字段传递给 `DisplayNameFor` 和 `DisplayFor`HTML 帮助程序。</span><span class="sxs-lookup"><span data-stu-id="8691d-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="8691d-278">`Create` 和 `Edit` 方法以及视图也传递一个 `Movie` 模型对象。</span><span class="sxs-lookup"><span data-stu-id="8691d-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="8691d-279">检查电影控制器中的 Index.cshtml 视图和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="8691d-280">请注意代码在调用 `View` 方法时是如何创建 `List` 对象的。</span><span class="sxs-lookup"><span data-stu-id="8691d-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="8691d-281">代码将此 `Movies` 列表从 `Index` 操作方法传递给视图：</span><span class="sxs-lookup"><span data-stu-id="8691d-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="8691d-282">创建影片控制器后，基架将以下 `@model` 语句包含在 Index.cshtml 文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="8691d-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="8691d-283">`@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影列表。</span><span class="sxs-lookup"><span data-stu-id="8691d-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8691d-284">例如，在 Index.cshtml 视图中，代码使用 `foreach` 语句通过强类型 `Model` 对象对电影进行循环遍历：</span><span class="sxs-lookup"><span data-stu-id="8691d-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="8691d-285">因为 `Model` 对象为强类型（作为 `IEnumerable<Movie>` 对象），因此循环中的每个项都被类型化为 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="8691d-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="8691d-286">除其他优点之外，这意味着可对代码进行编译时检查。</span><span class="sxs-lookup"><span data-stu-id="8691d-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8691d-287">其他资源</span><span class="sxs-lookup"><span data-stu-id="8691d-287">Additional resources</span></span>

* [<span data-ttu-id="8691d-288">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="8691d-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="8691d-289">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="8691d-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="8691d-290">[上一篇：添加视图](adding-view.md)
> [下一篇：使用 SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="8691d-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="8691d-291">添加数据模型类</span><span class="sxs-lookup"><span data-stu-id="8691d-291">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-293">右键单击 Models 文件夹，然后单击“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="8691d-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="8691d-294">将文件命名为 Movie.cs。</span><span class="sxs-lookup"><span data-stu-id="8691d-294">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8691d-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8691d-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8691d-296">将名为 Movie.cs 的文件添加到“Models”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="8691d-296">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8691d-297">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-297">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8691d-298">右键单击 Models 文件夹，然后单击“添加” > “新类” > “空类”。  </span><span class="sxs-lookup"><span data-stu-id="8691d-298">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="8691d-299">将文件命名为 Movie.cs。</span><span class="sxs-lookup"><span data-stu-id="8691d-299">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="8691d-300">使用以下代码更新 Movie.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="8691d-300">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="8691d-301">`Movie` 类包含一个 `Id` 字段，数据需要该字段作为主键。</span><span class="sxs-lookup"><span data-stu-id="8691d-301">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="8691d-302">`ReleaseDate` 上的 <xref:System.ComponentModel.DataAnnotations.DataType> 特性指定了数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="8691d-302">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8691d-303">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="8691d-303">With this attribute:</span></span>

* <span data-ttu-id="8691d-304">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="8691d-304">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="8691d-305">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="8691d-305">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8691d-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="8691d-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="8691d-307">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="8691d-307">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-308">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-308">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-309">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”(PMC)。 </span><span class="sxs-lookup"><span data-stu-id="8691d-309">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC 菜单](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8691d-311">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-311">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="8691d-312">前面的命令添加 EF Core SQL Server 提供程序。</span><span class="sxs-lookup"><span data-stu-id="8691d-312">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="8691d-313">提供程序包将 EF Core 包作为依赖项进行安装。</span><span class="sxs-lookup"><span data-stu-id="8691d-313">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="8691d-314">在本教程后面的基架步骤中会自动安装其他包。</span><span class="sxs-lookup"><span data-stu-id="8691d-314">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8691d-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8691d-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8691d-316">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8691d-317">从“项目”菜单中选择“管理 NuGet 程序包” 。</span><span class="sxs-lookup"><span data-stu-id="8691d-317">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="8691d-318">在右上方的“搜索”字段中，输入 `Microsoft.EntityFrameworkCore.SQLite` 并按回车键进行搜索。 </span><span class="sxs-lookup"><span data-stu-id="8691d-318">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="8691d-319">搜索匹配的 NuGet 程序包，并按“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="8691d-319">Select the matching NuGet package and press the **Add Package** button.</span></span>

![添加 Entity Framework Core NuGet 包](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="8691d-321">“选择项目”对话框将显示，并已选中 `MvcMovie` 项目。</span><span class="sxs-lookup"><span data-stu-id="8691d-321">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="8691d-322">按“确认”按钮。</span><span class="sxs-lookup"><span data-stu-id="8691d-322">Press the **Ok** button.</span></span>

<span data-ttu-id="8691d-323">“接受许可证”对话框将显示。</span><span class="sxs-lookup"><span data-stu-id="8691d-323">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="8691d-324">根据需要查看许可证，然后单击“接受”按钮。</span><span class="sxs-lookup"><span data-stu-id="8691d-324">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="8691d-325">重复上面的步骤，以安装以下 NuGet 程序包：</span><span class="sxs-lookup"><span data-stu-id="8691d-325">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="8691d-326">创建数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="8691d-326">Create a database context class</span></span>

<span data-ttu-id="8691d-327">需要一个数据库上下文类来协调 `Movie` 模型的 EF Core 功能（创建、读取、更新和删除）。</span><span class="sxs-lookup"><span data-stu-id="8691d-327">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="8691d-328">数据库上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 并指定要包含在数据模型中的实体。</span><span class="sxs-lookup"><span data-stu-id="8691d-328">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="8691d-329">创建一个“Data”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8691d-329">Create a *Data* folder.</span></span>

<span data-ttu-id="8691d-330">使用以下代码添加 Data/MvcMovieContext.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="8691d-330">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="8691d-331">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="8691d-331">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8691d-332">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="8691d-332">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8691d-333">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="8691d-333">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="8691d-334">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="8691d-334">Register the database context</span></span>

<span data-ttu-id="8691d-335">ASP.NET Core 通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 生成。</span><span class="sxs-lookup"><span data-stu-id="8691d-335">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8691d-336">在应用程序启动过程中，必须向 DI 注册服务（如 EF Core DB 上下文）。</span><span class="sxs-lookup"><span data-stu-id="8691d-336">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="8691d-337">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="8691d-337">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8691d-338">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-338">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="8691d-339">本部分会将数据库上下文注册到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="8691d-339">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="8691d-340">将以下 `using` 语句添加到 Startup.cs 顶部：</span><span class="sxs-lookup"><span data-stu-id="8691d-340">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="8691d-341">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="8691d-341">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-342">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-342">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-343">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-343">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="8691d-344">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="8691d-344">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8691d-345">进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8691d-345">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="8691d-346">添加数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="8691d-346">Add a database connection string</span></span>

<span data-ttu-id="8691d-347">将连接字符串添加到 *appsettings.json* 文件中：</span><span class="sxs-lookup"><span data-stu-id="8691d-347">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-348">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-348">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-349">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-349">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="8691d-350">生成项目以检查编译器错误。</span><span class="sxs-lookup"><span data-stu-id="8691d-350">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="8691d-351">基架电影页面</span><span class="sxs-lookup"><span data-stu-id="8691d-351">Scaffold movie pages</span></span>

<span data-ttu-id="8691d-352">使用基架工具为电影模型生成“创建”、“读取”、“更新”和“删除”(CRUD) 页面。</span><span class="sxs-lookup"><span data-stu-id="8691d-352">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-353">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-353">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-354">在解决方案资源管理器中，右键单击“Controllers”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="8691d-354">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上述步骤的视图](adding-model/_static/add_controller21.png)

<span data-ttu-id="8691d-356">在“添加基架”对话框中，选择“包含视图的 MVC 控制器(使用 Entity Framework)”>“添加” 。</span><span class="sxs-lookup"><span data-stu-id="8691d-356">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![“添加基架”对话框](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="8691d-358">填写“添加控制器”对话框：</span><span class="sxs-lookup"><span data-stu-id="8691d-358">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="8691d-359">模型类：Movie (MvcMovie.Models)</span><span class="sxs-lookup"><span data-stu-id="8691d-359">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="8691d-360">数据上下文类：MvcMovieContext (MvcMovie.Data)</span><span class="sxs-lookup"><span data-stu-id="8691d-360">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![“添加数据”上下文](adding-model/_static/dc3.png)

* <span data-ttu-id="8691d-362">视图：将每个选项保持为默认选中状态</span><span class="sxs-lookup"><span data-stu-id="8691d-362">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="8691d-363">控制器名称：保留默认的 MoviesController</span><span class="sxs-lookup"><span data-stu-id="8691d-363">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="8691d-364">选择“添加”</span><span class="sxs-lookup"><span data-stu-id="8691d-364">Select **Add**</span></span>

<span data-ttu-id="8691d-365">Visual Studio 将创建：</span><span class="sxs-lookup"><span data-stu-id="8691d-365">Visual Studio creates:</span></span>

* <span data-ttu-id="8691d-366">电影控制器 (Controllers/MoviesController.cs)</span><span class="sxs-lookup"><span data-stu-id="8691d-366">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="8691d-367">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="8691d-367">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="8691d-368">自动创建这些文件称为“基架”。</span><span class="sxs-lookup"><span data-stu-id="8691d-368">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="8691d-369">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8691d-369">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="8691d-370">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="8691d-370">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8691d-371">在 Linux 上，导出基架工具路径：</span><span class="sxs-lookup"><span data-stu-id="8691d-371">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="8691d-372">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-372">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8691d-373">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-373">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8691d-374">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="8691d-374">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8691d-375">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-375">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="8691d-376">你还不能使用基架页面，因为该数据库不存在。</span><span class="sxs-lookup"><span data-stu-id="8691d-376">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="8691d-377">如果运行应用并单击“Movie App”链接，则会出现“无法打开数据库”或“无此类表：Movie”错误消息。</span><span class="sxs-lookup"><span data-stu-id="8691d-377">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="8691d-378">初始迁移</span><span class="sxs-lookup"><span data-stu-id="8691d-378">Initial migration</span></span>

<span data-ttu-id="8691d-379">使用 EF Core [迁移](xref:data/ef-mvc/migrations)功能来创建数据库。</span><span class="sxs-lookup"><span data-stu-id="8691d-379">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="8691d-380">迁移是可用于创建和更新数据库以匹配数据模型的一组工具。</span><span class="sxs-lookup"><span data-stu-id="8691d-380">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-381">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-382">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”(PMC)。 </span><span class="sxs-lookup"><span data-stu-id="8691d-382">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="8691d-383">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-383">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="8691d-384">`Add-Migration InitialCreate`：生成 Migrations/{timestamp}_InitialCreate.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="8691d-384">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="8691d-385">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-385">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8691d-386">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-386">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="8691d-387">因为这是首次迁移，所以生成的类包含用于创建数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-387">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="8691d-388">数据库架构基于在 `MvcMovieContext` 类中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="8691d-388">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="8691d-389">`Update-Database`：将数据库更新到上一个命令创建的最新迁移。</span><span class="sxs-lookup"><span data-stu-id="8691d-389">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="8691d-390">此命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-390">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="8691d-391">数据库更新命令生成以下警告：</span><span class="sxs-lookup"><span data-stu-id="8691d-391">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="8691d-392">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="8691d-392">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8691d-393">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="8691d-393">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8691d-394">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="8691d-394">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="8691d-395">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="8691d-395">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-396">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-396">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="8691d-397">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-397">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="8691d-398">`ef migrations add InitialCreate`：生成 Migrations/{timestamp}_InitialCreate.cs 迁移文件。</span><span class="sxs-lookup"><span data-stu-id="8691d-398">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="8691d-399">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-399">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8691d-400">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-400">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="8691d-401">因为这是首次迁移，所以生成的类包含用于创建数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-401">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="8691d-402">数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="8691d-402">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="8691d-403">`ef database update`：将数据库更新到上一个命令创建的最新迁移。</span><span class="sxs-lookup"><span data-stu-id="8691d-403">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="8691d-404">此命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-404">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="8691d-405">InitialCreate 类</span><span class="sxs-lookup"><span data-stu-id="8691d-405">The InitialCreate class</span></span>

<span data-ttu-id="8691d-406">检查 Migrations/{timestamp}_InitialCreate.cs 迁移文件：</span><span class="sxs-lookup"><span data-stu-id="8691d-406">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="8691d-407">`Up` 方法创建 Movie 表，并将 `Id` 配置为主键。</span><span class="sxs-lookup"><span data-stu-id="8691d-407">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="8691d-408">`Down` 方法可还原 `Up` 迁移所做的架构更改。</span><span class="sxs-lookup"><span data-stu-id="8691d-408">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="8691d-409">测试应用</span><span class="sxs-lookup"><span data-stu-id="8691d-409">Test the app</span></span>

* <span data-ttu-id="8691d-410">运行应用并单击“Movie App”链接。</span><span class="sxs-lookup"><span data-stu-id="8691d-410">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="8691d-411">如果遇到类似于以下情况的异常：</span><span class="sxs-lookup"><span data-stu-id="8691d-411">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-412">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-413">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-413">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="8691d-414">可能缺失[迁移步骤](#migration)。</span><span class="sxs-lookup"><span data-stu-id="8691d-414">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="8691d-415">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="8691d-415">Test the **Create** page.</span></span> <span data-ttu-id="8691d-416">输入并提交数据。</span><span class="sxs-lookup"><span data-stu-id="8691d-416">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8691d-417">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="8691d-417">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8691d-418">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="8691d-418">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8691d-419">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="8691d-419">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8691d-420">测试“编辑”、“详细信息”和“删除”页  。</span><span class="sxs-lookup"><span data-stu-id="8691d-420">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="8691d-421">控制器中的依赖项注入</span><span class="sxs-lookup"><span data-stu-id="8691d-421">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-422">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-423">打开 Controllers/MoviesController.cs 文件并检查构造函数：</span><span class="sxs-lookup"><span data-stu-id="8691d-423">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8691d-424">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="8691d-424">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8691d-425">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="8691d-425">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-426">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-426">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8691d-427">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="8691d-427">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8691d-428">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="8691d-428">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="8691d-429">将 SQLite 用于开发，将 SQL Server 用于生产</span><span class="sxs-lookup"><span data-stu-id="8691d-429">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="8691d-430">选择 SQLite 后，模板生成的代码便可用于开发。</span><span class="sxs-lookup"><span data-stu-id="8691d-430">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="8691d-431">下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。</span><span class="sxs-lookup"><span data-stu-id="8691d-431">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="8691d-432">注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="8691d-432">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="8691d-433">强类型模型和 @model 关键词</span><span class="sxs-lookup"><span data-stu-id="8691d-433">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="8691d-434">在本教程之前的内容中，已经介绍了控制器如何使用 `ViewData` 字典将数据或对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="8691d-434">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="8691d-435">`ViewData` 字典是一个动态对象，提供了将信息传递给视图的方便的后期绑定方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-435">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="8691d-436">MVC 还提供将强类型模型对象传递给视图的功能。</span><span class="sxs-lookup"><span data-stu-id="8691d-436">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="8691d-437">此强类型方法启用编译时代码检查。</span><span class="sxs-lookup"><span data-stu-id="8691d-437">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="8691d-438">基架机制通过 `MoviesController` 类和视图使用了此方法（即传递强类型模型）。</span><span class="sxs-lookup"><span data-stu-id="8691d-438">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="8691d-439">检查 Controllers/MoviesController.cs 文件中生成的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="8691d-439">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="8691d-440">`id` 参数通常作为路由数据传递。</span><span class="sxs-lookup"><span data-stu-id="8691d-440">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="8691d-441">例如 `https://localhost:5001/movies/details/1` 的设置如下：</span><span class="sxs-lookup"><span data-stu-id="8691d-441">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="8691d-442">控制器被设置为 `movies` 控制器（第一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-442">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="8691d-443">操作被设置为 `details`（第二个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-443">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="8691d-444">ID 被设置为 1（最后一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-444">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="8691d-445">还可以使用查询字符串传入 `id`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="8691d-445">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="8691d-446">在未提供 ID 值的情况下，`id` 参数可定义为[可以为 null 的类型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`)。</span><span class="sxs-lookup"><span data-stu-id="8691d-446">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="8691d-447">[Lambda 表达式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)会被传入 `FirstOrDefaultAsync` 以选择与路由数据或查询字符串值相匹配的电影实体。</span><span class="sxs-lookup"><span data-stu-id="8691d-447">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="8691d-448">如果找到了电影，`Movie` 模型的实例则会被传递到 `Details` 视图：</span><span class="sxs-lookup"><span data-stu-id="8691d-448">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="8691d-449">检查 Views/Movies/Details.cshtml 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="8691d-449">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="8691d-450">视图文件顶部的 `@model` 语句可指定视图期望的对象类型。</span><span class="sxs-lookup"><span data-stu-id="8691d-450">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="8691d-451">创建影片控制器时，将包含以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="8691d-451">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="8691d-452">此 `@model` 指令允许访问控制器传递给视图的影片。</span><span class="sxs-lookup"><span data-stu-id="8691d-452">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="8691d-453">`Model` 对象为强类型对象。</span><span class="sxs-lookup"><span data-stu-id="8691d-453">The `Model` object is strongly typed.</span></span> <span data-ttu-id="8691d-454">例如，在 Details.cshtml 视图中，代码通过强类型的 `Model` 对象将每个电影字段传递给 `DisplayNameFor` 和 `DisplayFor`HTML 帮助程序。</span><span class="sxs-lookup"><span data-stu-id="8691d-454">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="8691d-455">`Create` 和 `Edit` 方法以及视图也传递一个 `Movie` 模型对象。</span><span class="sxs-lookup"><span data-stu-id="8691d-455">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="8691d-456">检查电影控制器中的 Index.cshtml 视图和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-456">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="8691d-457">请注意代码在调用 `View` 方法时是如何创建 `List` 对象的。</span><span class="sxs-lookup"><span data-stu-id="8691d-457">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="8691d-458">代码将此 `Movies` 列表从 `Index` 操作方法传递给视图：</span><span class="sxs-lookup"><span data-stu-id="8691d-458">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="8691d-459">创建影片控制器后，基架将以下 `@model` 语句包含在 Index.cshtml 文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="8691d-459">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="8691d-460">`@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影列表。</span><span class="sxs-lookup"><span data-stu-id="8691d-460">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8691d-461">例如，在 Index.cshtml 视图中，代码使用 `foreach` 语句通过强类型 `Model` 对象对电影进行循环遍历：</span><span class="sxs-lookup"><span data-stu-id="8691d-461">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="8691d-462">因为 `Model` 对象为强类型（作为 `IEnumerable<Movie>` 对象），因此循环中的每个项都被类型化为 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="8691d-462">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="8691d-463">除其他优点之外，这意味着可对代码进行编译时检查。</span><span class="sxs-lookup"><span data-stu-id="8691d-463">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8691d-464">其他资源</span><span class="sxs-lookup"><span data-stu-id="8691d-464">Additional resources</span></span>

* [<span data-ttu-id="8691d-465">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="8691d-465">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="8691d-466">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="8691d-466">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="8691d-467">[上一篇：添加视图](adding-view.md)
> [下一篇：使用 SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="8691d-467">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="8691d-468">添加数据模型类</span><span class="sxs-lookup"><span data-stu-id="8691d-468">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-470">右键单击 Models 文件夹，然后单击“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="8691d-470">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="8691d-471">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="8691d-471">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-472">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-472">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8691d-473">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="8691d-473">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8691d-474">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="8691d-474">Scaffold the movie model</span></span>

<span data-ttu-id="8691d-475">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="8691d-475">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8691d-476">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="8691d-476">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-477">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-477">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-478">在解决方案资源管理器中，右键单击“Controllers”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="8691d-478">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![上述步骤的视图](adding-model/_static/add_controller21.png)

<span data-ttu-id="8691d-480">在“添加基架”对话框中，选择“包含视图的 MVC 控制器(使用 Entity Framework)”>“添加” 。</span><span class="sxs-lookup"><span data-stu-id="8691d-480">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![“添加基架”对话框](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="8691d-482">填写“添加控制器”对话框：</span><span class="sxs-lookup"><span data-stu-id="8691d-482">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="8691d-483">模型类：Movie (MvcMovie.Models)</span><span class="sxs-lookup"><span data-stu-id="8691d-483">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="8691d-484">数据上下文类：选择 + 图标并添加默认的 MvcMovie.Models.MvcMovieContext </span><span class="sxs-lookup"><span data-stu-id="8691d-484">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![“添加数据”上下文](adding-model/_static/dc.png)

* <span data-ttu-id="8691d-486">视图：将每个选项保持为默认选中状态</span><span class="sxs-lookup"><span data-stu-id="8691d-486">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="8691d-487">控制器名称：保留默认的 MoviesController</span><span class="sxs-lookup"><span data-stu-id="8691d-487">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="8691d-488">选择“添加”</span><span class="sxs-lookup"><span data-stu-id="8691d-488">Select **Add**</span></span>

![“添加控制器”对话框](adding-model/_static/add_controller2.png)

<span data-ttu-id="8691d-490">Visual Studio 将创建：</span><span class="sxs-lookup"><span data-stu-id="8691d-490">Visual Studio creates:</span></span>

* <span data-ttu-id="8691d-491">Entity Framework Core [数据库上下文类](xref:data/ef-mvc/intro#create-the-database-context) (Data/MvcMovieContext.cs)</span><span class="sxs-lookup"><span data-stu-id="8691d-491">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="8691d-492">电影控制器 (Controllers/MoviesController.cs)</span><span class="sxs-lookup"><span data-stu-id="8691d-492">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="8691d-493">“创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)</span><span class="sxs-lookup"><span data-stu-id="8691d-493">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="8691d-494">自动创建数据库上下文和 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)（创建、读取、更新和删除）操作方法和视图的过程称为“搭建基架”。</span><span class="sxs-lookup"><span data-stu-id="8691d-494">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8691d-495">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8691d-495">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="8691d-496">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="8691d-496">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8691d-497">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="8691d-497">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8691d-498">在 Linux 上，导出基架工具路径：</span><span class="sxs-lookup"><span data-stu-id="8691d-498">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="8691d-499">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-499">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8691d-500">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-500">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8691d-501">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="8691d-501">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8691d-502">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="8691d-502">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8691d-503">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-503">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="8691d-504">如果运行应用并单击“Mvc 电影”链接，则会出现以下类似的错误：</span><span class="sxs-lookup"><span data-stu-id="8691d-504">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-505">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-506">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-506">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="8691d-507">你需要创建数据库，并且使用 EF Core [迁移](xref:data/ef-mvc/migrations)功能来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="8691d-507">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="8691d-508">通过迁移可创建与数据模型匹配的数据库，并在数据模型更改时更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="8691d-508">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8691d-509">初始迁移</span><span class="sxs-lookup"><span data-stu-id="8691d-509">Initial migration</span></span>

<span data-ttu-id="8691d-510">在本节中，将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="8691d-510">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="8691d-511">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="8691d-511">Add an initial migration.</span></span>
* <span data-ttu-id="8691d-512">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="8691d-512">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-513">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-513">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8691d-514">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”(PMC)。 </span><span class="sxs-lookup"><span data-stu-id="8691d-514">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC 菜单](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="8691d-516">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="8691d-516">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="8691d-517">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-517">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="8691d-518">数据库架构基于在 `MvcMovieContext` 类中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="8691d-518">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="8691d-519">`Initial` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-519">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="8691d-520">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-520">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="8691d-521">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="8691d-521">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="8691d-522">`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-522">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-523">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-523">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="8691d-524">`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-524">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="8691d-525">数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="8691d-525">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="8691d-526">`InitialCreate` 参数是迁移名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-526">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8691d-527">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="8691d-527">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8691d-528">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="8691d-528">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8691d-529">ASP.NET Core 通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 生成。</span><span class="sxs-lookup"><span data-stu-id="8691d-529">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8691d-530">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过 DI 注册。</span><span class="sxs-lookup"><span data-stu-id="8691d-530">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="8691d-531">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="8691d-531">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8691d-532">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="8691d-532">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8691d-533">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8691d-533">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8691d-534">基架工具自动创建数据库上下文并将其注册到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="8691d-534">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="8691d-535">我们来研究以下 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-535">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8691d-536">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="8691d-536">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="8691d-537">`MvcMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="8691d-537">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8691d-538">数据上下文 (`MvcMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="8691d-538">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8691d-539">数据上下文指定数据模型中包含哪些实体：</span><span class="sxs-lookup"><span data-stu-id="8691d-539">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="8691d-540">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="8691d-540">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8691d-541">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="8691d-541">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8691d-542">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="8691d-542">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8691d-543">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="8691d-543">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8691d-544">进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8691d-544">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8691d-545">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8691d-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8691d-546">创建了数据库上下文并将其注册到了 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="8691d-546">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8691d-547">测试应用</span><span class="sxs-lookup"><span data-stu-id="8691d-547">Test the app</span></span>

* <span data-ttu-id="8691d-548">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="8691d-548">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8691d-549">如果收到如下所示数据库异常：</span><span class="sxs-lookup"><span data-stu-id="8691d-549">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8691d-550">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="8691d-550">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8691d-551">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="8691d-551">Test the **Create** link.</span></span> <span data-ttu-id="8691d-552">输入并提交数据。</span><span class="sxs-lookup"><span data-stu-id="8691d-552">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8691d-553">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="8691d-553">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8691d-554">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="8691d-554">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8691d-555">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="8691d-555">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8691d-556">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="8691d-556">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="8691d-557">检查 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="8691d-557">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="8691d-558">上面突出显示的代码显示了要添加到[依赖关系注入](xref:fundamentals/dependency-injection)容器的电影数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="8691d-558">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="8691d-559">`services.AddDbContext<MvcMovieContext>(options =>` 指定要使用的数据库和连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8691d-559">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="8691d-560">`=>` 是 [lambda 运算符](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="8691d-560">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="8691d-561">打开 Controllers/MoviesController.cs 文件并检查构造函数：</span><span class="sxs-lookup"><span data-stu-id="8691d-561">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8691d-562">构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="8691d-562">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8691d-563">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="8691d-563">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="8691d-564">强类型模型和 @model 关键词</span><span class="sxs-lookup"><span data-stu-id="8691d-564">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="8691d-565">在本教程之前的内容中，已经介绍了控制器如何使用 `ViewData` 字典将数据或对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="8691d-565">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="8691d-566">`ViewData` 字典是一个动态对象，提供了将信息传递给视图的方便的后期绑定方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-566">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="8691d-567">MVC 还提供将强类型模型对象传递给视图的功能。</span><span class="sxs-lookup"><span data-stu-id="8691d-567">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="8691d-568">凭借此强类型方法可更好地对代码进行编译时检查。</span><span class="sxs-lookup"><span data-stu-id="8691d-568">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="8691d-569">基架机制在创建方法和视图时，通过 `MoviesController` 类和视图使用了此方法（即传递强类型模型）。</span><span class="sxs-lookup"><span data-stu-id="8691d-569">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="8691d-570">检查 Controllers/MoviesController.cs 文件中生成的 `Details` 方法：</span><span class="sxs-lookup"><span data-stu-id="8691d-570">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="8691d-571">`id` 参数通常作为路由数据传递。</span><span class="sxs-lookup"><span data-stu-id="8691d-571">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="8691d-572">例如 `https://localhost:5001/movies/details/1` 的设置如下：</span><span class="sxs-lookup"><span data-stu-id="8691d-572">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="8691d-573">控制器被设置为 `movies` 控制器（第一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-573">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="8691d-574">操作被设置为 `details`（第二个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-574">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="8691d-575">ID 被设置为 1（最后一个 URL 段）。</span><span class="sxs-lookup"><span data-stu-id="8691d-575">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="8691d-576">还可以使用查询字符串传入 `id`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="8691d-576">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="8691d-577">在未提供 ID 值的情况下，`id` 参数可定义为[可以为 null 的类型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`)。</span><span class="sxs-lookup"><span data-stu-id="8691d-577">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="8691d-578">[Lambda 表达式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)会被传入 `FirstOrDefaultAsync` 以选择与路由数据或查询字符串值相匹配的电影实体。</span><span class="sxs-lookup"><span data-stu-id="8691d-578">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="8691d-579">如果找到了电影，`Movie` 模型的实例则会被传递到 `Details` 视图：</span><span class="sxs-lookup"><span data-stu-id="8691d-579">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="8691d-580">检查 Views/Movies/Details.cshtml 文件的内容：</span><span class="sxs-lookup"><span data-stu-id="8691d-580">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="8691d-581">通过将 `@model` 语句包括在视图文件的顶端，可以指定视图期望的对象类型。</span><span class="sxs-lookup"><span data-stu-id="8691d-581">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="8691d-582">创建电影控制器时，会自动在 Details.cshtml 文件的顶端包括以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="8691d-582">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="8691d-583">此 `@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影。</span><span class="sxs-lookup"><span data-stu-id="8691d-583">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8691d-584">例如，在 Details.cshtml 视图中，代码通过强类型的 `Model` 对象将每个电影字段传递给 `DisplayNameFor` 和 `DisplayFor`HTML 帮助程序。</span><span class="sxs-lookup"><span data-stu-id="8691d-584">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="8691d-585">`Create` 和 `Edit` 方法以及视图也传递一个 `Movie` 模型对象。</span><span class="sxs-lookup"><span data-stu-id="8691d-585">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="8691d-586">检查电影控制器中的 Index.cshtml 视图和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="8691d-586">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="8691d-587">请注意代码在调用 `View` 方法时是如何创建 `List` 对象的。</span><span class="sxs-lookup"><span data-stu-id="8691d-587">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="8691d-588">代码将此 `Movies` 列表从 `Index` 操作方法传递给视图：</span><span class="sxs-lookup"><span data-stu-id="8691d-588">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="8691d-589">创建电影控制器时，基架会自动在 Index.cshtml 文件的顶端包含以下 `@model` 语句：</span><span class="sxs-lookup"><span data-stu-id="8691d-589">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="8691d-590">`@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影列表。</span><span class="sxs-lookup"><span data-stu-id="8691d-590">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8691d-591">例如，在 Index.cshtml 视图中，代码使用 `foreach` 语句通过强类型 `Model` 对象对电影进行循环遍历：</span><span class="sxs-lookup"><span data-stu-id="8691d-591">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="8691d-592">因为 `Model` 对象为强类型（作为 `IEnumerable<Movie>` 对象），因此循环中的每个项都被类型化为 `Movie`。</span><span class="sxs-lookup"><span data-stu-id="8691d-592">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="8691d-593">除其他优点之外，这意味着可对代码进行编译时检查：</span><span class="sxs-lookup"><span data-stu-id="8691d-593">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8691d-594">其他资源</span><span class="sxs-lookup"><span data-stu-id="8691d-594">Additional resources</span></span>

* [<span data-ttu-id="8691d-595">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="8691d-595">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="8691d-596">全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="8691d-596">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="8691d-597">[上一篇：添加视图](adding-view.md)
> [下一篇：使用数据库](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="8691d-597">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
