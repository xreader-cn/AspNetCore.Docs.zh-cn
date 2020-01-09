---
title: 在 ASP.NET Core MVC 应用中使用 SQL
author: rick-anderson
description: 了解如何在 ASP.NET Core MVC 应用中使用 SQL Server LocalDB 或 SQLite。
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722761"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="85097-103">在 ASP.NET Core 中使用 SQL</span><span class="sxs-lookup"><span data-stu-id="85097-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85097-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="85097-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="85097-105">`MvcMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="85097-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="85097-106">在 Startup.cs 文件的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文  ：</span><span class="sxs-lookup"><span data-stu-id="85097-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85097-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85097-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="85097-108">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="85097-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="85097-109">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串： </span><span class="sxs-lookup"><span data-stu-id="85097-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="85097-110">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="85097-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="85097-111">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="85097-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="85097-112">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串： </span><span class="sxs-lookup"><span data-stu-id="85097-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="85097-113">当应用部署到测试服务器或生产服务器时，环境变量可用于将连接字符串设置为生产 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="85097-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="85097-114">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="85097-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85097-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85097-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="85097-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="85097-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="85097-117">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="85097-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="85097-118">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="85097-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="85097-119">默认情况下，LocalDB 数据库在 C:/Users/{user} 目录中创建 .mdf 文件   。</span><span class="sxs-lookup"><span data-stu-id="85097-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="85097-120">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX)   。</span><span class="sxs-lookup"><span data-stu-id="85097-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](working-with-sql/_static/ssox.png)

* <span data-ttu-id="85097-122">右键单击 `Movie` 表，然后单击“视图设计器” </span><span class="sxs-lookup"><span data-stu-id="85097-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/design.png)

  ![设计器中打开的 Movie 表](working-with-sql/_static/dv.png)

