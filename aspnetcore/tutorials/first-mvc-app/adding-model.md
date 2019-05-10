---
title: 将模型添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: 将模型添加到简单的 ASP.NET Core 应用。
ms.author: riande
ms.date: 02/25/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 0580c2cb7717a89d0d16cd3f431a63db78fa27cb
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887422"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>将模型添加到 ASP.NET Core MVC 应用

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Tom Dykstra](https://github.com/tdykstra)

在本部分中将添加用于管理数据库中的电影的类。 这些类将是 MVC 应用的“Model”部分。

可以结合 [Entity Framework Core](/ef/core) (EF Core) 使用这些类来处理数据库。 EF Core 是对象关系映射 (ORM) 框架，可以简化需要编写的数据访问代码。

要创建的模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。 它们只定义将存储在数据库中的数据的属性。

在本教程中，首先要编写模型类，然后 EF Core 将创建数据库。 有一种备选方法（此处未介绍）：从现有数据库生成模型类。 有关此方法的信息，请参阅 [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db)（ASP.NET Core - 现有数据库）。

## <a name="add-a-data-model-class"></a>添加数据模型类

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

右键单击 Models 文件夹，然后单击“添加” > “类”。 将类命名“Movie”。

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* 将类添加到名为“Movie.cs”的“Models”文件夹。

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>搭建“电影”模型的基架

在此部分，将搭建“电影”模型的基架。 确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在解决方案资源管理器中，右键单击“Controllers”文件夹 >“添加”>“新搭建基架的项目”。

![上述步骤的视图](adding-model/_static/add_controller21.png)

在“添加基架”对话框中，选择“包含视图的 MVC 控制器(使用 Entity Framework)”>“添加”。

![“添加基架”对话框](adding-model/_static/add_scaffold21.png)

填写“添加控制器”对话框：

* 模型类：Movie (MvcMovie.Models)
* 数据上下文类：选择 + 图标并添加默认的 MvcMovie.Models.MvcMovieContext

![“添加数据”上下文](adding-model/_static/dc.png)

* 视图：将每个选项保持为默认选中状态
* 控制器名称：保留默认的 MoviesController
* 选择“添加”

![“添加控制器”对话框](adding-model/_static/add_controller2.png)

Visual Studio 将创建：

* Entity Framework Core [数据库上下文类](xref:data/ef-mvc/intro#create-the-database-context) (Data/MvcMovieContext.cs)
* 电影控制器 (Controllers/MoviesController.cs)
* “创建”、“删除”、“详细信息”、“编辑”和“索引”页面的 Razor 视图文件 (Views/Movies/\*.cshtml)

自动创建数据库上下文和 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)（创建、读取、更新和删除）操作方法和视图的过程称为“搭建基架”。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 安装基架工具：

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* 运行下面的命令：

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 安装基架工具：

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* 运行下面的命令：

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<!-- End of VS tabs                  -->

如果运行应用并单击“Mvc 电影”链接，则会出现以下类似的错误：

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

你需要创建数据库，并且使用 EF Core [迁移](xref:data/ef-mvc/migrations)功能来执行此操作。 通过迁移可创建与数据模型匹配的数据库，并在数据模型更改时更新数据库架构。

<a name="pmc"></a>

## <a name="initial-migration"></a>初始迁移

在本节中，将完成以下任务：

* 添加初始迁移。
* 使用初始迁移来更新数据库。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 从“工具”菜单中，选择“NuGet 包管理器” > “包管理器控制台”(PMC)。

   ![PMC 菜单](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. 在 PMC 中，输入以下命令：

   ```console
   Add-Migration Initial
   Update-Database
   ```

   `Add-Migration` 命令生成用于创建初始数据库架构的代码。

   数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。 `Initial` 参数是迁移名称。 可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。 有关更多信息，请参见<xref:data/ef-mvc/migrations>。

   `Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。

数据库架构基于在 `MvcMovieContext` 类中（位于 Data/MvcMovieContext.cs 文件中）中指定的模型。 `InitialCreate` 参数是迁移名称。 可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>检查通过依赖关系注入注册的上下文

ASP.NET Core 通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 生成。 服务（例如 EF Core 数据库上下文）在应用程序启动期间通过 DI 注册。 需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。 本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

基架工具自动创建数据库上下文并将其注册到 DI 容器。

我们来研究以下 `Startup.ConfigureServices` 方法。 基架添加了突出显示的行：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`MvcMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。 数据上下文 (`MvcMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。 数据上下文指定数据模型中包含哪些实体：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。 在实体框架术语中，实体集通常与数据表相对应。 实体对应表中的行。

通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。 进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

创建了数据库上下文并将其注册到了 DI 容器。

---

<a name="test"></a>

### <a name="test-the-app"></a>测试应用

* 运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。

如果收到如下所示数据库异常：

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

缺少[迁移步骤](#pmc)。

* 测试“创建”链接。 输入并提交数据。

  > [!NOTE]
  > 可能无法在 `Price` 字段中输入十进制逗号。 若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。 有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。

* 测试“编辑”、“详细信息”和“删除”链接。

检查 `Startup` 类：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

上面突出显示的代码显示了要添加到[依赖关系注入](xref:fundamentals/dependency-injection)容器的电影数据库上下文：

* `services.AddDbContext<MvcMovieContext>(options =>` 指定要使用的数据库和连接字符串。
* `=>` 是 [lambda 运算符](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

打开 Controllers/MoviesController.cs 文件并检查构造函数：

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将数据库上下文 (`MvcMovieContext`) 注入到控制器中。 数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>强类型模型和 @model 关键词

在本教程之前的内容中，已经介绍了控制器如何使用 `ViewData` 字典将数据或对象传递给视图。 `ViewData` 字典是一个动态对象，提供了将信息传递给视图的方便的后期绑定方法。

MVC 还提供将强类型模型对象传递给视图的功能。 凭借此强类型方法可更好地对代码进行编译时检查。 基架机制在创建方法和视图时，通过 `MoviesController` 类和视图使用了此方法（即传递强类型模型）。

检查 Controllers/MoviesController.cs 文件中生成的 `Details` 方法：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

`id` 参数通常作为路由数据传递。 例如 `https://localhost:5001/movies/details/1` 的设置如下：

* 控制器被设置为 `movies` 控制器（第一个 URL 段）。
* 操作被设置为 `details`（第二个 URL 段）。
* ID 被设置为 1（最后一个 URL 段）。

还可以使用查询字符串传入 `id`，如下所示：

`https://localhost:5001/movies/details?id=1`

在未提供 ID 值的情况下，`id` 参数可定义为[可以为 null 的类型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`)。

[Lambda 表达式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)会被传入 `FirstOrDefaultAsync` 以选择与路由数据或查询字符串值相匹配的电影实体。

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

如果找到了电影，`Movie` 模型的实例则会被传递到 `Details` 视图：

```csharp
return View(movie);
   ```

检查 Views/Movies/Details.cshtml 文件的内容：

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

通过将 `@model` 语句包括在视图文件的顶端，可以指定视图期望的对象类型。 创建电影控制器时，会自动在 Details.cshtml 文件的顶端包括以下 `@model` 语句：

```HTML
@model MvcMovie.Models.Movie
   ```

此 `@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影。 例如，在 Details.cshtml 视图中，代码通过强类型的 `Model` 对象将每个电影字段传递给 `DisplayNameFor` 和 `DisplayFor`HTML 帮助程序。 `Create` 和 `Edit` 方法以及视图也传递一个 `Movie` 模型对象。

检查电影控制器中的 Index.cshtml 视图和 `Index` 方法。 请注意代码在调用 `View` 方法时是如何创建 `List` 对象的。 代码将此 `Movies` 列表从 `Index` 操作方法传递给视图：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

创建电影控制器时，基架会自动在 Index.cshtml 文件的顶端包含以下 `@model` 语句：

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` 指令使你能够使用强类型的 `Model` 对象访问控制器传递给视图的电影列表。 例如，在 Index.cshtml 视图中，代码使用 `foreach` 语句通过强类型 `Model` 对象对电影进行循环遍历：

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

因为 `Model` 对象为强类型（作为 `IEnumerable<Movie>` 对象），因此循环中的每个项都被类型化为 `Movie`。 除其他优点之外，这意味着可对代码进行编译时检查：

## <a name="additional-resources"></a>其他资源

* [标记帮助程序](xref:mvc/views/tag-helpers/intro)
* [全球化和本地化](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [上一篇：添加视图](adding-view.md)
> [下一篇：使用 SQL](working-with-sql.md)
