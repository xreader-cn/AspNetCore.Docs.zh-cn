---
title: 在 ASP.NET Core 中向 Razor Pages 应用添加模型
author: rick-anderson
description: 了解如何使用 Entity Framework Core (EF Core) 添加用于管理数据库中的影片的类。
ms.author: riande
ms.date: 02/12/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: b4f14241d50383ad4d1c1d8c9d00ca439f371b7d
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265021"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>在 ASP.NET Core 中向 Razor Pages 应用添加模型

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

在本节中，添加了用于管理数据库中的电影的类。 这些类与 [Entity Framework Core](/ef/core)（EF Core）一起使用来处理数据库。 EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问代码。

模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。 它们定义数据库中存储的数据属性。

[查看或下载](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)示例。

## <a name="add-a-data-model"></a>添加数据模型

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。 将文件夹命名为“Models”。

右键单击“Models”文件夹。 选择“添加” > “类”。 将类命名“Movie”。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 添加名为“Models”的文件夹。
* 将类添加到名为“Movie.cs”的“Models”文件夹。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在解决方案资源管理器中，右键单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。 将文件夹命名为“Models”。
* 右键单击“Models”文件夹，然后选择“添加” > “新建文件”。
* 在“新建文件”对话框中：

  * 在左侧窗格中，选择“常规”。
  * 在中间窗格中，选择“空类”。
  * 将此类命名为“Movie”，然后选择“新建”。

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

生成项目以验证没有任何编译错误。

## <a name="scaffold-the-movie-model"></a>搭建“电影”模型的基架

在此部分，将搭建“电影”模型的基架。 确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

创建“Pages/Movies”文件夹：

* 右键单击 Pages 文件夹 >“添加” > “新建文件夹”。
* 将文件夹命名为“Movies”

右键单击 Pages/Movies 文件夹 >“添加” > “新搭建基架的项目”。

![上述说明的图像。](model/_static/sca.png)

在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)” > “添加”。

![上述说明的图像。](model/_static/add_scaffold.png)

完成“使用实体框架(CRUD)添加 Razor Pages”对话框：

* 在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)。
* 在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”。
* 选择“添加”。

![上述说明的图像。](model/_static/arp.png)

appsettings.json 文件通过用于连接到本地数据的连接字符串进行更新。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 安装基架工具：

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **对于 Windows**：运行下面的命令：

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **对于 macOS 和 Linux**：运行下面的命令：

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。
* 安装基架工具：

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* 运行下面的命令：

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

在搭建基架时，会创建并更新以下文件：

### <a name="files-created"></a>创建的文件

* *Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。
* Data/RazorPagesMovieContext.cs

### <a name="file-updated"></a>文件已更新

* *Startup.cs*

创建和更新的文件将在下一节中说明。

<a name="pmc"></a>

## <a name="initial-migration"></a>初始迁移

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在此部分中，程序包管理器控制台 (PMC) 用于：

* 添加初始迁移。
* 使用初始迁移来更新数据库。

从“工具”菜单中，选择“NuGet 包管理器” > “包管理器控制台”。

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

在 PMC 中，输入以下命令：

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”

你可以忽略该警告，它将后面的教程中得到修复。

`ef migrations add InitialCreate` 命令生成用于创建初始数据库架构的代码。 此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。 `InitialCreate` 参数用于为迁移命名。 可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。

`ef database update` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。 `Up` 方法会创建数据库。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>检查通过依赖关系注入注册的上下文

ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。 服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。 需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。 本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。

基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。

检查 `Startup.ConfigureServices` 方法。 基架添加了突出显示的行：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。 数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。 数据上下文指定数据模型中包含哪些实体。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

前面的代码为实体集创建 [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。 在实体框架术语中，实体集通常与数据表相对应。 实体对应表中的行。

通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。 进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

---

`Add-Migration` 命令生成用于创建初始数据库架构的代码。 此架构的依据为 `RazorPagesMovieContext` 中指定的模型（在 Data/RazorPagesMovieContext.cs 文件中）。 `Initial` 参数用于为迁移命名。 可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。 有关更多信息，请参见<xref:data/ef-mvc/migrations>。

`Update-Database` 命令在用于创建数据库的 Migrations/{time-stamp}_InitialCreate.cs 文件中运行 `Up` 方法。

<a name="test"></a>

### <a name="test-the-app"></a>测试应用

* 运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。

如果收到错误：

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

缺少[迁移步骤](#pmc)。

* 测试“创建”链接。

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > 可能无法在 `Price` 字段中输入十进制逗号。 若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。 有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420)。

* 测试“编辑”、“详细信息”和“删除”链接。

下一个教程介绍由基架创建的文件。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://www.youtube.com/watch?v=sFVIsdR_RcM)

> [!div class="step-by-step"]
> [上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：已搭建基架的 Razor Pages](xref:tutorials/razor-pages/page)
