---
title: 第 4 部分，使用数据库和 ASP.NET Core
author: rick-anderson
description: Razor 页面教程系列的第 4 部分。
ms.author: riande
ms.date: 7/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 68764a5b8c908012cc0053aa4e3ea071f2ce423d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400903"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a>第 4 部分，使用数据库和 ASP.NET Core

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。 在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。 为了进行本地开发，它会从 appsettings.json 文件获取连接字符串。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

生成代码的数据库名称值 (`Database={Database name}`) 将并不不同。 名称值是任意的。

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为实际的数据库服务器。 有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。

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

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>设定数据库种子

使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

如果 DB 中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>添加种子初始值设定项

在 Program.cs 中，修改 `Main` 方法以执行以下操作：

* 从依赖关系注入容器获取数据库上下文实例。
* 调用 seed 方法，并将上下文传递给它。
* Seed 方法完成时释放上下文。

下面的代码显示更新后的 Program.cs 文件。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

未运行 `Update-Database` 时出现以下异常：

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>测试应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 删除 DB 中的所有记录。 可以使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作
* 强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。 若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。 可以使用以下任一方法来执行此操作：

  * 右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * 如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行。
    * 如果是在调试模式下运行 VS 的，请停止调试程序并按 F5。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

删除 DB 中的所有记录（使种子方法运行）。 停止并启动应用以设定数据库种子。

应用将显示设定为种子的数据。

---

在下一教程中将对数据的展示进行改进。

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。 在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

有关 `ConfigureServices` 中使用的方法的详细信息，请参阅：

* 面向 `CookiePolicyOptions` 的 [ASP.NET Core 中的欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。 为了进行本地开发，它会从 appsettings.json 文件获取连接字符串。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

生成代码的数据库名称值 (`Database={Database name}`) 将并不不同。 名称值是任意的。

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为实际的数据库服务器。 有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。

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

如果 DB 中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>添加种子初始值设定项

在 Program.cs 中，修改 `Main` 方法以执行以下操作：

* 从依赖关系注入容器获取数据库上下文实例。
* 调用 seed 方法，并将上下文传递给它。
* Seed 方法完成时释放上下文。

下面的代码显示更新后的 Program.cs 文件。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

生产应用不会调用 `Database.Migrate`。 它会添加到上面的代码中，以防止在未运行 `Update-Database` 时出现以下异常：

SqlException：无法打开登录请求的数据库“RazorPagesMovieContext-21”。 登录失败。
用户“用户名”登录失败。

### <a name="test-the-app"></a>测试应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 删除 DB 中的所有记录。 可以使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作
* 强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。 若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。 可以使用以下任一方法来执行此操作：

  * 右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * 如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行。
    * 如果是在调试模式下运行 VS 的，请停止调试程序并按 F5。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

删除 DB 中的所有记录（使种子方法运行）。 停止并启动应用以设定数据库种子。

应用将显示设定为种子的数据。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

删除 DB 中的所有记录（使种子方法运行）。 停止并启动应用以设定数据库种子。

应用将显示设定为种子的数据。

---

应用将显示设定为种子的数据：

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55.png)

在下一教程中将对数据的展示进行整理。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)

::: moniker-end
