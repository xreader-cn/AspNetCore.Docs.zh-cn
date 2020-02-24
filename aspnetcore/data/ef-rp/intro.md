---
title: ASP.NET Core 中的 Razor 页面和 Entity Framework Core - 第 1 个教程（共 8 个）
author: rick-anderson
description: 介绍了如何使用 Entity Framework Core 创建 Razor 页面应用
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 1a9d83be9180b1d32ab941932eb3cab8612dff01
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213397"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="aaac9-103">ASP.NET Core 中的 Razor 页面和 Entity Framework Core - 第 1 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="aaac9-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="aaac9-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aaac9-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aaac9-105">本文是系列教程的第一篇，这些教程展示如何在 [ASP.NET Core Razor Pages](xref:razor-pages/index) 应用中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="aaac9-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="aaac9-106">这些教程为虚构的 Contoso University 生成一个网站。</span><span class="sxs-lookup"><span data-stu-id="aaac9-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="aaac9-107">网站包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="aaac9-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="aaac9-108">本教程使用代码优先方法。</span><span class="sxs-lookup"><span data-stu-id="aaac9-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="aaac9-109">有关使用数据库优先方法学习本教程的信息，请参阅[此 Github 问题](https://github.com/aspnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/aspnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="aaac9-110">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-110">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="aaac9-111">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aaac9-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="aaac9-112">Prerequisites</span></span>

* <span data-ttu-id="aaac9-113">如果不熟悉 Razor Pages，则在开始前，请浏览 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)系列教程。</span><span class="sxs-lookup"><span data-stu-id="aaac9-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="aaac9-116">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="aaac9-116">Database engines</span></span>

<span data-ttu-id="aaac9-117">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="aaac9-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="aaac9-118">Visual Studio Code 指令使用 [SQLite](https://www.sqlite.org/)，一种跨平台数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="aaac9-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="aaac9-119">如果选择使用 SQLite，请下载并安装[适用于 SQLite 的数据库浏览器](https://sqlitebrowser.org/)等第三方工具，用于管理和查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="aaac9-120">疑难解答</span><span class="sxs-lookup"><span data-stu-id="aaac9-120">Troubleshooting</span></span>

<span data-ttu-id="aaac9-121">如果遇到无法解决的问题，请将你的代码与[完成的项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)进行比较。</span><span class="sxs-lookup"><span data-stu-id="aaac9-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="aaac9-122">获取帮助的一个好方法是使用 [ASP.NET Core 标记](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 标记](https://stackoverflow.com/questions/tagged/entity-framework-core)将问题发布到 StackOverflow.com。</span><span class="sxs-lookup"><span data-stu-id="aaac9-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="aaac9-123">示例应用</span><span class="sxs-lookup"><span data-stu-id="aaac9-123">The sample app</span></span>

<span data-ttu-id="aaac9-124">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="aaac9-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="aaac9-125">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="aaac9-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="aaac9-126">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="aaac9-126">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index30.png)

![学生编辑页](intro/_static/student-edit30.png)

<span data-ttu-id="aaac9-129">此网站的 UI 样式基于内置的项目模板。</span><span class="sxs-lookup"><span data-stu-id="aaac9-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="aaac9-130">本教程侧重于如何使用 EF Core，而不是如何自定义 UI。</span><span class="sxs-lookup"><span data-stu-id="aaac9-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="aaac9-131">单击页面顶部的链接，获取已完成项目的源代码。</span><span class="sxs-lookup"><span data-stu-id="aaac9-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="aaac9-132">“cu30”文件夹中有本教程的 ASP.NET Core 3.0 版本的代码  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="aaac9-133">在“cu30snapshots”文件夹中可以找到反映教程 1-7 代码状态的文件  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaac9-135">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="aaac9-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="aaac9-136">删除名称中包含 SQLite 的三个文件和一个文件夹  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="aaac9-137">生成项目。</span><span class="sxs-lookup"><span data-stu-id="aaac9-137">Build the project.</span></span>
* <span data-ttu-id="aaac9-138">在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="aaac9-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="aaac9-139">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="aaac9-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aaac9-141">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="aaac9-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="aaac9-142">删除 ContosoUniversity.csproj，然后将 ContosoUniversitySQLite.csproj 重命名为 ContosoUniversity.csproj \*\*\*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="aaac9-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="aaac9-143">删除 Startup.cs，然后将 StartupSQLite.cs 重命名为 Startup.cs \*\*\* \*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="aaac9-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="aaac9-144">删除 appSettings.json，然后将 appSettingsSQLite.json 重命名为 appSettings.json \*\*\*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="aaac9-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="aaac9-145">删除“Migrations”文件夹，然后将 MigrationsSQL 重命名为 Migrations    。</span><span class="sxs-lookup"><span data-stu-id="aaac9-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="aaac9-146">生成项目。</span><span class="sxs-lookup"><span data-stu-id="aaac9-146">Build the project.</span></span>
* <span data-ttu-id="aaac9-147">在项目文件夹中的命令提示符下运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="aaac9-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="aaac9-148">在 SQLite 工具中，运行以下 SQL 语句：</span><span class="sxs-lookup"><span data-stu-id="aaac9-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="aaac9-149">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="aaac9-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="aaac9-150">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="aaac9-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaac9-152">从 Visual Studio“文件”  菜单中选择“新建”  >“项目”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="aaac9-153">选择“ASP.NET Core Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="aaac9-154">将该项目命名为 ContosoUniversity  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="aaac9-155">请务必使用此名称（含大写），确保在复制和粘贴代码时与命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="aaac9-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="aaac9-156">在下拉列表中选择“.NET Core”和“ASP.NET Core 3.0”，然后选择“Web 应用程序”    。</span><span class="sxs-lookup"><span data-stu-id="aaac9-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aaac9-158">在终端中，导航到应在其中创建项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="aaac9-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="aaac9-159">运行以下命令，在新的项目文件夹中创建 Razor Pages 项目和 `cd`：</span><span class="sxs-lookup"><span data-stu-id="aaac9-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="aaac9-160">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="aaac9-160">Set up the site style</span></span>

