---
title: 在 ASP.NET Core MVC 应用中使用 SQL
author: rick-anderson
description: 了解如何在 ASP.NET Core MVC 应用中使用 SQL Server LocalDB 或 SQLite。
ms.author: riande
ms.date: 03/07/2017
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 983742276f3519b540cd62e4ada6eb5189650aa8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884112"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="44a0e-103">在 ASP.NET Core 中使用 SQL</span><span class="sxs-lookup"><span data-stu-id="44a0e-103">Work with SQL in ASP.NET Core</span></span>

<span data-ttu-id="44a0e-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="44a0e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="44a0e-105">`MvcMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。</span><span class="sxs-lookup"><span data-stu-id="44a0e-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="44a0e-106">在 Startup.cs 文件的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="44a0e-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44a0e-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a0e-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="44a0e-108">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="44a0e-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="44a0e-109">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串：</span><span class="sxs-lookup"><span data-stu-id="44a0e-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="44a0e-110">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44a0e-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="44a0e-111">ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。</span><span class="sxs-lookup"><span data-stu-id="44a0e-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="44a0e-112">为了进行本地开发，它会从 appsettings.json 文件获取连接字符串：</span><span class="sxs-lookup"><span data-stu-id="44a0e-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="44a0e-113">将应用部署到测试或生产服务器时，可使用环境变量或另一种方法将连接字符串设置为实际的 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="44a0e-113">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="44a0e-114">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="44a0e-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44a0e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a0e-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="44a0e-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="44a0e-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="44a0e-117">LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。</span><span class="sxs-lookup"><span data-stu-id="44a0e-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="44a0e-118">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="44a0e-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="44a0e-119">默认情况下，LocalDB 数据库在 C:/Users/{user} 目录中创建 .mdf 文件。</span><span class="sxs-lookup"><span data-stu-id="44a0e-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="44a0e-120">从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX)。</span><span class="sxs-lookup"><span data-stu-id="44a0e-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![“视图”菜单](working-with-sql/_static/ssox.png)

* <span data-ttu-id="44a0e-122">右键单击 `Movie` 表，然后单击“视图设计器”</span><span class="sxs-lookup"><span data-stu-id="44a0e-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/design.png)

  ![设计器中打开的 Movie 表](working-with-sql/_static/dv.png)

<span data-ttu-id="44a0e-125">请注意 `ID` 旁边的密钥图标。</span><span class="sxs-lookup"><span data-stu-id="44a0e-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="44a0e-126">默认情况下，EF 将名为 `ID` 的属性设置为主键。</span><span class="sxs-lookup"><span data-stu-id="44a0e-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="44a0e-127">右键单击 `Movie` 表，然后单击“查看数据”</span><span class="sxs-lookup"><span data-stu-id="44a0e-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/ssox2.png)

  ![显示表数据的打开的 Movie 表](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="44a0e-130">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44a0e-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="44a0e-131">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="44a0e-131">Seed the database</span></span>

<span data-ttu-id="44a0e-132">在 Models 文件夹中创建一个名为 `SeedData` 的新类。</span><span class="sxs-lookup"><span data-stu-id="44a0e-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="44a0e-133">将生成的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="44a0e-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="44a0e-134">如果 DB 中没有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。</span><span class="sxs-lookup"><span data-stu-id="44a0e-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="44a0e-135">添加种子初始值设定项</span><span class="sxs-lookup"><span data-stu-id="44a0e-135">Add the seed initializer</span></span>

<span data-ttu-id="44a0e-136">将 Program.cs 的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="44a0e-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="44a0e-137">测试应用</span><span class="sxs-lookup"><span data-stu-id="44a0e-137">Test the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44a0e-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44a0e-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="44a0e-139">删除 DB 中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="44a0e-139">Delete all the records in the DB.</span></span> <span data-ttu-id="44a0e-140">可以使用浏览器中的删除链接，也可从 SSOX 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="44a0e-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="44a0e-141">强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。</span><span class="sxs-lookup"><span data-stu-id="44a0e-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="44a0e-142">若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。</span><span class="sxs-lookup"><span data-stu-id="44a0e-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="44a0e-143">可以使用以下任一方法来执行此操作：</span><span class="sxs-lookup"><span data-stu-id="44a0e-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="44a0e-144">右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点”</span><span class="sxs-lookup"><span data-stu-id="44a0e-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express 系统任务栏图标](working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="44a0e-147">如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行</span><span class="sxs-lookup"><span data-stu-id="44a0e-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="44a0e-148">如果是在调试模式下运行 VS 的，请停止调试程序并按 F5</span><span class="sxs-lookup"><span data-stu-id="44a0e-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="44a0e-149">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44a0e-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="44a0e-150">删除 DB 中的所有记录（使种子方法运行）。</span><span class="sxs-lookup"><span data-stu-id="44a0e-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="44a0e-151">停止并启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="44a0e-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="44a0e-152">应用将显示设定为种子的数据。</span><span class="sxs-lookup"><span data-stu-id="44a0e-152">The app shows the seeded data.</span></span>

![在 Microsoft Edge 中打开的显示电影数据的 MVC 电影应用程序](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="44a0e-154">[上一页](adding-model.md)
> [下一页](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="44a0e-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
