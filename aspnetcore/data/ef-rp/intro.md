---
title: ASP.NET Core 中的 Razor Pages 和 Entity Framework Core - 第 1 个教程（共 8 个）
author: rick-anderson
description: 介绍如何使用 Entity Framework Core 创建 Razor Pages 应用
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: data/ef-rp/intro
ms.openlocfilehash: cd6624d107fb19da92a7e58a747cc85e876a6ba4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018632"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="0eff5-103">ASP.NET Core 中的 Razor Pages 和 Entity Framework Core - 第 1 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="0eff5-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="0eff5-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0eff5-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0eff5-105">本文是系列教程的第一篇，这些教程展示如何在 [ASP.NET Core Razor Pages](xref:razor-pages/index) 应用中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="0eff5-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="0eff5-106">这些教程为虚构的 Contoso University 生成一个网站。</span><span class="sxs-lookup"><span data-stu-id="0eff5-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0eff5-107">网站包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="0eff5-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0eff5-108">本教程使用代码优先方法。</span><span class="sxs-lookup"><span data-stu-id="0eff5-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="0eff5-109">有关使用数据库优先方法学习本教程的信息，请参阅[此 Github 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="0eff5-110">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0eff5-111">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0eff5-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="0eff5-112">Prerequisites</span></span>

* <span data-ttu-id="0eff5-113">如果不熟悉 Razor Pages，则在开始前，请浏览 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)系列教程。</span><span class="sxs-lookup"><span data-stu-id="0eff5-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="0eff5-116">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="0eff5-116">Database engines</span></span>

