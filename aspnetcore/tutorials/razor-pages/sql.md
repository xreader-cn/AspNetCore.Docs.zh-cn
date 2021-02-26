---
title: 第 4 部分：使用数据库
author: rick-anderson
description: Razor 页面教程系列的第 4 部分。
ms.author: riande
ms.date: 01/05/2021
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: fa1060ae1a046a40d55e9fef4a094aa9e51a18af
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101166"
---
# <a name="part-4-of-tutorial-series-on-razor-pages"></a>Razor 页面教程系列第 4 部分

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。

`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。 在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。 进行本地开发时，配置从 appsettings.json 文件获取连接字符串。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

生成的连接字符串类似于以下 JSON：

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。 有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。 LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。 默认情况下，LocalDB 数据库在 `C:\Users\<user>\` 目录下创建 `*.mdf` 文件。

<a name="ssox"></a>
1. 从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。

   ![“视图”菜单](sql/_static/5/ssox.png)

1. 右键单击 `Movie` 表，然后选择“视图设计器”：

   ![Movie 表上打开的上下文菜单](sql/_static/5/design.png)

   ![设计器中打开的 Movie 表](sql/_static/dv.png)

   请注意 `ID` 旁边的密钥图标。 默认情况下，EF 为该主键创建一个名为 `ID` 的属性。

1. 右键单击 `Movie` 表，然后选择“查看数据”：

   ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) 网站上表示：

> SQLite 是一个自包含、高可靠性、嵌入式、功能完整、公共域的 SQL 数据库引擎。 SQLite 是世界上使用最多的数据库引擎。

可以下载许多第三方工具来管理并查看 SQLite 数据库。 下面的图片来自 [DB Browser for SQLite](https://sqlitebrowser.org/)。 如果你有最喜欢的 SQLite 工具，请发表评论以分享你喜欢的方面。

![显示电影数据库的 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> 在本教程中，使用 Entity Framework Core 迁移功能（若可行）。 迁移会更新数据库架构，使其与数据模型中的更改相匹配。 但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。 例如，支持添加列，但不支持删除或更改列。 如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。 由于上述限制，本教程不对 SQLite 架构更改使用迁移。 转而在架构更改时，放弃并重新创建数据库。
>
>要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。 表重新生成涉及：
>
>* 创建新表。
>* 将旧表中的数据复制到新表中。
>* 放弃旧表。
>* 为新表重命名。
>
>有关更多信息，请参见以下资源：
> * [SQLite EF Core 数据库提供程序限制](/ef/core/providers/sqlite/limitations)
> * [自定义迁移代码](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [数据种子设定](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 语句](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>设定数据库种子

使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>添加种子初始值设定项

将 Program.cs 的内容替换为以下代码：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

前面的代码修改了 `Main` 方法，以便执行以下操作：

* 从依赖注入容器中获取数据库上下文实例。
* 调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。
* Seed 方法完成时释放上下文。 [using 语句](/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。

未运行 `Update-Database` 时出现以下异常：

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>测试应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 删除数据库中的所有记录。 使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作

1. 通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。 若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。 使用以下任一方法停止并重启 IIS：

   1. 右键单击通知区域中的 IIS Express 系统任务栏图标，然后选择“退出”或“停止站点”：

      ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![上下文菜单](sql/_static/stopIIS.png)

   1. 如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。
   1. 如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

删除数据库中的所有记录，使种子方法运行。 停止并启动应用以设定数据库种子。

---

应用将显示设定为种子的数据：

![在浏览器中打开的显示电影数据的电影应用程序](sql/_static/5/m55.png)

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。

`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。 在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。 进行本地开发时，配置从 appsettings.json 文件获取连接字符串。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

