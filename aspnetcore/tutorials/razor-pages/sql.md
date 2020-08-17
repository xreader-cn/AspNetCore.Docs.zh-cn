---
title: 第 4 部分，使用数据库和 ASP.NET Core
author: rick-anderson
description: Razor 页面教程系列的第 4 部分。
ms.author: riande
ms.date: 7/22/2019
no-loc:
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
ms.openlocfilehash: 6d1a93a9cab49d33181e88b7fdc0f203a1e2546c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022415"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="96fb6-103">第 4 部分，使用数据库和 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="96fb6-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="96fb6-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="96fb6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="96fb6-105">`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="96fb6-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="96fb6-106">在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="96fb6-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96fb6-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96fb6-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96fb6-108">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="96fb6-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="96fb6-109">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="96fb6-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="96fb6-110">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="96fb6-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96fb6-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96fb6-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96fb6-112">生成代码的数据库名称值 (`Database={Database name}`) 将并不不同。</span><span class="sxs-lookup"><span data-stu-id="96fb6-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="96fb6-113">名称值是任意的。</span><span class="sxs-lookup"><span data-stu-id="96fb6-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96fb6-114">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="96fb6-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="96fb6-115">将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为实际的数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="96fb6-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="96fb6-116">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="96fb6-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96fb6-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96fb6-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="96fb6-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="96fb6-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="96fb6-119">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="96fb6-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="96fb6-120">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="96fb6-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="96fb6-121">默认情况下，LocalDB 数据库在 `C:\Users\<user>\` 目录下创建 `*.mdf` 文件。</span><span class="sxs-lookup"><span data-stu-id="96fb6-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="96fb6-122">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="96fb6-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](sql/_static/ssox.png)

* <span data-ttu-id="96fb6-124">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="96fb6-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

<span data-ttu-id="96fb6-127">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="96fb6-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="96fb6-128">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="96fb6-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="96fb6-129">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="96fb6-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96fb6-131">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="96fb6-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="96fb6-132">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="96fb6-132">Seed the database</span></span>

<span data-ttu-id="96fb6-133">使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：</span><span class="sxs-lookup"><span data-stu-id="96fb6-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="96fb6-134">如果 DB 中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="96fb6-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="96fb6-135">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="96fb6-135">Add the seed initializer</span></span>

<span data-ttu-id="96fb6-136">在 Program.cs 中，修改 `Main` 方法以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="96fb6-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="96fb6-137">从依赖关系注入容器获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="96fb6-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="96fb6-138">调用 seed 方法，并将上下文传递给它。</span><span class="sxs-lookup"><span data-stu-id="96fb6-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="96fb6-139">Seed 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="96fb6-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="96fb6-140">下面的代码显示更新后的 Program.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="96fb6-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="96fb6-141">未运行 `Update-Database` 时出现以下异常：</span><span class="sxs-lookup"><span data-stu-id="96fb6-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="96fb6-142">测试应用</span><span class="sxs-lookup"><span data-stu-id="96fb6-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96fb6-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96fb6-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="96fb6-144">删除 DB 中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="96fb6-144">Delete all the records in the DB.</span></span> <span data-ttu-id="96fb6-145">可以使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作</span><span class="sxs-lookup"><span data-stu-id="96fb6-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="96fb6-146">强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="96fb6-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="96fb6-147">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="96fb6-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="96fb6-148">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="96fb6-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="96fb6-149">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：</span><span class="sxs-lookup"><span data-stu-id="96fb6-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * <span data-ttu-id="96fb6-152">如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="96fb6-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="96fb6-153">如果是在调试模式下运行 VS 的，请停止调试程序并按 F5。</span><span class="sxs-lookup"><span data-stu-id="96fb6-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96fb6-154">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="96fb6-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="96fb6-155">删除 DB 中的所有记录（使种子方法运行）。</span><span class="sxs-lookup"><span data-stu-id="96fb6-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="96fb6-156">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="96fb6-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="96fb6-157">应用将显示设定为种子的数据。</span><span class="sxs-lookup"><span data-stu-id="96fb6-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="96fb6-158">在下一教程中将对数据的展示进行改进。</span><span class="sxs-lookup"><span data-stu-id="96fb6-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96fb6-159">其他资源</span><span class="sxs-lookup"><span data-stu-id="96fb6-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="96fb6-160">[上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="96fb6-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="96fb6-161">`RazorPagesMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="96fb6-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="96fb6-162">在 Startup.cs 的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="96fb6-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96fb6-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96fb6-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="96fb6-164">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="96fb6-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="96fb6-165">有关 `ConfigureServices` 中使用的方法的详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="96fb6-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="96fb6-166">面向 `CookiePolicyOptions` 的 [ASP.NET Core 中的欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="96fb6-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="96fb6-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="96fb6-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="96fb6-168">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="96fb6-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="96fb6-169">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="96fb6-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96fb6-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96fb6-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="96fb6-171">生成代码的数据库名称值 (`Database={Database name}`) 将并不不同。</span><span class="sxs-lookup"><span data-stu-id="96fb6-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="96fb6-172">名称值是任意的。</span><span class="sxs-lookup"><span data-stu-id="96fb6-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="96fb6-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96fb6-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96fb6-174">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="96fb6-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="96fb6-175">将应用部署到测试或生产服务器时，可以使用环境变量将连接字符串设置为实际的数据库服务器。</span><span class="sxs-lookup"><span data-stu-id="96fb6-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="96fb6-176">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="96fb6-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96fb6-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96fb6-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="96fb6-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="96fb6-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="96fb6-179">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="96fb6-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="96fb6-180">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="96fb6-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="96fb6-181">默认情况下，LocalDB 数据库在 `C:/Users/<user/>` 目录下创建 `*.mdf` 文件。</span><span class="sxs-lookup"><span data-stu-id="96fb6-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="96fb6-182">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="96fb6-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](sql/_static/ssox.png)