<span data-ttu-id="0eff5-117">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="0eff5-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="0eff5-118">Visual Studio Code 指令使用 [SQLite](https://www.sqlite.org/)，一种跨平台数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="0eff5-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="0eff5-119">如果选择使用 SQLite，请下载并安装[适用于 SQLite 的数据库浏览器](https://sqlitebrowser.org/)等第三方工具，用于管理和查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0eff5-120">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0eff5-120">Troubleshooting</span></span>

<span data-ttu-id="0eff5-121">如果遇到无法解决的问题，请将你的代码与[完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)进行比较。</span><span class="sxs-lookup"><span data-stu-id="0eff5-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0eff5-122">获取帮助的一个好方法是使用 [ASP.NET Core 标记](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 标记](https://stackoverflow.com/questions/tagged/entity-framework-core)将问题发布到 StackOverflow.com。</span><span class="sxs-lookup"><span data-stu-id="0eff5-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="0eff5-123">示例应用</span><span class="sxs-lookup"><span data-stu-id="0eff5-123">The sample app</span></span>

<span data-ttu-id="0eff5-124">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="0eff5-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="0eff5-125">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="0eff5-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0eff5-126">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="0eff5-126">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index30.png)

![学生编辑页](intro/_static/student-edit30.png)

<span data-ttu-id="0eff5-129">此网站的 UI 样式基于内置的项目模板。</span><span class="sxs-lookup"><span data-stu-id="0eff5-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="0eff5-130">本教程侧重于如何使用 EF Core，而不是如何自定义 UI。</span><span class="sxs-lookup"><span data-stu-id="0eff5-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="0eff5-131">单击页面顶部的链接，获取已完成项目的源代码。</span><span class="sxs-lookup"><span data-stu-id="0eff5-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="0eff5-132">“cu30”文件夹中有本教程的 ASP.NET Core 3.0 版本的代码。</span><span class="sxs-lookup"><span data-stu-id="0eff5-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="0eff5-133">在“cu30snapshots”文件夹中可以找到反映教程 1-7 代码状态的文件。</span><span class="sxs-lookup"><span data-stu-id="0eff5-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0eff5-135">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0eff5-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0eff5-136">生成项目。</span><span class="sxs-lookup"><span data-stu-id="0eff5-136">Build the project.</span></span>
* <span data-ttu-id="0eff5-137">在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0eff5-137">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="0eff5-138">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="0eff5-138">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0eff5-140">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0eff5-140">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0eff5-141">删除 ContosoUniversity.csproj，然后将 ContosoUniversitySQLite.csproj 重命名为 ContosoUniversity.csproj\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0eff5-141">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="0eff5-142">删除 Startup.cs，然后将 StartupSQLite.cs 重命名为 Startup.cs\*\*\* \*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0eff5-142">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="0eff5-143">删除 appSettings.json，然后将 appSettingsSQLite.json 重命名为 appSettings.json\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="0eff5-143">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="0eff5-144">删除“Migrations”文件夹，然后将 MigrationsSQL 重命名为 Migrations  。</span><span class="sxs-lookup"><span data-stu-id="0eff5-144">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="0eff5-145">对 `#if SQLiteVersion` 执行全局搜索，并删除 `#if SQLiteVersion` 和相关 `#endif` 语句。</span><span class="sxs-lookup"><span data-stu-id="0eff5-145">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="0eff5-146">生成项目。</span><span class="sxs-lookup"><span data-stu-id="0eff5-146">Build the project.</span></span>
* <span data-ttu-id="0eff5-147">在项目文件夹中的命令提示符下运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0eff5-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="0eff5-148">在 SQLite 工具中，运行以下 SQL 语句：</span><span class="sxs-lookup"><span data-stu-id="0eff5-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="0eff5-149">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="0eff5-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="0eff5-150">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="0eff5-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eff5-152">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0eff5-153">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0eff5-154">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="0eff5-155">请务必使用此名称（含大写），确保在复制和粘贴代码时与命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="0eff5-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="0eff5-156">在下拉列表中选择“.NET Core”和“ASP.NET Core 3.0”，然后选择“Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="0eff5-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eff5-158">在终端中，导航到应在其中创建项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eff5-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="0eff5-159">运行以下命令，在新的项目文件夹中创建 Razor Pages 项目和 `cd`：</span><span class="sxs-lookup"><span data-stu-id="0eff5-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0eff5-160">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="0eff5-160">Set up the site style</span></span>

<span data-ttu-id="0eff5-161">更新 Pages/Shared/_Layout.cshtml 以设置网站的页眉、页脚和菜单：</span><span class="sxs-lookup"><span data-stu-id="0eff5-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="0eff5-162">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="0eff5-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0eff5-163">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="0eff5-163">There are three occurrences.</span></span>

* <span data-ttu-id="0eff5-164">删除“主页”和“隐私”菜单项，然后添加“关于”、“学生”、“课程”、“讲师”和“院系”的菜单项      。</span><span class="sxs-lookup"><span data-stu-id="0eff5-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="0eff5-165">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="0eff5-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="0eff5-166">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET Core 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="0eff5-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="0eff5-167">运行应用以验证主页是否显示。</span><span class="sxs-lookup"><span data-stu-id="0eff5-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="0eff5-168">数据模型</span><span class="sxs-lookup"><span data-stu-id="0eff5-168">The data model</span></span>

<span data-ttu-id="0eff5-169">以下部分用于创建数据模型：</span><span class="sxs-lookup"><span data-stu-id="0eff5-169">The following sections create a data model:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="0eff5-171">一名学生可以修读任意数量的课程，并且某一课程可以有任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="0eff5-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="0eff5-172">Student 实体</span><span class="sxs-lookup"><span data-stu-id="0eff5-172">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

* <span data-ttu-id="0eff5-174">在项目文件夹中创建“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eff5-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="0eff5-175">使用以下代码创建 Models/Student.cs：</span><span class="sxs-lookup"><span data-stu-id="0eff5-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="0eff5-176">`ID` 属性成为此类对应的数据库表的主键列。</span><span class="sxs-lookup"><span data-stu-id="0eff5-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0eff5-177">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="0eff5-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0eff5-178">因此，`Student` 类主键的另一种自动识别的名称是 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="0eff5-179">有关详细信息，请参阅 [F Core - 密钥](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="0eff5-180">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0eff5-181">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0eff5-182">在本例中，`Student` 实体的 `Enrollments` 属性包含与该 Student 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="0eff5-183">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="0eff5-184">在数据库中，如果 StudentID 列包含学生的 ID 值，则 Enrollment 行与 Student 行相关。</span><span class="sxs-lookup"><span data-stu-id="0eff5-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="0eff5-185">例如，假设某个 Student 行的 ID=1。</span><span class="sxs-lookup"><span data-stu-id="0eff5-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="0eff5-186">则相关 Enrollment 行的 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="0eff5-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="0eff5-187">StudentID 是 Enrollment 表中的外键。</span><span class="sxs-lookup"><span data-stu-id="0eff5-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="0eff5-188">`Enrollments` 属性定义为 `ICollection<Enrollment>`，因为可能有多个相关的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="0eff5-189">可以使用 `List<Enrollment>` 或 `HashSet<Enrollment>` 等其他集合类型。</span><span class="sxs-lookup"><span data-stu-id="0eff5-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="0eff5-190">使用 `ICollection<Enrollment>` 时，EF Core 会默认创建 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="0eff5-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="0eff5-191">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="0eff5-191">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="0eff5-193">使用以下代码创建 Models/Enrollment.cs：</span><span class="sxs-lookup"><span data-stu-id="0eff5-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="0eff5-194">`EnrollmentID` 属性为主键；此实体使用 `classnameID` 模式而不是直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0eff5-195">对于生产数据模型，请选择一个模式并一直使用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="0eff5-196">本教程两个都使用，只是为了说明这两个模式都能使用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="0eff5-197">使用不具有 `classname` 的 `ID` 可以更轻松地实现某些类型的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="0eff5-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="0eff5-198">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0eff5-199">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 [null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="0eff5-200">评级为 null 和评级为零是有区别的 &mdash; null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="0eff5-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0eff5-201">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0eff5-202">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="0eff5-203">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0eff5-204">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="0eff5-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0eff5-205">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="0eff5-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0eff5-206">例如，`StudentID` 是 `Student` 导航属性的外键，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0eff5-207">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0eff5-208">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0eff5-209">Course 实体</span><span class="sxs-lookup"><span data-stu-id="0eff5-209">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="0eff5-211">使用以下代码创建 Models/Course.cs：</span><span class="sxs-lookup"><span data-stu-id="0eff5-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="0eff5-212">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="0eff5-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0eff5-213">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="0eff5-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0eff5-214">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="0eff5-215">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="0eff5-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="0eff5-216">搭建“学生”页的基架</span><span class="sxs-lookup"><span data-stu-id="0eff5-216">Scaffold Student pages</span></span>

<span data-ttu-id="0eff5-217">本部分使用 ASP.NET Core 基架工具生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="0eff5-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="0eff5-218">EF Core 上下文类。</span><span class="sxs-lookup"><span data-stu-id="0eff5-218">An EF Core *context* class.</span></span> <span data-ttu-id="0eff5-219">上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="0eff5-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="0eff5-220">它派生自 `Microsoft.EntityFrameworkCore.DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="0eff5-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="0eff5-221">Razor 页面，可处理 `Student` 实体的创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eff5-223">在“Pages”文件夹中创建“Students”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="0eff5-224">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹，然后选择“添加”>“新搭建基架的项目” 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0eff5-225">在“添加基架”对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="0eff5-226">在“添加使用实体框架的 Razor Pages (CRUD)”对话框中：</span><span class="sxs-lookup"><span data-stu-id="0eff5-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="0eff5-227">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="0eff5-228">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="0eff5-229">将数据上下文名称从 ContosoUniversity.Models.ContosoUniversityContext 更改为 ContosoUniversity.Data.SchoolContext 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="0eff5-230">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-230">Select **Add**.</span></span>

<span data-ttu-id="0eff5-231">自动安装以下包：</span><span class="sxs-lookup"><span data-stu-id="0eff5-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eff5-233">运行以下 .NET Core CLI 命令，安装所需的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="0eff5-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="0eff5-234">基架需要 Microsoft.VisualStudio.Web.CodeGeneration.Design 包。</span><span class="sxs-lookup"><span data-stu-id="0eff5-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="0eff5-235">虽然应用不使用 SQL Server，但基架工具需要 SQL Server 包。</span><span class="sxs-lookup"><span data-stu-id="0eff5-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="0eff5-236">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eff5-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="0eff5-237">运行以下命令安装 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="0eff5-238">运行以下命令，搭建“学生”页的基架。</span><span class="sxs-lookup"><span data-stu-id="0eff5-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="0eff5-239">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="0eff5-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="0eff5-240">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="0eff5-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="0eff5-241">如果对上述步骤有疑问，请生成项目并重试基架搭建步骤。</span><span class="sxs-lookup"><span data-stu-id="0eff5-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="0eff5-242">基架流程：</span><span class="sxs-lookup"><span data-stu-id="0eff5-242">The scaffolding process:</span></span>

* <span data-ttu-id="0eff5-243">在“Pages/Students”文件夹中创建 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="0eff5-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="0eff5-244">Create.cshtml 和 Create.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="0eff5-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="0eff5-245">Delete.cshtml 和 Delete.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="0eff5-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="0eff5-246">Details.cshtml 和 Details.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="0eff5-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="0eff5-247">Edit.cshtml 和 Edit.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="0eff5-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="0eff5-248">Index.cshtml 和 Index.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="0eff5-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="0eff5-249">创建 Data/SchoolContext.cs。</span><span class="sxs-lookup"><span data-stu-id="0eff5-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="0eff5-250">将上下文添加到 Startup.cs 中的依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="0eff5-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="0eff5-251">将数据库连接字符串添加到 appsettings.json。</span><span class="sxs-lookup"><span data-stu-id="0eff5-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="0eff5-252">数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="0eff5-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0eff5-254">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="0eff5-255">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0eff5-256">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 文件。</span><span class="sxs-lookup"><span data-stu-id="0eff5-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0eff5-258">更改连接字符串以指向名为 CU.db 的 SQLite 数据库文件：</span><span class="sxs-lookup"><span data-stu-id="0eff5-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="0eff5-259">更新数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="0eff5-259">Update the database context class</span></span>

<span data-ttu-id="0eff5-260">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="0eff5-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0eff5-261">上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0eff5-262">上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0eff5-263">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0eff5-264">使用以下代码更新 SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="0eff5-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="0eff5-265">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="0eff5-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0eff5-266">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="0eff5-266">In EF Core terminology:</span></span>

* <span data-ttu-id="0eff5-267">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="0eff5-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="0eff5-268">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="0eff5-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0eff5-269">由于实体集包含多个实体，因此 DBSet 属性应为复数名称。</span><span class="sxs-lookup"><span data-stu-id="0eff5-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="0eff5-270">由于基架工具创建了 `Student` DBSet，因此此步骤将其更改为复数 `Students`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="0eff5-271">为了使 Razor Pages 代码与新的 DBSet 名称相匹配，请在整个项目中进行全局更改，将 `_context.Student` 更改为 `_context.Students`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="0eff5-272">更改发生 8 次。</span><span class="sxs-lookup"><span data-stu-id="0eff5-272">There are 8 occurrences.</span></span>

<span data-ttu-id="0eff5-273">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="0eff5-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="0eff5-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0eff5-274">Startup.cs</span></span>

<span data-ttu-id="0eff5-275">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0eff5-276">在应用程序启动过程通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="0eff5-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0eff5-277">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="0eff5-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0eff5-278">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="0eff5-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0eff5-279">基架工具自动将上下文类注册到了依赖项注入容器。</span><span class="sxs-lookup"><span data-stu-id="0eff5-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eff5-281">在 `ConfigureServices` 中，基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="0eff5-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eff5-283">在 `ConfigureServices` 中，确保基架所添加的代码调用 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="0eff5-284">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="0eff5-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0eff5-285">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="0eff5-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0eff5-286">创建数据库</span><span class="sxs-lookup"><span data-stu-id="0eff5-286">Create the database</span></span>

<span data-ttu-id="0eff5-287">如果没有数据库，请更新 Program.cs 以创建数据库：</span><span class="sxs-lookup"><span data-stu-id="0eff5-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0eff5-288">如果有上下文的数据库，则 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="0eff5-289">如果没有数据库，则它将创建数据库和架构。</span><span class="sxs-lookup"><span data-stu-id="0eff5-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="0eff5-290">`EnsureCreated` 启用以下工作流来处理数据模型更改：</span><span class="sxs-lookup"><span data-stu-id="0eff5-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="0eff5-291">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-291">Delete the database.</span></span> <span data-ttu-id="0eff5-292">任何现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="0eff5-292">Any existing data is lost.</span></span>
* <span data-ttu-id="0eff5-293">更改数据模型。</span><span class="sxs-lookup"><span data-stu-id="0eff5-293">Change the data model.</span></span> <span data-ttu-id="0eff5-294">例如，添加 `EmailAddress` 字段。</span><span class="sxs-lookup"><span data-stu-id="0eff5-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="0eff5-295">运行应用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-295">Run the app.</span></span>
* <span data-ttu-id="0eff5-296">`EnsureCreated` 创建具有新架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="0eff5-297">在无需保存数据的情况下，当架构快速发展时，此工作流在早期开发过程中表现良好。</span><span class="sxs-lookup"><span data-stu-id="0eff5-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="0eff5-298">如果需要保存已输入数据库的数据，情况就有所不同了。</span><span class="sxs-lookup"><span data-stu-id="0eff5-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="0eff5-299">如果是这种情况，请使用迁移。</span><span class="sxs-lookup"><span data-stu-id="0eff5-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="0eff5-300">本系列教程的后续部分将删除 `EnsureCreated` 创建的数据库，转而使用迁移。</span><span class="sxs-lookup"><span data-stu-id="0eff5-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="0eff5-301">无法使用迁移更新 `EnsureCreated` 创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0eff5-302">测试应用</span><span class="sxs-lookup"><span data-stu-id="0eff5-302">Test the app</span></span>

* <span data-ttu-id="0eff5-303">运行应用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-303">Run the app.</span></span>
* <span data-ttu-id="0eff5-304">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0eff5-305">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="0eff5-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0eff5-306">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="0eff5-306">Seed the database</span></span>

<span data-ttu-id="0eff5-307">`EnsureCreated` 方法将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="0eff5-308">本节添加用测试数据填充数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="0eff5-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="0eff5-309">使用以下代码创建 Data/DbInitializer.cs：</span><span class="sxs-lookup"><span data-stu-id="0eff5-309">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="0eff5-310">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="0eff5-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="0eff5-311">如果不存在学生，它将向数据库添加测试数据。</span><span class="sxs-lookup"><span data-stu-id="0eff5-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="0eff5-312">该代码使用数组创建测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="0eff5-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="0eff5-313">在 Program.cs 中，将 `EnsureCreated` 调用替换为 `DbInitializer.Initialize` 调用：</span><span class="sxs-lookup"><span data-stu-id="0eff5-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0eff5-315">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0eff5-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eff5-317">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="0eff5-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="0eff5-318">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-318">Restart the app.</span></span>

* <span data-ttu-id="0eff5-319">选择“学生”页查看已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="0eff5-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="0eff5-320">查看数据库</span><span class="sxs-lookup"><span data-stu-id="0eff5-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eff5-322">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0eff5-323">在 SSOX 中，依次选择“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="0eff5-324">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="0eff5-325">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="0eff5-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="0eff5-326">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="0eff5-327">右键单击 Student 表，然后单击“查看代码”查看 `Student` 模型如何映射到 `Student` 表架构 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0eff5-329">使用 SQLite 工具查看数据库架构和已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="0eff5-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="0eff5-330">数据库文件名为 CU.db，位于项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eff5-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="0eff5-331">异步代码</span><span class="sxs-lookup"><span data-stu-id="0eff5-331">Asynchronous code</span></span>

<span data-ttu-id="0eff5-332">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="0eff5-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0eff5-333">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0eff5-334">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="0eff5-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0eff5-335">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0eff5-336">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="0eff5-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0eff5-337">因此，使用异步代码可以更有效地利用服务器资源，并且服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="0eff5-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="0eff5-338">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="0eff5-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0eff5-339">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="0eff5-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0eff5-340">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="0eff5-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="0eff5-341">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0eff5-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0eff5-342">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="0eff5-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0eff5-343">创建返回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 对象。</span><span class="sxs-lookup"><span data-stu-id="0eff5-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="0eff5-344">返回类型 `Task<T>` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="0eff5-345">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="0eff5-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0eff5-346">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0eff5-347">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="0eff5-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0eff5-348">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="0eff5-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0eff5-349">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="0eff5-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0eff5-350">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="0eff5-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0eff5-351">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0eff5-352">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0eff5-353">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0eff5-354">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="0eff5-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="0eff5-355">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0eff5-356">后续步骤</span><span class="sxs-lookup"><span data-stu-id="0eff5-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0eff5-357">下一教程</span><span class="sxs-lookup"><span data-stu-id="0eff5-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0eff5-358">Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 创建 ASP.NET Core Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-358">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="0eff5-359">该示例应用是一个虚构的 Contoso University 的网站。</span><span class="sxs-lookup"><span data-stu-id="0eff5-359">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0eff5-360">其中包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="0eff5-360">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0eff5-361">本页是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。</span><span class="sxs-lookup"><span data-stu-id="0eff5-361">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="0eff5-362">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-362">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0eff5-363">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-363">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0eff5-364">先决条件</span><span class="sxs-lookup"><span data-stu-id="0eff5-364">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-365">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-366">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-366">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="0eff5-367">熟悉 [Razor Pages](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-367">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="0eff5-368">新程序员在开始学习本系列之前，应先完成 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-368">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0eff5-369">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0eff5-369">Troubleshooting</span></span>

<span data-ttu-id="0eff5-370">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="0eff5-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0eff5-371">获取帮助的一个好方法是将问题发布到适用于 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 的 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-371">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="0eff5-372">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="0eff5-372">The Contoso University web app</span></span>

<span data-ttu-id="0eff5-373">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="0eff5-373">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="0eff5-374">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="0eff5-374">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0eff5-375">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="0eff5-375">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

<span data-ttu-id="0eff5-378">此网站的 UI 样式与内置模板生成的 UI 样式类似。</span><span class="sxs-lookup"><span data-stu-id="0eff5-378">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="0eff5-379">教程的重点是 EF Core 和 Razor Pages，而非 UI。</span><span class="sxs-lookup"><span data-stu-id="0eff5-379">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="0eff5-380">创建 ContosoUniversity Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="0eff5-380">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-381">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eff5-382">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-382">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0eff5-383">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="0eff5-383">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="0eff5-384">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-384">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="0eff5-385">务必将该项目命名为 ContosoUniversity，以便复制/粘贴代码时命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="0eff5-385">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="0eff5-386">在下拉列表中选择“ASP.NET Core 2.1”，然后选择“Web 应用程序” 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-386">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="0eff5-387">有关上述步骤的图像，请参阅[创建 Razor Web 应用](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-387">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="0eff5-388">运行应用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-388">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0eff5-390">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="0eff5-390">Set up the site style</span></span>

<span data-ttu-id="0eff5-391">设置网站菜单、布局和主页时需作少量更改。</span><span class="sxs-lookup"><span data-stu-id="0eff5-391">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="0eff5-392">进行以下更改以更新 Pages/Shared/_Layout.cshtml：</span><span class="sxs-lookup"><span data-stu-id="0eff5-392">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="0eff5-393">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="0eff5-393">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0eff5-394">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="0eff5-394">There are three occurrences.</span></span>

* <span data-ttu-id="0eff5-395">添加菜单项 **Students**，**Courses**，**Instructors**，和 **Department**，并删除 **Contact**菜单项。</span><span class="sxs-lookup"><span data-stu-id="0eff5-395">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="0eff5-396">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="0eff5-396">The changes are highlighted.</span></span> <span data-ttu-id="0eff5-397">（没有显示全部标记。）</span><span class="sxs-lookup"><span data-stu-id="0eff5-397">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="0eff5-398">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET 和 MVC 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="0eff5-398">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="0eff5-399">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="0eff5-399">Create the data model</span></span>

<span data-ttu-id="0eff5-400">创建 Contoso University 应用的实体类。</span><span class="sxs-lookup"><span data-stu-id="0eff5-400">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="0eff5-401">从以下三个实体开始：</span><span class="sxs-lookup"><span data-stu-id="0eff5-401">Start with the following three entities:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="0eff5-403">`Student` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="0eff5-403">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="0eff5-404">`Course` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="0eff5-404">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="0eff5-405">一名学生可以报名参加任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="0eff5-405">A student can enroll in any number of courses.</span></span> <span data-ttu-id="0eff5-406">一门课程中可以包含任意数量的学生。</span><span class="sxs-lookup"><span data-stu-id="0eff5-406">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="0eff5-407">以下部分将为这几个实体中的每一个实体创建一个类。</span><span class="sxs-lookup"><span data-stu-id="0eff5-407">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="0eff5-408">Student 实体</span><span class="sxs-lookup"><span data-stu-id="0eff5-408">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="0eff5-410">创建 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eff5-410">Create a *Models* folder.</span></span> <span data-ttu-id="0eff5-411">在 Models 文件夹中，使用以下代码创建一个名为 Student.cs 的类文件 ：</span><span class="sxs-lookup"><span data-stu-id="0eff5-411">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="0eff5-412">`ID` 属性成为此类对应的数据库 (DB) 表的主键列。</span><span class="sxs-lookup"><span data-stu-id="0eff5-412">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="0eff5-413">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="0eff5-413">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0eff5-414">在 `classnameID` 中，`classname` 为类名称。</span><span class="sxs-lookup"><span data-stu-id="0eff5-414">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="0eff5-415">另一种自动识别的主键是上例中的 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-415">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="0eff5-416">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-416">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0eff5-417">导航属性链接到与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-417">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="0eff5-418">在这种情况下，`Student entity` 的 `Enrollments` 属性包含与该 `Student` 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-418">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="0eff5-419">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-419">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="0eff5-420">相关的 `Enrollment` 行是 `StudentID` 列中包含该学生的主键值的行。</span><span class="sxs-lookup"><span data-stu-id="0eff5-420">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="0eff5-421">例如，假设 ID=1 的学生在 `Enrollment` 表中有两行。</span><span class="sxs-lookup"><span data-stu-id="0eff5-421">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="0eff5-422">`Enrollment` 表中有两行的 `StudentID` = 1。</span><span class="sxs-lookup"><span data-stu-id="0eff5-422">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="0eff5-423">`StudentID` 是 `Enrollment` 表中的外键，用于指定 `Student` 表中的学生。</span><span class="sxs-lookup"><span data-stu-id="0eff5-423">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="0eff5-424">如果导航属性包含多个实体，则导航属性必须是列表类型，例如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-424">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="0eff5-425">可以指定 `ICollection<T>` 或诸如 `List<T>` 或 `HashSet<T>` 的类型。</span><span class="sxs-lookup"><span data-stu-id="0eff5-425">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="0eff5-426">使用 `ICollection<T>` 时，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="0eff5-426">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="0eff5-427">包含多个实体的导航属性来自于多对多和一对多关系。</span><span class="sxs-lookup"><span data-stu-id="0eff5-427">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="0eff5-428">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="0eff5-428">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="0eff5-430">在 Models 文件夹中，使用以下代码创建 Enrollment.cs ：</span><span class="sxs-lookup"><span data-stu-id="0eff5-430">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="0eff5-431">`EnrollmentID` 属性为主键。</span><span class="sxs-lookup"><span data-stu-id="0eff5-431">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="0eff5-432">`Student` 实体使用的是 `ID` 模式，而本实体使用的是 `classnameID` 模式。</span><span class="sxs-lookup"><span data-stu-id="0eff5-432">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="0eff5-433">通常情况下，开发者会选择一种模式并在整个数据模型中都使用该模式。</span><span class="sxs-lookup"><span data-stu-id="0eff5-433">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="0eff5-434">下一个教程将介绍如何使用不带类名的 ID，以便更轻松地在数据模型中实现集成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-434">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="0eff5-435">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-435">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0eff5-436">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="0eff5-436">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="0eff5-437">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="0eff5-437">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0eff5-438">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-438">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0eff5-439">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-439">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="0eff5-440">`Student` 实体与 `Student.Enrollments` 导航属性不同，后者包含多个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-440">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="0eff5-441">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-441">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0eff5-442">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="0eff5-442">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0eff5-443">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="0eff5-443">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0eff5-444">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-444">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0eff5-445">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-445">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0eff5-446">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-446">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="0eff5-447">Course 实体</span><span class="sxs-lookup"><span data-stu-id="0eff5-447">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="0eff5-449">在 Models 文件夹中，使用以下代码创建 Course.cs ：</span><span class="sxs-lookup"><span data-stu-id="0eff5-449">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="0eff5-450">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="0eff5-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0eff5-451">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="0eff5-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0eff5-452">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-452">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="0eff5-453">为“学生”模型搭建基架</span><span class="sxs-lookup"><span data-stu-id="0eff5-453">Scaffold the student model</span></span>

<span data-ttu-id="0eff5-454">本部分将为“学生”模型搭建基架。</span><span class="sxs-lookup"><span data-stu-id="0eff5-454">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="0eff5-455">确切地说，基架工具将生成页面，用于对“学生”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-455">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="0eff5-456">生成项目。</span><span class="sxs-lookup"><span data-stu-id="0eff5-456">Build the project.</span></span>
* <span data-ttu-id="0eff5-457">创建 Pages/Students 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eff5-457">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eff5-459">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-459">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0eff5-460">在“添加基架”对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-460">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="0eff5-461">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="0eff5-461">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0eff5-462">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-462">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="0eff5-463">在“数据上下文类”行中，选择加号 (+) 并将生成的名称更改为 ContosoUniversity.Models.SchoolContext  。</span><span class="sxs-lookup"><span data-stu-id="0eff5-463">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="0eff5-464">在“数据上下文类”下拉列表中，选择“ContosoUniversity.Models.SchoolContext” </span><span class="sxs-lookup"><span data-stu-id="0eff5-464">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="0eff5-465">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-465">Select **Add**.</span></span>

![CRUD 对话框](intro/_static/s1.png)

<span data-ttu-id="0eff5-467">如果对前面的步骤有疑问，请参阅[搭建“电影”模型的基架](xref:tutorials/razor-pages/model#scaffold-the-movie-model)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-467">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-468">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-468">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0eff5-469">运行以下命令，搭建“学生”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="0eff5-469">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="0eff5-470">搭建基架的过程会创建并更改以下文件：</span><span class="sxs-lookup"><span data-stu-id="0eff5-470">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0eff5-471">创建的文件</span><span class="sxs-lookup"><span data-stu-id="0eff5-471">Files created</span></span>

* <span data-ttu-id="0eff5-472">Pages/Students：“创建”、“删除”、“详细信息”、“编辑”、“索引”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-472">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="0eff5-473">Data/SchoolContext.cs</span><span class="sxs-lookup"><span data-stu-id="0eff5-473">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="0eff5-474">更新的文件</span><span class="sxs-lookup"><span data-stu-id="0eff5-474">File updates</span></span>

* <span data-ttu-id="0eff5-475">*Startup.cs*：下一部分详细介绍对此文件所作的更改。</span><span class="sxs-lookup"><span data-stu-id="0eff5-475">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="0eff5-476">*appsettings.json*：添加用于连接到本地数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="0eff5-476">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0eff5-477">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="0eff5-477">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0eff5-478">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-478">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0eff5-479">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="0eff5-479">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0eff5-480">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="0eff5-480">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0eff5-481">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="0eff5-481">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0eff5-482">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="0eff5-482">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0eff5-483">在 Startup.cs 中检查 `ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="0eff5-483">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="0eff5-484">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="0eff5-484">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="0eff5-485">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="0eff5-485">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0eff5-486">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="0eff5-486">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="0eff5-487">更新 main</span><span class="sxs-lookup"><span data-stu-id="0eff5-487">Update main</span></span>

<span data-ttu-id="0eff5-488">在 Program.cs 中，修改 `Main` 方法以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0eff5-488">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="0eff5-489">从依赖关系注入容器获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="0eff5-489">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="0eff5-490">调用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-490">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="0eff5-491">`EnsureCreated` 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="0eff5-491">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="0eff5-492">下面的代码显示更新后的 Program.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="0eff5-492">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="0eff5-493">`EnsureCreated` 确保存在上下文数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-493">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="0eff5-494">如果存在，则不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-494">If it exists, no action is taken.</span></span> <span data-ttu-id="0eff5-495">如果不存在，则会创建数据库及其所有架构。</span><span class="sxs-lookup"><span data-stu-id="0eff5-495">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="0eff5-496">`EnsureCreated` 不使用迁移创建数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-496">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="0eff5-497">使用 `EnsureCreated` 创建的数据库稍后无法使用迁移更新。</span><span class="sxs-lookup"><span data-stu-id="0eff5-497">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="0eff5-498">启动应用时会调用 `EnsureCreated`，以进行以下工作流：</span><span class="sxs-lookup"><span data-stu-id="0eff5-498">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="0eff5-499">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-499">Delete the DB.</span></span>
* <span data-ttu-id="0eff5-500">更改数据库架构（例如添加一个 `EmailAddress` 字段）。</span><span class="sxs-lookup"><span data-stu-id="0eff5-500">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="0eff5-501">运行应用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-501">Run the app.</span></span>
* <span data-ttu-id="0eff5-502">`EnsureCreated` 创建一个带有 `EmailAddress` 列的数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-502">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="0eff5-503">架构快速演变时，在开发初期使用 `EnsureCreated` 很方便。</span><span class="sxs-lookup"><span data-stu-id="0eff5-503">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="0eff5-504">本教程后面将删除 DB 并使用迁移。</span><span class="sxs-lookup"><span data-stu-id="0eff5-504">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0eff5-505">测试应用</span><span class="sxs-lookup"><span data-stu-id="0eff5-505">Test the app</span></span>

<span data-ttu-id="0eff5-506">运行应用并接受 cookie 策略。</span><span class="sxs-lookup"><span data-stu-id="0eff5-506">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="0eff5-507">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="0eff5-507">This app doesn't keep personal information.</span></span> <span data-ttu-id="0eff5-508">有关 cookie 策略的信息，请参阅[欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-508">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="0eff5-509">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-509">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0eff5-510">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="0eff5-510">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="0eff5-511">检查 SchoolContext DB 上下文</span><span class="sxs-lookup"><span data-stu-id="0eff5-511">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="0eff5-512">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="0eff5-512">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="0eff5-513">数据上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-513">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0eff5-514">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-514">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0eff5-515">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-515">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0eff5-516">使用以下代码更新 SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="0eff5-516">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="0eff5-517">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="0eff5-517">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0eff5-518">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="0eff5-518">In EF Core terminology:</span></span>

* <span data-ttu-id="0eff5-519">实体集通常对应一个数据库表。</span><span class="sxs-lookup"><span data-stu-id="0eff5-519">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="0eff5-520">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="0eff5-520">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0eff5-521">可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-521">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="0eff5-522">EF Core 隐式包含了它们，因为 `Student` 实体引用 `Enrollment` 实体，而 `Enrollment` 实体引用 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="0eff5-522">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="0eff5-523">在本教程中，将 `DbSet<Enrollment>` 和 `DbSet<Course>` 保留在 `SchoolContext` 中。</span><span class="sxs-lookup"><span data-stu-id="0eff5-523">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="0eff5-524">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="0eff5-524">SQL Server Express LocalDB</span></span>

<span data-ttu-id="0eff5-525">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-525">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="0eff5-526">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0eff5-527">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="0eff5-527">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="0eff5-528">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="0eff5-528">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="0eff5-529">添加代码，以使用测试数据初始化该数据库</span><span class="sxs-lookup"><span data-stu-id="0eff5-529">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="0eff5-530">EF Core 会创建一个空的数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-530">EF Core creates an empty DB.</span></span> <span data-ttu-id="0eff5-531">本部分中编写了 `Initialize` 方法来使用测试数据填充该数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-531">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="0eff5-532">在 Data 文件夹中，新建一个名为 DbInitializer.cs 的类文件，并添加以下代码 ：</span><span class="sxs-lookup"><span data-stu-id="0eff5-532">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="0eff5-533">注意：上面的代码对命名空间使用 `Models` (`namespace ContosoUniversity.Models`)，而不是 `Data`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-533">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="0eff5-534">`Models` 与基架生成的代码一致。</span><span class="sxs-lookup"><span data-stu-id="0eff5-534">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="0eff5-535">有关详细信息，请参阅[此 GitHub 基架问题](https://github.com/aspnet/Scaffolding/issues/822)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-535">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="0eff5-536">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="0eff5-536">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="0eff5-537">如果 DB 中没有任何学生，则会使用测试数据初始化该 DB。</span><span class="sxs-lookup"><span data-stu-id="0eff5-537">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="0eff5-538">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="0eff5-538">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0eff5-539">`EnsureCreated` 方法自动为数据库上下文创建数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-539">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="0eff5-540">如果数据库已存在，则返回 `EnsureCreated`，并且不修改数据库。</span><span class="sxs-lookup"><span data-stu-id="0eff5-540">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="0eff5-541">在 Program.cs 中，将 `Main` 方法修改为调用 `Initialize`：</span><span class="sxs-lookup"><span data-stu-id="0eff5-541">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="0eff5-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eff5-542">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0eff5-543">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0eff5-543">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0eff5-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eff5-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eff5-545">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="0eff5-545">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="0eff5-546">查看数据库</span><span class="sxs-lookup"><span data-stu-id="0eff5-546">View the DB</span></span>

<span data-ttu-id="0eff5-547">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-547">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="0eff5-548">因此，数据库名称为“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-548">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="0eff5-549">GUID 因用户而异。</span><span class="sxs-lookup"><span data-stu-id="0eff5-549">The GUID will be different for each user.</span></span>
<span data-ttu-id="0eff5-550">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-550">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="0eff5-551">在 SSOX 中，依次单击“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="0eff5-551">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="0eff5-552">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="0eff5-552">Expand the **Tables** node.</span></span>

<span data-ttu-id="0eff5-553">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="0eff5-553">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="0eff5-554">异步代码</span><span class="sxs-lookup"><span data-stu-id="0eff5-554">Asynchronous code</span></span>

<span data-ttu-id="0eff5-555">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="0eff5-555">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0eff5-556">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="0eff5-556">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0eff5-557">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="0eff5-557">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0eff5-558">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="0eff5-558">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0eff5-559">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="0eff5-559">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0eff5-560">因此，使用异步代码可以更有效地利用服务器资源，并且可以让服务器在没有延迟的情况下处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="0eff5-560">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="0eff5-561">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="0eff5-561">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0eff5-562">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="0eff5-562">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0eff5-563">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="0eff5-563">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="0eff5-564">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0eff5-564">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0eff5-565">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="0eff5-565">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0eff5-566">自动创建返回的 [Task](/dotnet/api/system.threading.tasks.task) 对象。</span><span class="sxs-lookup"><span data-stu-id="0eff5-566">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="0eff5-567">有关详细信息，请参阅[任务返回类型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-567">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="0eff5-568">隐式返回类型 `Task` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-568">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="0eff5-569">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="0eff5-569">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0eff5-570">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-570">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0eff5-571">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="0eff5-571">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0eff5-572">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="0eff5-572">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0eff5-573">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="0eff5-573">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0eff5-574">只会异步执行导致查询或命令被发送到数据库的语句。</span><span class="sxs-lookup"><span data-stu-id="0eff5-574">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="0eff5-575">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-575">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0eff5-576">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="0eff5-576">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0eff5-577">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-577">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0eff5-578">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="0eff5-578">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="0eff5-579">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="0eff5-579">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="0eff5-580">下一个教程将介绍基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="0eff5-580">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="0eff5-581">其他资源</span><span class="sxs-lookup"><span data-stu-id="0eff5-581">Additional resources</span></span>

* [<span data-ttu-id="0eff5-582">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="0eff5-582">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="0eff5-583">下一页</span><span class="sxs-lookup"><span data-stu-id="0eff5-583">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