生成的连接字符串将类似于以下内容：

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。 有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。 LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。 默认情况下，LocalDB 数据库在 `C:\Users\<user>\` 目录下创建 `*.mdf` 文件。

<a name="ssox"></a>
* 从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。

  ![“视图”菜单](sql/_static/ssox.png)

* 右键单击 `Movie` 表，然后选择“视图设计器”：

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

请注意 `ID` 旁边的密钥图标。 默认情况下，EF 为该主键创建一个名为 `ID` 的属性。

* 右键单击 `Movie` 表，然后选择“查看数据”：

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) 网站上表示：

> SQLite 是一个自包含、高可靠性、嵌入式、功能完整、公共域的 SQL 数据库引擎。 SQLite 是世界上使用最多的数据库引擎。

可以下载许多第三方工具来管理并查看 SQLite 数据库。 下面的图片来自 [DB Browser for SQLite](https://sqlitebrowser.org/)。 如果你有最喜欢的 SQLite 工具，请发表评论以分享你喜欢的方面。

![显示电影数据库的 DB Browser for SQLite](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> 在本教程中，使用 Entity Framework Core 迁移功能（若可行）。 迁移会更新数据库架构，使其与数据模型中的更改相匹配。 但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。 例如，支持添加列，但不支持删除或更改列。 如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。 由于上述限制，本教程不对 SQLite 架构更改使用迁移。 转而在架构更改时，放弃并重新创建数据库。
>
>要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。 表重新生成涉及：
>
>* 创建新表。
>* 将旧表中的数据复制到新表中。
>* 放弃旧表。
>* 为新表重命名。
>
>有关更多信息，请参见以下资源：
> * [SQLite EF Core 数据库提供程序限制](/ef/core/providers/sqlite/limitations)
> * [自定义迁移代码](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [数据种子设定](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 语句](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>设定数据库种子

使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>添加种子初始值设定项

将 Program.cs 的内容替换为以下代码：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

前面的代码修改了 `Main` 方法，以便执行以下操作：

* 从依赖注入容器中获取数据库上下文实例。
* 调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。
* Seed 方法完成时释放上下文。 [using 语句](/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。

未运行 `Update-Database` 时出现以下异常：

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>测试应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 删除数据库中的所有记录。 使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作。
* 通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。 若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。 使用以下任一方法停止并重启 IIS：

  * 右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * 如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。
    * 如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

删除数据库中的所有记录，使种子方法运行。 停止并启动应用以设定数据库种子。

---

应用将显示设定为种子的数据：

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55https.png)

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。

`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。 在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

有关 `ConfigureServices` 中使用的方法的详细信息，请参阅：

* 面向 `CookiePolicyOptions` 的 [ASP.NET Core 中的欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString` 键。 进行本地开发时，配置从 appsettings.json 文件获取连接字符串。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

生成的连接字符串将类似于以下内容：

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为测试或生产数据库服务器。 有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。 LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。 默认情况下，LocalDB 数据库在 `C:/Users/<user/>` 目录下创建 `*.mdf` 文件。

<a name="ssox"></a>
* 从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。

  ![“视图”菜单](sql/_static/ssox.png)

* 右键单击 `Movie` 表，然后选择“视图设计器”：

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

请注意 `ID` 旁边的密钥图标。 默认情况下，EF 为该主键创建一个名为 `ID` 的属性。

* 右键单击 `Movie` 表，然后选择“查看数据”：

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>设定数据库种子

使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

如果数据库中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>添加种子初始值设定项

将 Program.cs 的内容替换为以下代码：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

前面的代码修改了 `Main` 方法，以便执行以下操作：

* 从依赖注入容器中获取数据库上下文实例。
* 调用 `seedData.Initialize` 方法，并向其传递数据库上下文实例。
* Seed 方法完成时释放上下文。 [using 语句](/dotnet/csharp/language-reference/keywords/using-statement)将确保释放上下文。

生产应用不会调用 `Database.Migrate`。 它会添加到上面的代码中，以防止在未运行 `Update-Database` 时出现以下异常：

SqlException：无法打开登录请求的数据库“RazorPagesMovieContext-21”。 登录失败。
用户“用户名”登录失败。

### <a name="test-the-app"></a>测试应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 删除数据库中的所有记录。 可以使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作
* 通过调用 `Startup` 类中的方法强制应用初始化，使种子方法能够正常运行。 若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。 可以使用以下任一方法来执行此操作：

  * 右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * 如果应用是在非调试模式下运行的，请按 F5<kbd></kbd> 以在调试模式下运行。
    * 如果应用处于调试模式下，请停止调试程序并按 F5<kbd></kbd>。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

删除数据库中的所有记录，使种子方法运行。 停止并启动应用以设定数据库种子。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

删除数据库中的所有记录，使种子方法运行。 停止并启动应用以设定数据库种子。

---

应用将显示设定为种子的数据：

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55https.png)

在下一教程中将对数据的展示进行整理。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)

::: moniker-end