* <span data-ttu-id="96fb6-184">右键单击 `Movie` 表，然后选择“视图设计器”：</span><span class="sxs-lookup"><span data-stu-id="96fb6-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Movie 表上打开的上下文菜单](sql/_static/design.png)

  ![设计器中打开的 Movie 表](sql/_static/dv.png)

<span data-ttu-id="96fb6-187">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="96fb6-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="96fb6-188">默认情况下，EF 为该主键创建一个名为 `ID` 的属性。</span><span class="sxs-lookup"><span data-stu-id="96fb6-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="96fb6-189">右键单击 `Movie` 表，然后选择“查看数据”：</span><span class="sxs-lookup"><span data-stu-id="96fb6-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![显示表数据的打开的 Movie 表](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="96fb6-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96fb6-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96fb6-192">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="96fb6-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="96fb6-193">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="96fb6-193">Seed the database</span></span>

<span data-ttu-id="96fb6-194">使用以下代码在 Models 文件夹中创建一个名为 `SeedData` 的新类：</span><span class="sxs-lookup"><span data-stu-id="96fb6-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="96fb6-195">如果 DB 中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="96fb6-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="96fb6-196">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="96fb6-196">Add the seed initializer</span></span>

<span data-ttu-id="96fb6-197">在 Program.cs 中，修改 `Main` 方法以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="96fb6-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="96fb6-198">从依赖关系注入容器获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="96fb6-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="96fb6-199">调用 seed 方法，并将上下文传递给它。</span><span class="sxs-lookup"><span data-stu-id="96fb6-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="96fb6-200">Seed 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="96fb6-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="96fb6-201">下面的代码显示更新后的 Program.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="96fb6-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="96fb6-202">生产应用不会调用 `Database.Migrate`。</span><span class="sxs-lookup"><span data-stu-id="96fb6-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="96fb6-203">它会添加到上面的代码中，以防止在未运行 `Update-Database` 时出现以下异常：</span><span class="sxs-lookup"><span data-stu-id="96fb6-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="96fb6-204">SqlException：无法打开登录请求的数据库“RazorPagesMovieContext-21”。</span><span class="sxs-lookup"><span data-stu-id="96fb6-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="96fb6-205">登录失败。</span><span class="sxs-lookup"><span data-stu-id="96fb6-205">The login failed.</span></span>
<span data-ttu-id="96fb6-206">用户“用户名”登录失败。</span><span class="sxs-lookup"><span data-stu-id="96fb6-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="96fb6-207">测试应用</span><span class="sxs-lookup"><span data-stu-id="96fb6-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="96fb6-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="96fb6-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="96fb6-209">删除 DB 中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="96fb6-209">Delete all the records in the DB.</span></span> <span data-ttu-id="96fb6-210">可以使用浏览器中的删除链接，也可以从 [SSOX](xref:tutorials/razor-pages/new-field#ssox) 执行此操作</span><span class="sxs-lookup"><span data-stu-id="96fb6-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="96fb6-211">强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="96fb6-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="96fb6-212">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="96fb6-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="96fb6-213">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="96fb6-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="96fb6-214">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” ：</span><span class="sxs-lookup"><span data-stu-id="96fb6-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express 系统任务栏图标](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](sql/_static/stopIIS.png)

    * <span data-ttu-id="96fb6-217">如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行。</span><span class="sxs-lookup"><span data-stu-id="96fb6-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="96fb6-218">如果是在调试模式下运行 VS 的，请停止调试程序并按 F5。</span><span class="sxs-lookup"><span data-stu-id="96fb6-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="96fb6-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="96fb6-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="96fb6-220">删除 DB 中的所有记录（使种子方法运行）。</span><span class="sxs-lookup"><span data-stu-id="96fb6-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="96fb6-221">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="96fb6-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="96fb6-222">应用将显示设定为种子的数据。</span><span class="sxs-lookup"><span data-stu-id="96fb6-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="96fb6-223">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="96fb6-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="96fb6-224">删除 DB 中的所有记录（使种子方法运行）。</span><span class="sxs-lookup"><span data-stu-id="96fb6-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="96fb6-225">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="96fb6-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="96fb6-226">应用将显示设定为种子的数据。</span><span class="sxs-lookup"><span data-stu-id="96fb6-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="96fb6-227">应用将显示设定为种子的数据：</span><span class="sxs-lookup"><span data-stu-id="96fb6-227">The app shows the seeded data:</span></span>

![在 Chrome 中打开的显示电影数据的电影应用程序](sql/_static/m55.png)

<span data-ttu-id="96fb6-229">在下一教程中将对数据的展示进行整理。</span><span class="sxs-lookup"><span data-stu-id="96fb6-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="96fb6-230">其他资源</span><span class="sxs-lookup"><span data-stu-id="96fb6-230">Additional resources</span></span>

* [<span data-ttu-id="96fb6-231">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="96fb6-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="96fb6-232">[上一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)
> [下一篇：更新页面](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="96fb6-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