<span data-ttu-id="85097-125">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="85097-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="85097-126">默认情况下，EF 将名为 `ID` 的属性设置为主键。</span><span class="sxs-lookup"><span data-stu-id="85097-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="85097-127">右键单击 `Movie` 表，然后单击“查看数据” </span><span class="sxs-lookup"><span data-stu-id="85097-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/ssox2.png)

  ![显示表数据的打开的 Movie 表](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="85097-130">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="85097-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="85097-131">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="85097-131">Seed the database</span></span>

<span data-ttu-id="85097-132">在 Models 文件夹中创建一个名为 `SeedData` 的新类  。</span><span class="sxs-lookup"><span data-stu-id="85097-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="85097-133">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="85097-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="85097-134">如果 DB 中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="85097-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="85097-135">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="85097-135">Add the seed initializer</span></span>

<span data-ttu-id="85097-136">将 Program.cs 的内容替换为以下代码  ：</span><span class="sxs-lookup"><span data-stu-id="85097-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="85097-137">测试应用</span><span class="sxs-lookup"><span data-stu-id="85097-137">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85097-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85097-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85097-139">删除 DB 中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="85097-139">Delete all the records in the DB.</span></span> <span data-ttu-id="85097-140">可以使用浏览器中的删除链接，也可从 SSOX 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="85097-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="85097-141">强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="85097-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="85097-142">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="85097-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="85097-143">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="85097-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="85097-144">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点”  </span><span class="sxs-lookup"><span data-stu-id="85097-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express 系统任务栏图标](working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="85097-147">如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行</span><span class="sxs-lookup"><span data-stu-id="85097-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="85097-148">如果是在调试模式下运行 VS 的，请停止调试程序并按 F5</span><span class="sxs-lookup"><span data-stu-id="85097-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="85097-149">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="85097-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="85097-150">删除 DB 中的所有记录（使种子方法运行）。</span><span class="sxs-lookup"><span data-stu-id="85097-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="85097-151">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="85097-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="85097-152">应用将显示设定为种子的数据。</span><span class="sxs-lookup"><span data-stu-id="85097-152">The app shows the seeded data.</span></span>

![在 Microsoft Edge 中打开的显示电影数据的 MVC 电影应用程序](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="85097-154">[上一页](adding-model.md)
> [下一页](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="85097-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="85097-155">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="85097-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="85097-156">`MvcMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="85097-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="85097-157">在 Startup.cs 文件的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文  ：</span><span class="sxs-lookup"><span data-stu-id="85097-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85097-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85097-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="85097-159">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="85097-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="85097-160">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串： </span><span class="sxs-lookup"><span data-stu-id="85097-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="85097-161">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="85097-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="85097-162">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="85097-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="85097-163">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串： </span><span class="sxs-lookup"><span data-stu-id="85097-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="85097-164">将应用部署到测试或生产服务器时，可使用环境变量或另一种方法将连接字符串设置为实际的 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="85097-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="85097-165">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="85097-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85097-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85097-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="85097-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="85097-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="85097-168">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="85097-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="85097-169">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="85097-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="85097-170">默认情况下，LocalDB 数据库在 C:/Users/{user} 目录中创建 .mdf 文件   。</span><span class="sxs-lookup"><span data-stu-id="85097-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="85097-171">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX)   。</span><span class="sxs-lookup"><span data-stu-id="85097-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](working-with-sql/_static/ssox.png)

* <span data-ttu-id="85097-173">右键单击 `Movie` 表，然后单击“视图设计器” </span><span class="sxs-lookup"><span data-stu-id="85097-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/design.png)

  ![设计器中打开的 Movie 表](working-with-sql/_static/dv.png)

<span data-ttu-id="85097-176">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="85097-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="85097-177">默认情况下，EF 将名为 `ID` 的属性设置为主键。</span><span class="sxs-lookup"><span data-stu-id="85097-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="85097-178">右键单击 `Movie` 表，然后单击“查看数据” </span><span class="sxs-lookup"><span data-stu-id="85097-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/ssox2.png)

  ![显示表数据的打开的 Movie 表](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="85097-181">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="85097-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="85097-182">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="85097-182">Seed the database</span></span>

<span data-ttu-id="85097-183">在 Models 文件夹中创建一个名为 `SeedData` 的新类  。</span><span class="sxs-lookup"><span data-stu-id="85097-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="85097-184">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="85097-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="85097-185">如果 DB 中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="85097-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="85097-186">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="85097-186">Add the seed initializer</span></span>

<span data-ttu-id="85097-187">将 Program.cs 的内容替换为以下代码  ：</span><span class="sxs-lookup"><span data-stu-id="85097-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="85097-188">测试应用</span><span class="sxs-lookup"><span data-stu-id="85097-188">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85097-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85097-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85097-190">删除 DB 中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="85097-190">Delete all the records in the DB.</span></span> <span data-ttu-id="85097-191">可以使用浏览器中的删除链接，也可从 SSOX 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="85097-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="85097-192">强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="85097-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="85097-193">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="85097-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="85097-194">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="85097-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="85097-195">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点”  </span><span class="sxs-lookup"><span data-stu-id="85097-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express 系统任务栏图标](working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="85097-198">如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行</span><span class="sxs-lookup"><span data-stu-id="85097-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="85097-199">如果是在调试模式下运行 VS 的，请停止调试程序并按 F5</span><span class="sxs-lookup"><span data-stu-id="85097-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="85097-200">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="85097-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="85097-201">删除 DB 中的所有记录（使种子方法运行）。</span><span class="sxs-lookup"><span data-stu-id="85097-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="85097-202">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="85097-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="85097-203">应用将显示设定为种子的数据。</span><span class="sxs-lookup"><span data-stu-id="85097-203">The app shows the seeded data.</span></span>

![在 Microsoft Edge 中打开的显示电影数据的 MVC 电影应用程序](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="85097-205">[上一页](adding-model.md)
> [下一页](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="85097-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