<span data-ttu-id="aaac9-161">更新 Pages/Shared/_Layout.cshtml 以设置网站的页眉、页脚和菜单  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="aaac9-162">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="aaac9-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="aaac9-163">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="aaac9-163">There are three occurrences.</span></span>

* <span data-ttu-id="aaac9-164">删除“主页”和“隐私”菜单项，然后添加“关于”、“学生”、“课程”、“讲师”和“院系”的菜单项        。</span><span class="sxs-lookup"><span data-stu-id="aaac9-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="aaac9-165">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="aaac9-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="aaac9-166">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET Core 的文本替换为有关本应用的文本  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="aaac9-167">运行应用以验证主页是否显示。</span><span class="sxs-lookup"><span data-stu-id="aaac9-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="aaac9-168">数据模型</span><span class="sxs-lookup"><span data-stu-id="aaac9-168">The data model</span></span>

<span data-ttu-id="aaac9-169">以下部分用于创建数据模型：</span><span class="sxs-lookup"><span data-stu-id="aaac9-169">The following sections create a data model:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="aaac9-171">一名学生可以修读任意数量的课程，并且某一课程可以有任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="aaac9-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="aaac9-172">Student 实体</span><span class="sxs-lookup"><span data-stu-id="aaac9-172">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

* <span data-ttu-id="aaac9-174">在项目文件夹中创建“Models”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="aaac9-175">使用以下代码创建 Models/Student.cs  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="aaac9-176">`ID` 属性成为此类对应的数据库表的主键列。</span><span class="sxs-lookup"><span data-stu-id="aaac9-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="aaac9-177">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="aaac9-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="aaac9-178">因此，`Student` 类主键的另一种自动识别的名称是 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="aaac9-179">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="aaac9-180">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="aaac9-181">在本例中，`Student` 实体的 `Enrollments` 属性包含与该 Student 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="aaac9-182">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="aaac9-183">在数据库中，如果 StudentID 列包含学生的 ID 值，则 Enrollment 行与 Student 行相关。</span><span class="sxs-lookup"><span data-stu-id="aaac9-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="aaac9-184">例如，假设某个 Student 行的 ID=1。</span><span class="sxs-lookup"><span data-stu-id="aaac9-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="aaac9-185">则相关 Enrollment 行的 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="aaac9-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="aaac9-186">StudentID 是 Enrollment 表中的外键  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="aaac9-187">`Enrollments` 属性定义为 `ICollection<Enrollment>`，因为可能有多个相关的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="aaac9-188">可以使用 `List<Enrollment>` 或 `HashSet<Enrollment>` 等其他集合类型。</span><span class="sxs-lookup"><span data-stu-id="aaac9-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="aaac9-189">使用 `ICollection<Enrollment>` 时，EF Core 会默认创建 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="aaac9-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="aaac9-190">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="aaac9-190">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="aaac9-192">使用以下代码创建 Models/Enrollment.cs  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="aaac9-193">`EnrollmentID` 属性为主键；此实体使用 `classnameID` 模式而不是直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="aaac9-194">对于生产数据模型，请选择一个模式并一直使用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="aaac9-195">本教程两个都使用，只是为了说明这两个模式都能使用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="aaac9-196">使用不具有 `classname` 的 `ID` 可以更轻松地实现某些类型的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="aaac9-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="aaac9-197">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="aaac9-198">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 [null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="aaac9-199">评级为 null 和评级为零是有区别的 &mdash; null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="aaac9-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="aaac9-200">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="aaac9-201">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="aaac9-202">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="aaac9-203">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="aaac9-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="aaac9-204">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="aaac9-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="aaac9-205">例如，`StudentID` 是 `Student` 导航属性的外键，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="aaac9-206">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="aaac9-207">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="aaac9-208">Course 实体</span><span class="sxs-lookup"><span data-stu-id="aaac9-208">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="aaac9-210">使用以下代码创建 Models/Course.cs  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="aaac9-211">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="aaac9-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="aaac9-212">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="aaac9-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="aaac9-213">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="aaac9-214">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="aaac9-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="aaac9-215">搭建“学生”页的基架</span><span class="sxs-lookup"><span data-stu-id="aaac9-215">Scaffold Student pages</span></span>

<span data-ttu-id="aaac9-216">本部分使用 ASP.NET Core 基架工具生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="aaac9-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="aaac9-217">EF Core 上下文类  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-217">An EF Core *context* class.</span></span> <span data-ttu-id="aaac9-218">上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="aaac9-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="aaac9-219">它派生自 `Microsoft.EntityFrameworkCore.DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="aaac9-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="aaac9-220">Razor 页面可处理 `Student` 实体的创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaac9-222">在“Pages”文件夹中创建“Students”文件夹   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="aaac9-223">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹，然后选择“添加”>“新搭建基架的项目”     。</span><span class="sxs-lookup"><span data-stu-id="aaac9-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="aaac9-224">在“添加基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“添加”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="aaac9-225">在“使用实体框架(CRUD)添加 Razor Pages”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="aaac9-226">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="aaac9-227">在“数据上下文类”行中，选择 +（加号）   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="aaac9-228">将数据上下文名称从 ContosoUniversity.Models.ContosoUniversityContext 更改为 ContosoUniversity.Data.SchoolContext   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="aaac9-229">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-229">Select **Add**.</span></span>

<span data-ttu-id="aaac9-230">自动安装以下包：</span><span class="sxs-lookup"><span data-stu-id="aaac9-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aaac9-232">运行以下 .NET Core CLI 命令，安装所需的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="aaac9-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="aaac9-233">基架需要 Microsoft.VisualStudio.Web.CodeGeneration.Design 包。</span><span class="sxs-lookup"><span data-stu-id="aaac9-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="aaac9-234">虽然应用不使用 SQL Server，但基架工具需要 SQL Server 包。</span><span class="sxs-lookup"><span data-stu-id="aaac9-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="aaac9-235">创建“Pages/Students”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="aaac9-236">运行以下命令安装 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="aaac9-237">运行以下命令，搭建“学生”页的基架。</span><span class="sxs-lookup"><span data-stu-id="aaac9-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="aaac9-238">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="aaac9-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="aaac9-239">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="aaac9-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="aaac9-240">如果对上述步骤有疑问，请生成项目并重试基架搭建步骤。</span><span class="sxs-lookup"><span data-stu-id="aaac9-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="aaac9-241">基架流程：</span><span class="sxs-lookup"><span data-stu-id="aaac9-241">The scaffolding process:</span></span>

* <span data-ttu-id="aaac9-242">在“Pages/Students”文件夹中创建 Razor 页面  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="aaac9-243">Create.cshtml 和 Create.cshtml.cs  </span><span class="sxs-lookup"><span data-stu-id="aaac9-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="aaac9-244">Delete.cshtml 和 Delete.cshtml.cs  </span><span class="sxs-lookup"><span data-stu-id="aaac9-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="aaac9-245">Details.cshtml 和 Details.cshtml.cs  </span><span class="sxs-lookup"><span data-stu-id="aaac9-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="aaac9-246">Edit.cshtml 和 Edit.cshtml.cs  </span><span class="sxs-lookup"><span data-stu-id="aaac9-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="aaac9-247">Index.cshtml 和 Index.cshtml.cs  </span><span class="sxs-lookup"><span data-stu-id="aaac9-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="aaac9-248">创建 Data/SchoolContext.cs  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="aaac9-249">将上下文添加到 Startup.cs 中的依赖项注入  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="aaac9-250">将数据库连接字符串添加到 appsettings.json  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="aaac9-251">数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="aaac9-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaac9-253">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="aaac9-254">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="aaac9-255">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 文件  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aaac9-257">更改连接字符串以指向名为 CU.db 的 SQLite 数据库文件  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="aaac9-258">更新数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="aaac9-258">Update the database context class</span></span>

<span data-ttu-id="aaac9-259">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="aaac9-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="aaac9-260">上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="aaac9-261">上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="aaac9-262">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="aaac9-263">使用以下代码更新 SchoolContext.cs  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="aaac9-264">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="aaac9-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="aaac9-265">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="aaac9-265">In EF Core terminology:</span></span>

* <span data-ttu-id="aaac9-266">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="aaac9-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="aaac9-267">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="aaac9-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="aaac9-268">由于实体集包含多个实体，因此 DBSet 属性应为复数名称。</span><span class="sxs-lookup"><span data-stu-id="aaac9-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="aaac9-269">由于基架工具创建了 `Student` DBSet，因此此步骤将其更改为复数 `Students`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="aaac9-270">为了使 Razor Pages 代码与新的 DBSet 名称相匹配，请在整个项目中进行全局更改，将 `_context.Student` 更改为 `_context.Students`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="aaac9-271">更改发生 8 次。</span><span class="sxs-lookup"><span data-stu-id="aaac9-271">There are 8 occurrences.</span></span>

<span data-ttu-id="aaac9-272">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="aaac9-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="aaac9-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="aaac9-273">Startup.cs</span></span>

<span data-ttu-id="aaac9-274">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aaac9-275">在应用程序启动过程通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="aaac9-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="aaac9-276">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="aaac9-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aaac9-277">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="aaac9-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="aaac9-278">基架工具自动将上下文类注册到了依赖项注入容器。</span><span class="sxs-lookup"><span data-stu-id="aaac9-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaac9-280">在 `ConfigureServices` 中，基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="aaac9-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aaac9-282">在 `ConfigureServices` 中，确保基架所添加的代码调用 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="aaac9-283">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="aaac9-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aaac9-284">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="aaac9-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="aaac9-285">创建数据库</span><span class="sxs-lookup"><span data-stu-id="aaac9-285">Create the database</span></span>

<span data-ttu-id="aaac9-286">如果没有数据库，请更新 Program.cs 以创建数据库  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="aaac9-287">如果有上下文的数据库，则 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="aaac9-288">如果没有数据库，则它将创建数据库和架构。</span><span class="sxs-lookup"><span data-stu-id="aaac9-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="aaac9-289">`EnsureCreated` 启用以下工作流来处理数据模型更改：</span><span class="sxs-lookup"><span data-stu-id="aaac9-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="aaac9-290">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-290">Delete the database.</span></span> <span data-ttu-id="aaac9-291">任何现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="aaac9-291">Any existing data is lost.</span></span>
* <span data-ttu-id="aaac9-292">更改数据模型。</span><span class="sxs-lookup"><span data-stu-id="aaac9-292">Change the data model.</span></span> <span data-ttu-id="aaac9-293">例如，添加 `EmailAddress` 字段。</span><span class="sxs-lookup"><span data-stu-id="aaac9-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="aaac9-294">运行应用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-294">Run the app.</span></span>
* <span data-ttu-id="aaac9-295">`EnsureCreated` 创建具有新架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="aaac9-296">在无需保存数据的情况下，当架构快速发展时，此工作流在早期开发过程中表现良好。</span><span class="sxs-lookup"><span data-stu-id="aaac9-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="aaac9-297">如果需要保存已输入数据库的数据，情况就有所不同了。</span><span class="sxs-lookup"><span data-stu-id="aaac9-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="aaac9-298">如果是这种情况，请使用迁移。</span><span class="sxs-lookup"><span data-stu-id="aaac9-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="aaac9-299">本系列教程的后续部分将删除 `EnsureCreated` 创建的数据库，转而使用迁移。</span><span class="sxs-lookup"><span data-stu-id="aaac9-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="aaac9-300">无法使用迁移更新 `EnsureCreated` 创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="aaac9-301">测试应用</span><span class="sxs-lookup"><span data-stu-id="aaac9-301">Test the app</span></span>

* <span data-ttu-id="aaac9-302">运行应用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-302">Run the app.</span></span>
* <span data-ttu-id="aaac9-303">依次选择“学生”链接、“新建”   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="aaac9-304">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="aaac9-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="aaac9-305">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="aaac9-305">Seed the database</span></span>

<span data-ttu-id="aaac9-306">`EnsureCreated` 方法将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="aaac9-307">本节添加用测试数据填充数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="aaac9-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="aaac9-308">使用以下代码创建 Data/DbInitializer.cs  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-308">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="aaac9-309">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="aaac9-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="aaac9-310">如果不存在学生，它将向数据库添加测试数据。</span><span class="sxs-lookup"><span data-stu-id="aaac9-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="aaac9-311">该代码使用数组创建测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="aaac9-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="aaac9-312">在 Program.cs 中，将 `EnsureCreated` 调用替换为 `DbInitializer.Initialize` 调用  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaac9-314">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aaac9-316">如果应用正在运行，则停止应用，然后删除 CU.db 文件  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="aaac9-317">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-317">Restart the app.</span></span>

* <span data-ttu-id="aaac9-318">选择“学生”页查看已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="aaac9-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="aaac9-319">查看数据库</span><span class="sxs-lookup"><span data-stu-id="aaac9-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaac9-321">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX)   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="aaac9-322">在 SSOX 中，依次选择“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="aaac9-323">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="aaac9-324">展开“表”节点  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="aaac9-325">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="aaac9-326">右键单击 Student 表，然后单击“查看代码”查看 `Student` 模型如何映射到 `Student` 表架构   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aaac9-328">使用 SQLite 工具查看数据库架构和已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="aaac9-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="aaac9-329">数据库文件名为 CU.db，位于项目文件夹  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="aaac9-330">异步代码</span><span class="sxs-lookup"><span data-stu-id="aaac9-330">Asynchronous code</span></span>

<span data-ttu-id="aaac9-331">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="aaac9-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="aaac9-332">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="aaac9-333">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="aaac9-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="aaac9-334">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="aaac9-335">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="aaac9-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="aaac9-336">因此，使用异步代码可以更有效地利用服务器资源，并且服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="aaac9-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="aaac9-337">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="aaac9-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="aaac9-338">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="aaac9-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="aaac9-339">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="aaac9-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="aaac9-340">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="aaac9-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="aaac9-341">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="aaac9-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="aaac9-342">创建返回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 对象。</span><span class="sxs-lookup"><span data-stu-id="aaac9-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="aaac9-343">返回类型 `Task<T>` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="aaac9-344">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="aaac9-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="aaac9-345">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="aaac9-346">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="aaac9-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="aaac9-347">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="aaac9-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="aaac9-348">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="aaac9-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="aaac9-349">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="aaac9-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="aaac9-350">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="aaac9-351">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="aaac9-352">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="aaac9-353">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="aaac9-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="aaac9-354">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="aaac9-355">后续步骤</span><span class="sxs-lookup"><span data-stu-id="aaac9-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="aaac9-356">下一教程</span><span class="sxs-lookup"><span data-stu-id="aaac9-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aaac9-357">Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 创建 ASP.NET Core Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="aaac9-358">该示例应用是一个虚构的 Contoso University 的网站。</span><span class="sxs-lookup"><span data-stu-id="aaac9-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="aaac9-359">其中包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="aaac9-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="aaac9-360">本页是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。</span><span class="sxs-lookup"><span data-stu-id="aaac9-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="aaac9-361">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-361">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="aaac9-362">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aaac9-363">先决条件</span><span class="sxs-lookup"><span data-stu-id="aaac9-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="aaac9-366">熟悉 [Razor 页面](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="aaac9-367">新程序员在开始学习本系列之前，应先完成 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="aaac9-368">疑难解答</span><span class="sxs-lookup"><span data-stu-id="aaac9-368">Troubleshooting</span></span>

<span data-ttu-id="aaac9-369">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="aaac9-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="aaac9-370">获取帮助的一个好方法是将问题发布到适用于 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 的 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="aaac9-371">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="aaac9-371">The Contoso University web app</span></span>

<span data-ttu-id="aaac9-372">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="aaac9-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="aaac9-373">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="aaac9-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="aaac9-374">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="aaac9-374">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

<span data-ttu-id="aaac9-377">此网站的 UI 样式与内置模板生成的 UI 样式类似。</span><span class="sxs-lookup"><span data-stu-id="aaac9-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="aaac9-378">教程的重点是 EF Core 和 Razor 页面，而非 UI。</span><span class="sxs-lookup"><span data-stu-id="aaac9-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="aaac9-379">创建 ContosoUniversity Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="aaac9-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaac9-381">从 Visual Studio“文件”  菜单中选择“新建”  >“项目”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="aaac9-382">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="aaac9-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="aaac9-383">将该项目命名为 ContosoUniversity  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="aaac9-384">务必将该项目命名为 ContosoUniversity，以便复制/粘贴代码时命名空间相匹配  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="aaac9-385">在下拉列表中选择“ASP.NET Core 2.1”，然后选择“Web 应用程序”   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="aaac9-386">有关上述步骤的图像，请参阅[创建 Razor Web 应用](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="aaac9-387">运行应用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="aaac9-389">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="aaac9-389">Set up the site style</span></span>

<span data-ttu-id="aaac9-390">设置网站菜单、布局和主页时需作少量更改。</span><span class="sxs-lookup"><span data-stu-id="aaac9-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="aaac9-391">进行以下更改以更新 Pages/Shared/_Layout.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="aaac9-392">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="aaac9-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="aaac9-393">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="aaac9-393">There are three occurrences.</span></span>

* <span data-ttu-id="aaac9-394">添加菜单项 **Students**，**Courses**，**Instructors**，和 **Department**，并删除 **Contact**菜单项。</span><span class="sxs-lookup"><span data-stu-id="aaac9-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="aaac9-395">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="aaac9-395">The changes are highlighted.</span></span> <span data-ttu-id="aaac9-396">（没有显示全部标记  。）</span><span class="sxs-lookup"><span data-stu-id="aaac9-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="aaac9-397">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET 和 MVC 的文本替换为有关本应用的文本  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="aaac9-398">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="aaac9-398">Create the data model</span></span>

<span data-ttu-id="aaac9-399">创建 Contoso University 应用的实体类。</span><span class="sxs-lookup"><span data-stu-id="aaac9-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="aaac9-400">从以下三个实体开始：</span><span class="sxs-lookup"><span data-stu-id="aaac9-400">Start with the following three entities:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="aaac9-402">`Student` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="aaac9-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="aaac9-403">`Course` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="aaac9-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="aaac9-404">一名学生可以报名参加任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="aaac9-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="aaac9-405">一门课程中可以包含任意数量的学生。</span><span class="sxs-lookup"><span data-stu-id="aaac9-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="aaac9-406">以下部分将为这几个实体中的每一个实体创建一个类。</span><span class="sxs-lookup"><span data-stu-id="aaac9-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="aaac9-407">Student 实体</span><span class="sxs-lookup"><span data-stu-id="aaac9-407">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="aaac9-409">创建 Models 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-409">Create a *Models* folder.</span></span> <span data-ttu-id="aaac9-410">在 Models 文件夹中，使用以下代码创建一个名为 Student.cs 的类文件   ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="aaac9-411">`ID` 属性成为此类对应的数据库 (DB) 表的主键列。</span><span class="sxs-lookup"><span data-stu-id="aaac9-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="aaac9-412">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="aaac9-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="aaac9-413">在 `classnameID` 中，`classname` 为类名称。</span><span class="sxs-lookup"><span data-stu-id="aaac9-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="aaac9-414">另一种自动识别的主键是上例中的 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="aaac9-415">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="aaac9-416">导航属性链接到与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="aaac9-417">在这种情况下，`Student entity` 的 `Enrollments` 属性包含与该 `Student` 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="aaac9-418">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="aaac9-419">相关的 `Enrollment` 行是 `StudentID` 列中包含该学生的主键值的行。</span><span class="sxs-lookup"><span data-stu-id="aaac9-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="aaac9-420">例如，假设 ID=1 的学生在 `Enrollment` 表中有两行。</span><span class="sxs-lookup"><span data-stu-id="aaac9-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="aaac9-421">`Enrollment` 表中有两行的 `StudentID` = 1。</span><span class="sxs-lookup"><span data-stu-id="aaac9-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="aaac9-422">`StudentID` 是 `Enrollment` 表中的外键，用于指定 `Student` 表中的学生。</span><span class="sxs-lookup"><span data-stu-id="aaac9-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="aaac9-423">如果导航属性包含多个实体，则导航属性必须是列表类型，例如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="aaac9-424">可以指定 `ICollection<T>` 或诸如 `List<T>` 或 `HashSet<T>` 的类型。</span><span class="sxs-lookup"><span data-stu-id="aaac9-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="aaac9-425">使用 `ICollection<T>` 时，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="aaac9-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="aaac9-426">包含多个实体的导航属性来自于多对多和一对多关系。</span><span class="sxs-lookup"><span data-stu-id="aaac9-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="aaac9-427">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="aaac9-427">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="aaac9-429">在 Models 文件夹中，使用以下代码创建 Enrollment.cs   ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="aaac9-430">`EnrollmentID` 属性为主键。</span><span class="sxs-lookup"><span data-stu-id="aaac9-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="aaac9-431">`Student` 实体使用的是 `ID` 模式，而本实体使用的是 `classnameID` 模式。</span><span class="sxs-lookup"><span data-stu-id="aaac9-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="aaac9-432">通常情况下，开发者会选择一种模式并在整个数据模型中都使用该模式。</span><span class="sxs-lookup"><span data-stu-id="aaac9-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="aaac9-433">下一个教程将介绍如何使用不带类名的 ID，以便更轻松地在数据模型中实现集成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="aaac9-434">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="aaac9-435">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="aaac9-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="aaac9-436">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="aaac9-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="aaac9-437">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="aaac9-438">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="aaac9-439">`Student` 实体与 `Student.Enrollments` 导航属性不同，后者包含多个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="aaac9-440">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="aaac9-441">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="aaac9-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="aaac9-442">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="aaac9-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="aaac9-443">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="aaac9-444">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="aaac9-445">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="aaac9-446">Course 实体</span><span class="sxs-lookup"><span data-stu-id="aaac9-446">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="aaac9-448">在 Models 文件夹中，使用以下代码创建 Course.cs   ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="aaac9-449">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="aaac9-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="aaac9-450">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="aaac9-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="aaac9-451">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="aaac9-452">为“学生”模型搭建基架</span><span class="sxs-lookup"><span data-stu-id="aaac9-452">Scaffold the student model</span></span>

<span data-ttu-id="aaac9-453">本部分将为“学生”模型搭建基架。</span><span class="sxs-lookup"><span data-stu-id="aaac9-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="aaac9-454">确切地说，基架工具将生成页面，用于对“学生”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="aaac9-455">生成项目。</span><span class="sxs-lookup"><span data-stu-id="aaac9-455">Build the project.</span></span>
* <span data-ttu-id="aaac9-456">创建 Pages/Students 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaac9-458">在“解决方案资源管理器”  中，右键单击“Pages/Students”  文件夹 >“添加”  >“新搭建基架的项目”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="aaac9-459">在“添加基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”  >“添加”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="aaac9-460">完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="aaac9-461">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="aaac9-462">在“数据上下文类”行中，选择加号 (+) 并将生成的名称更改为 ContosoUniversity.Models.SchoolContext    。</span><span class="sxs-lookup"><span data-stu-id="aaac9-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="aaac9-463">在“数据上下文类”下拉列表中，选择“ContosoUniversity.Models.SchoolContext”  </span><span class="sxs-lookup"><span data-stu-id="aaac9-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="aaac9-464">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-464">Select **Add**.</span></span>

![CRUD 对话框](intro/_static/s1.png)

<span data-ttu-id="aaac9-466">如果对前面的步骤有疑问，请参阅[搭建“电影”模型的基架](xref:tutorials/razor-pages/model#scaffold-the-movie-model)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aaac9-468">运行以下命令，搭建“学生”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="aaac9-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="aaac9-469">搭建基架的过程会创建并更改以下文件：</span><span class="sxs-lookup"><span data-stu-id="aaac9-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="aaac9-470">创建的文件</span><span class="sxs-lookup"><span data-stu-id="aaac9-470">Files created</span></span>

* <span data-ttu-id="aaac9-471">Pages/Students：“创建”、“删除”、“详细信息”、“编辑”、“索引”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="aaac9-472">Data/SchoolContext.cs </span><span class="sxs-lookup"><span data-stu-id="aaac9-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="aaac9-473">更新的文件</span><span class="sxs-lookup"><span data-stu-id="aaac9-473">File updates</span></span>

* <span data-ttu-id="aaac9-474">*Startup.cs*：下一部分详细介绍对此文件所作的更改。</span><span class="sxs-lookup"><span data-stu-id="aaac9-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="aaac9-475">*appsettings.json*：添加用于连接到本地数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="aaac9-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="aaac9-476">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="aaac9-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="aaac9-477">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aaac9-478">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="aaac9-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="aaac9-479">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="aaac9-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aaac9-480">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="aaac9-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="aaac9-481">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="aaac9-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="aaac9-482">在 Startup.cs 中检查 `ConfigureServices` 方法  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="aaac9-483">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="aaac9-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="aaac9-484">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="aaac9-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aaac9-485">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="aaac9-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="aaac9-486">更新 main</span><span class="sxs-lookup"><span data-stu-id="aaac9-486">Update main</span></span>

<span data-ttu-id="aaac9-487">在 Program.cs 中，修改 `Main` 方法以执行以下操作  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="aaac9-488">从依赖关系注入容器获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="aaac9-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="aaac9-489">调用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="aaac9-490">`EnsureCreated` 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="aaac9-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="aaac9-491">下面的代码显示更新后的 Program.cs  文件。</span><span class="sxs-lookup"><span data-stu-id="aaac9-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="aaac9-492">`EnsureCreated` 确保存在上下文数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="aaac9-493">如果存在，则不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-493">If it exists, no action is taken.</span></span> <span data-ttu-id="aaac9-494">如果不存在，则会创建数据库及其所有架构。</span><span class="sxs-lookup"><span data-stu-id="aaac9-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="aaac9-495">`EnsureCreated` 不使用迁移创建数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="aaac9-496">使用 `EnsureCreated` 创建的数据库稍后无法使用迁移更新。</span><span class="sxs-lookup"><span data-stu-id="aaac9-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="aaac9-497">启动应用时会调用 `EnsureCreated`，以进行以下工作流：</span><span class="sxs-lookup"><span data-stu-id="aaac9-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="aaac9-498">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-498">Delete the DB.</span></span>
* <span data-ttu-id="aaac9-499">更改数据库架构（例如添加一个 `EmailAddress` 字段）。</span><span class="sxs-lookup"><span data-stu-id="aaac9-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="aaac9-500">运行应用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-500">Run the app.</span></span>
* <span data-ttu-id="aaac9-501">`EnsureCreated` 创建一个带有 `EmailAddress` 列的数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="aaac9-502">架构快速演变时，在开发初期使用 `EnsureCreated` 很方便。</span><span class="sxs-lookup"><span data-stu-id="aaac9-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="aaac9-503">本教程后面将删除 DB 并使用迁移。</span><span class="sxs-lookup"><span data-stu-id="aaac9-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="aaac9-504">测试应用</span><span class="sxs-lookup"><span data-stu-id="aaac9-504">Test the app</span></span>

<span data-ttu-id="aaac9-505">运行应用并接受 cookie 策略。</span><span class="sxs-lookup"><span data-stu-id="aaac9-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="aaac9-506">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="aaac9-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="aaac9-507">有关 cookie 策略的信息，请参阅[欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="aaac9-508">依次选择“学生”链接、“新建”   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="aaac9-509">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="aaac9-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="aaac9-510">检查 SchoolContext DB 上下文</span><span class="sxs-lookup"><span data-stu-id="aaac9-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="aaac9-511">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="aaac9-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="aaac9-512">数据上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="aaac9-513">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="aaac9-514">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="aaac9-515">使用以下代码更新 SchoolContext.cs  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="aaac9-516">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="aaac9-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="aaac9-517">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="aaac9-517">In EF Core terminology:</span></span>

* <span data-ttu-id="aaac9-518">实体集通常对应一个数据库表。</span><span class="sxs-lookup"><span data-stu-id="aaac9-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="aaac9-519">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="aaac9-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="aaac9-520">可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="aaac9-521">EF Core 隐式包含了它们，因为 `Student` 实体引用 `Enrollment` 实体，而 `Enrollment` 实体引用 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="aaac9-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="aaac9-522">在本教程中，将 `DbSet<Enrollment>` 和 `DbSet<Course>` 保留在 `SchoolContext` 中。</span><span class="sxs-lookup"><span data-stu-id="aaac9-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="aaac9-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="aaac9-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="aaac9-524">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="aaac9-525">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="aaac9-526">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="aaac9-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="aaac9-527">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="aaac9-528">添加代码，以使用测试数据初始化该数据库</span><span class="sxs-lookup"><span data-stu-id="aaac9-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="aaac9-529">EF Core 会创建一个空的数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="aaac9-530">本部分中编写了 `Initialize` 方法来使用测试数据填充该数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="aaac9-531">在 Data 文件夹中，新建一个名为 DbInitializer.cs 的类文件，并添加以下代码   ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="aaac9-532">注意：上面的代码对命名空间使用 `Models` (`namespace ContosoUniversity.Models`)，而不是 `Data`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="aaac9-533">`Models` 与基架生成的代码一致。</span><span class="sxs-lookup"><span data-stu-id="aaac9-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="aaac9-534">有关详细信息，请参阅[此 GitHub 基架问题](https://github.com/aspnet/Scaffolding/issues/822)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="aaac9-535">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="aaac9-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="aaac9-536">如果 DB 中没有任何学生，则会使用测试数据初始化该 DB。</span><span class="sxs-lookup"><span data-stu-id="aaac9-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="aaac9-537">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="aaac9-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="aaac9-538">`EnsureCreated` 方法自动为数据库上下文创建数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="aaac9-539">如果数据库已存在，则返回 `EnsureCreated`，并且不修改数据库。</span><span class="sxs-lookup"><span data-stu-id="aaac9-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="aaac9-540">在 Program.cs 中，将 `Main` 方法修改为调用 `Initialize`  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="aaac9-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaac9-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aaac9-542">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="aaac9-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaac9-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaac9-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aaac9-544">如果应用正在运行，则停止应用，然后删除 CU.db 文件  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="aaac9-545">查看数据库</span><span class="sxs-lookup"><span data-stu-id="aaac9-545">View the DB</span></span>

<span data-ttu-id="aaac9-546">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="aaac9-547">因此，数据库名称为“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="aaac9-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="aaac9-548">GUID 因用户而异。</span><span class="sxs-lookup"><span data-stu-id="aaac9-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="aaac9-549">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX)   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="aaac9-550">在 SSOX 中，依次单击“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="aaac9-551">展开“表”节点  。</span><span class="sxs-lookup"><span data-stu-id="aaac9-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="aaac9-552">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行   。</span><span class="sxs-lookup"><span data-stu-id="aaac9-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="aaac9-553">异步代码</span><span class="sxs-lookup"><span data-stu-id="aaac9-553">Asynchronous code</span></span>

<span data-ttu-id="aaac9-554">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="aaac9-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="aaac9-555">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="aaac9-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="aaac9-556">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="aaac9-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="aaac9-557">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="aaac9-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="aaac9-558">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="aaac9-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="aaac9-559">因此，使用异步代码可以更有效地利用服务器资源，并且可以让服务器在没有延迟的情况下处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="aaac9-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="aaac9-560">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="aaac9-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="aaac9-561">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="aaac9-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="aaac9-562">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="aaac9-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="aaac9-563">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="aaac9-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="aaac9-564">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="aaac9-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="aaac9-565">自动创建返回的 [Task](/dotnet/api/system.threading.tasks.task) 对象。</span><span class="sxs-lookup"><span data-stu-id="aaac9-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="aaac9-566">有关详细信息，请参阅[任务返回类型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="aaac9-567">隐式返回类型 `Task` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="aaac9-568">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="aaac9-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="aaac9-569">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="aaac9-570">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="aaac9-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="aaac9-571">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="aaac9-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="aaac9-572">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="aaac9-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="aaac9-573">只会异步执行导致查询或命令被发送到数据库的语句。</span><span class="sxs-lookup"><span data-stu-id="aaac9-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="aaac9-574">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="aaac9-575">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="aaac9-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="aaac9-576">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="aaac9-577">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="aaac9-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="aaac9-578">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="aaac9-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="aaac9-579">下一个教程将介绍基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="aaac9-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="aaac9-580">其他资源</span><span class="sxs-lookup"><span data-stu-id="aaac9-580">Additional resources</span></span>

* [<span data-ttu-id="aaac9-581">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="aaac9-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="aaac9-582">下一页</span><span class="sxs-lookup"><span data-stu-id="aaac9-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
