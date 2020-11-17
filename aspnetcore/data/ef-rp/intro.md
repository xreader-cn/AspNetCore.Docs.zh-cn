---
title: ASP.NET Core 中的 Razor Pages 和 Entity Framework Core - 第 1 个教程（共 8 个）
author: rick-anderson
description: 介绍如何使用 Entity Framework Core 创建 Razor Pages 应用
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 5849f4bfb9d0355177ceb5c2f4236c6d39dd9e92
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340031"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="1f57c-103">ASP.NET Core 中的 Razor Pages 和 Entity Framework Core - 第 1 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="1f57c-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="1f57c-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f57c-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1f57c-105">本文是系列教程的第一篇，这些教程展示如何在 [ASP.NET Core Razor Pages](xref:razor-pages/index) 应用中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="1f57c-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="1f57c-106">这些教程为虚构的 Contoso University 生成一个网站。</span><span class="sxs-lookup"><span data-stu-id="1f57c-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1f57c-107">网站包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="1f57c-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1f57c-108">本教程使用代码优先方法。</span><span class="sxs-lookup"><span data-stu-id="1f57c-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="1f57c-109">有关使用数据库优先方法学习本教程的信息，请参阅[此 Github 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="1f57c-110">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1f57c-111">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f57c-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="1f57c-112">Prerequisites</span></span>

* <span data-ttu-id="1f57c-113">如果不熟悉 Razor Pages，则在开始前，请浏览 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)系列教程。</span><span class="sxs-lookup"><span data-stu-id="1f57c-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="1f57c-116">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="1f57c-116">Database engines</span></span>

<span data-ttu-id="1f57c-117">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="1f57c-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="1f57c-118">Visual Studio Code 指令使用 [SQLite](https://www.sqlite.org/)，一种跨平台数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="1f57c-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="1f57c-119">如果选择使用 SQLite，请下载并安装[适用于 SQLite 的数据库浏览器](https://sqlitebrowser.org/)等第三方工具，用于管理和查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1f57c-120">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1f57c-120">Troubleshooting</span></span>

<span data-ttu-id="1f57c-121">如果遇到无法解决的问题，请将你的代码与[完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)进行比较。</span><span class="sxs-lookup"><span data-stu-id="1f57c-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1f57c-122">获取帮助的一个好方法是使用 [ASP.NET Core 标记](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 标记](https://stackoverflow.com/questions/tagged/entity-framework-core)将问题发布到 StackOverflow.com。</span><span class="sxs-lookup"><span data-stu-id="1f57c-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="1f57c-123">示例应用</span><span class="sxs-lookup"><span data-stu-id="1f57c-123">The sample app</span></span>

<span data-ttu-id="1f57c-124">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="1f57c-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="1f57c-125">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="1f57c-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1f57c-126">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="1f57c-126">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index30.png)

![学生编辑页](intro/_static/student-edit30.png)

<span data-ttu-id="1f57c-129">此网站的 UI 样式基于内置的项目模板。</span><span class="sxs-lookup"><span data-stu-id="1f57c-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="1f57c-130">本教程侧重于如何将 EF Core 和 ASP.NET Core 结合在一起使用，而不是如何自定义 UI。</span><span class="sxs-lookup"><span data-stu-id="1f57c-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="1f57c-131">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="1f57c-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1f57c-133">启动 Visual Studio 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="1f57c-134">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="1f57c-135">在“配置新项目”对话框中，为“项目名称”输入 `ContosoUniversity`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="1f57c-136">请务必使用此名称（含大写），确保在复制代码时与每个 `namespace` 都相匹配。</span><span class="sxs-lookup"><span data-stu-id="1f57c-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="1f57c-137">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-137">Select **Create**.</span></span>
1. <span data-ttu-id="1f57c-138">在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：</span><span class="sxs-lookup"><span data-stu-id="1f57c-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="1f57c-139">下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="1f57c-140">ASP.NET Core Web 应用程序（模型-视图-控制器）。</span><span class="sxs-lookup"><span data-stu-id="1f57c-140">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="1f57c-141">“创建”
      ![新的 ASP.NET Core 项目对话框](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="1f57c-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f57c-143">在终端中，导航到应在其中创建项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="1f57c-144">运行以下命令，在新的项目文件夹中创建 Razor Pages 项目和 `cd`：</span><span class="sxs-lookup"><span data-stu-id="1f57c-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1f57c-145">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="1f57c-145">Set up the site style</span></span>

<span data-ttu-id="1f57c-146">将下面的代码复制并粘贴到 Pages/Shared/_Layout.cshtml 文件中：[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="1f57c-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="1f57c-147">布局文件会设置网站页眉、页脚和菜单。</span><span class="sxs-lookup"><span data-stu-id="1f57c-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="1f57c-148">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="1f57c-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1f57c-149">将文件中的“ContosoUniversity”更改为“Contoso University”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1f57c-150">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="1f57c-150">There are three occurrences.</span></span>
* <span data-ttu-id="1f57c-151">删除“主页”和“隐私”菜单项。</span><span class="sxs-lookup"><span data-stu-id="1f57c-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="1f57c-152">添加“关于”、“学生”、“课程”、“讲师”和“部门”项。</span><span class="sxs-lookup"><span data-stu-id="1f57c-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="1f57c-153">在 Pages/Index.cshtml 中，将该文件的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="1f57c-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="1f57c-154">前面的代码会将关于 ASP.NET Core 的文本替换为关于此应用的文本。</span><span class="sxs-lookup"><span data-stu-id="1f57c-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="1f57c-155">运行应用以验证主页是否显示。</span><span class="sxs-lookup"><span data-stu-id="1f57c-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="1f57c-156">数据模型</span><span class="sxs-lookup"><span data-stu-id="1f57c-156">The data model</span></span>

<span data-ttu-id="1f57c-157">以下部分用于创建数据模型：</span><span class="sxs-lookup"><span data-stu-id="1f57c-157">The following sections create a data model:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="1f57c-159">一名学生可以修读任意数量的课程，并且某一课程可以有任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="1f57c-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="1f57c-160">Student 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-160">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

* <span data-ttu-id="1f57c-162">在项目文件夹中创建“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="1f57c-163">使用以下代码创建 Models/Student.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="1f57c-164">`ID` 属性成为此类对应的数据库表的主键列。</span><span class="sxs-lookup"><span data-stu-id="1f57c-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="1f57c-165">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1f57c-166">因此，`Student` 类主键的另一种自动识别的名称是 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="1f57c-167">有关详细信息，请参阅 [F Core - 密钥](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="1f57c-168">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1f57c-169">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="1f57c-170">在本例中，`Student` 实体的 `Enrollments` 属性包含与该 Student 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="1f57c-171">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="1f57c-172">在数据库中，如果 StudentID 列包含学生的 ID 值，则 Enrollment 行与 Student 行相关。</span><span class="sxs-lookup"><span data-stu-id="1f57c-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="1f57c-173">例如，假设某个 Student 行的 ID=1。</span><span class="sxs-lookup"><span data-stu-id="1f57c-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="1f57c-174">则相关 Enrollment 行的 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="1f57c-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="1f57c-175">StudentID 是 Enrollment 表中的外键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="1f57c-176">`Enrollments` 属性定义为 `ICollection<Enrollment>`，因为可能有多个相关的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="1f57c-177">可以使用 `List<Enrollment>` 或 `HashSet<Enrollment>` 等其他集合类型。</span><span class="sxs-lookup"><span data-stu-id="1f57c-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="1f57c-178">使用 `ICollection<Enrollment>` 时，EF Core 会默认创建 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="1f57c-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="1f57c-179">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-179">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="1f57c-181">使用以下代码创建 Models/Enrollment.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="1f57c-182">`EnrollmentID` 属性为主键；此实体使用 `classnameID` 模式而不是直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="1f57c-183">对于生产数据模型，请选择一个模式并一直使用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="1f57c-184">本教程两个都使用，只是为了说明这两个模式都能使用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="1f57c-185">使用不具有 `classname` 的 `ID` 可以更轻松地实现某些类型的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="1f57c-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="1f57c-186">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1f57c-187">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 [null](/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="1f57c-188">评级为 null 和评级为零是有区别的 &mdash; null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="1f57c-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1f57c-189">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1f57c-190">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="1f57c-191">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1f57c-192">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="1f57c-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1f57c-193">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1f57c-194">例如，`StudentID` 是 `Student` 导航属性的外键，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1f57c-195">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1f57c-196">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="1f57c-197">Course 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-197">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="1f57c-199">使用以下代码创建 Models/Course.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="1f57c-200">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="1f57c-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1f57c-201">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="1f57c-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1f57c-202">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="1f57c-203">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="1f57c-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="1f57c-204">搭建“学生”页的基架</span><span class="sxs-lookup"><span data-stu-id="1f57c-204">Scaffold Student pages</span></span>

<span data-ttu-id="1f57c-205">本部分使用 ASP.NET Core 基架工具生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="1f57c-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="1f57c-206">EF Core `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="1f57c-207">上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="1f57c-208">它派生自 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="1f57c-209">Razor 页面，可处理 `Student` 实体的创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f57c-211">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="1f57c-212">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹，然后选择“添加”>“新搭建基架的项目” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1f57c-213">在“添加新基架项”对话框中：</span><span class="sxs-lookup"><span data-stu-id="1f57c-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="1f57c-214">在左侧选项卡中，依次选择“已安装 > 通用 > Razor 页面”</span><span class="sxs-lookup"><span data-stu-id="1f57c-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="1f57c-215">依次选择“使用实体框架的 Razor 页面(CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="1f57c-216">在“添加使用实体框架的 Razor Pages (CRUD)”对话框中：</span><span class="sxs-lookup"><span data-stu-id="1f57c-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="1f57c-217">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="1f57c-218">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="1f57c-219">将数据上下文名称更改为以 `SchoolContext` 结尾，而不以 `ContosoUniversityContext` 结尾。</span><span class="sxs-lookup"><span data-stu-id="1f57c-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="1f57c-220">更新后的上下文名称为：`ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="1f57c-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="1f57c-221">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-221">Select **Add**.</span></span>

<span data-ttu-id="1f57c-222">自动安装以下包：</span><span class="sxs-lookup"><span data-stu-id="1f57c-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f57c-224">运行以下 .NET Core CLI 命令，安装所需的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="1f57c-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="1f57c-225">基架需要 Microsoft.VisualStudio.Web.CodeGeneration.Design 包。</span><span class="sxs-lookup"><span data-stu-id="1f57c-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="1f57c-226">虽然应用不使用 SQL Server，但基架工具需要 SQL Server 包。</span><span class="sxs-lookup"><span data-stu-id="1f57c-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="1f57c-227">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="1f57c-228">运行以下命令安装 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="1f57c-229">运行以下命令，搭建“学生”页的基架。</span><span class="sxs-lookup"><span data-stu-id="1f57c-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="1f57c-230">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="1f57c-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="1f57c-231">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="1f57c-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="1f57c-232">如果上述步骤失败，请生成项目并重试基架搭建步骤。</span><span class="sxs-lookup"><span data-stu-id="1f57c-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="1f57c-233">基架流程：</span><span class="sxs-lookup"><span data-stu-id="1f57c-233">The scaffolding process:</span></span>

* <span data-ttu-id="1f57c-234">在“Pages/Students”文件夹中创建 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="1f57c-234">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="1f57c-235">Create.cshtml 和 Create.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="1f57c-236">Delete.cshtml 和 Delete.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="1f57c-237">Details.cshtml 和 Details.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="1f57c-238">Edit.cshtml 和 Edit.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="1f57c-239">Index.cshtml 和 Index.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="1f57c-240">创建 Data/SchoolContext.cs。</span><span class="sxs-lookup"><span data-stu-id="1f57c-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="1f57c-241">将上下文添加到 Startup.cs 中的依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="1f57c-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="1f57c-242">将数据库连接字符串添加到 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-242">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="1f57c-243">数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="1f57c-243">Database connection string</span></span>

<span data-ttu-id="1f57c-244">基架工具会在 *appsettings.json* 文件中生成连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1f57c-244">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f57c-246">此连接字符串指定了 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)：</span><span class="sxs-lookup"><span data-stu-id="1f57c-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="1f57c-247">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1f57c-248">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 文件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f57c-250">将 SQLite 连接字符串缩写为 CU.db：</span><span class="sxs-lookup"><span data-stu-id="1f57c-250">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="1f57c-251">更新数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="1f57c-251">Update the database context class</span></span>

<span data-ttu-id="1f57c-252">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="1f57c-253">上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1f57c-254">上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1f57c-255">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1f57c-256">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="1f57c-257">上述代码会将单数形式的 `DbSet<Student> Student` 更改为复数形式的 `DbSet<Student> Students`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="1f57c-258">若要使 Razor 页面代码与新的 `DBSet` 名称匹配，请进行以下全局更改：`_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="1f57c-258">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="1f57c-259">更改为 `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="1f57c-259">to: `_context.Students.`</span></span>

<span data-ttu-id="1f57c-260">更改发生 8 次。</span><span class="sxs-lookup"><span data-stu-id="1f57c-260">There are 8 occurrences.</span></span>

<span data-ttu-id="1f57c-261">由于一个实体集包含多个实体，因此许多开发人员更倾向于使用复数形式的 `DBSet` 属性名称。</span><span class="sxs-lookup"><span data-stu-id="1f57c-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="1f57c-262">突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="1f57c-262">The highlighted code:</span></span>

* <span data-ttu-id="1f57c-263">为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="1f57c-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1f57c-264">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="1f57c-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="1f57c-265">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="1f57c-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="1f57c-266">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="1f57c-267">调用 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>。</span><span class="sxs-lookup"><span data-stu-id="1f57c-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="1f57c-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="1f57c-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="1f57c-269">在完成对 `SchoolContext` 的初始化后，并在模型已锁定并用于初始化上下文之前，进行调用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="1f57c-270">是必需的，因为在本教程的后续部分中，`Student` 实体将引用其他实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="1f57c-271">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="1f57c-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="1f57c-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1f57c-272">Startup.cs</span></span>

<span data-ttu-id="1f57c-273">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1f57c-274">服务（例如 `SchoolContext`）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="1f57c-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="1f57c-275">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="1f57c-275">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="1f57c-276">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="1f57c-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1f57c-277">基架工具自动将上下文类注册到了依赖项注入容器。</span><span class="sxs-lookup"><span data-stu-id="1f57c-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-278">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f57c-279">基架添加了下列突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="1f57c-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f57c-281">验证基架所添加的代码是否调用 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="1f57c-282">有关使用生产数据库的信息，请参阅[将 SQLite 用于开发，将 SQL Server 用于生产](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="1f57c-283">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="1f57c-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1f57c-284">进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1f57c-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="1f57c-285">添加数据库异常筛选器</span><span class="sxs-lookup"><span data-stu-id="1f57c-285">Add the database exception filter</span></span>

<span data-ttu-id="1f57c-286">将 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> 添加到 `ConfigureServices`，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="1f57c-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="1f57c-288">添加 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="1f57c-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="1f57c-289">在 PMC 中，输入以下命令来添加此 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="1f57c-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="1f57c-291">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 包提供 Entity Framework Core 错误页的 ASP.NET Core 中间件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="1f57c-292">此中间件有助于检测和诊断 Entity Framework Core 迁移错误。</span><span class="sxs-lookup"><span data-stu-id="1f57c-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="1f57c-293">`AddDatabaseDeveloperPageExceptionFilter` 在[开发环境](xref:fundamentals/environments)中提供有用的错误信息。</span><span class="sxs-lookup"><span data-stu-id="1f57c-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="1f57c-294">创建数据库</span><span class="sxs-lookup"><span data-stu-id="1f57c-294">Create the database</span></span>

<span data-ttu-id="1f57c-295">如果没有数据库，请更新 Program.cs 以创建数据库：</span><span class="sxs-lookup"><span data-stu-id="1f57c-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="1f57c-296">如果有上下文的数据库，则 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="1f57c-297">如果没有数据库，则它将创建数据库和架构。</span><span class="sxs-lookup"><span data-stu-id="1f57c-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="1f57c-298">`EnsureCreated` 启用以下工作流来处理数据模型更改：</span><span class="sxs-lookup"><span data-stu-id="1f57c-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="1f57c-299">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-299">Delete the database.</span></span> <span data-ttu-id="1f57c-300">任何现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="1f57c-300">Any existing data is lost.</span></span>
* <span data-ttu-id="1f57c-301">更改数据模型。</span><span class="sxs-lookup"><span data-stu-id="1f57c-301">Change the data model.</span></span> <span data-ttu-id="1f57c-302">例如，添加 `EmailAddress` 字段。</span><span class="sxs-lookup"><span data-stu-id="1f57c-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="1f57c-303">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-303">Run the app.</span></span>
* <span data-ttu-id="1f57c-304">`EnsureCreated` 创建具有新架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="1f57c-305">在无需保存数据的情况下，当架构快速发展时，此工作流在早期开发过程中表现良好。</span><span class="sxs-lookup"><span data-stu-id="1f57c-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="1f57c-306">如果需要保存已输入数据库的数据，情况就有所不同了。</span><span class="sxs-lookup"><span data-stu-id="1f57c-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="1f57c-307">如果是这种情况，请使用迁移。</span><span class="sxs-lookup"><span data-stu-id="1f57c-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="1f57c-308">本系列教程的后续部分将删除 `EnsureCreated` 创建的数据库，转而使用迁移。</span><span class="sxs-lookup"><span data-stu-id="1f57c-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="1f57c-309">无法使用迁移更新 `EnsureCreated` 创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1f57c-310">测试应用</span><span class="sxs-lookup"><span data-stu-id="1f57c-310">Test the app</span></span>

* <span data-ttu-id="1f57c-311">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-311">Run the app.</span></span>
* <span data-ttu-id="1f57c-312">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1f57c-313">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="1f57c-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="1f57c-314">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="1f57c-314">Seed the database</span></span>

<span data-ttu-id="1f57c-315">`EnsureCreated` 方法将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="1f57c-316">本节添加用测试数据填充数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="1f57c-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="1f57c-317">使用以下代码创建 Data/DbInitializer.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="1f57c-318">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="1f57c-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="1f57c-319">如果不存在学生，它将向数据库添加测试数据。</span><span class="sxs-lookup"><span data-stu-id="1f57c-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="1f57c-320">该代码使用数组创建测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="1f57c-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="1f57c-321">在 Program.cs 中，将 `EnsureCreated` 调用替换为 `DbInitializer.Initialize` 调用：</span><span class="sxs-lookup"><span data-stu-id="1f57c-321">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-322">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f57c-323">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f57c-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="1f57c-324">使用 `Y` 进行响应，以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f57c-326">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="1f57c-327">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-327">Restart the app.</span></span>
* <span data-ttu-id="1f57c-328">选择“学生”页查看已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="1f57c-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="1f57c-329">查看数据库</span><span class="sxs-lookup"><span data-stu-id="1f57c-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f57c-331">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="1f57c-332">在 SSOX 中，依次选择“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="1f57c-333">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="1f57c-334">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="1f57c-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="1f57c-335">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="1f57c-336">右键单击 Student 表，然后单击“查看代码”查看 `Student` 模型如何映射到 `Student` 表架构 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f57c-338">使用 SQLite 工具查看数据库架构和已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="1f57c-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="1f57c-339">数据库文件名为 CU.db，位于项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="1f57c-340">异步代码</span><span class="sxs-lookup"><span data-stu-id="1f57c-340">Asynchronous code</span></span>

<span data-ttu-id="1f57c-341">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="1f57c-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1f57c-342">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1f57c-343">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="1f57c-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1f57c-344">使用同步代码时，可能会出现多个线程被占用但不能执行操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1f57c-345">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="1f57c-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1f57c-346">因此，使用异步代码可以更有效地利用服务器资源，并且服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="1f57c-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="1f57c-347">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="1f57c-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1f57c-348">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="1f57c-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1f57c-349">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="1f57c-350">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1f57c-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1f57c-351">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="1f57c-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1f57c-352">创建返回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 对象。</span><span class="sxs-lookup"><span data-stu-id="1f57c-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="1f57c-353">返回类型 `Task<T>` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-353">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="1f57c-354">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="1f57c-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1f57c-355">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1f57c-356">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="1f57c-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1f57c-357">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="1f57c-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1f57c-358">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="1f57c-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1f57c-359">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="1f57c-360">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1f57c-361">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1f57c-362">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1f57c-363">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="1f57c-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="1f57c-364">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="1f57c-365">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="1f57c-365">Performance considerations</span></span>

<span data-ttu-id="1f57c-366">通常，网页不应加载任何数量的行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="1f57c-367">查询应使用分页或限制方法。</span><span class="sxs-lookup"><span data-stu-id="1f57c-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="1f57c-368">例如，上述查询可以使用 `Take` 来限制返回的行数：</span><span class="sxs-lookup"><span data-stu-id="1f57c-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="1f57c-369">如果在枚举过程中出现异常数据库，则枚举视图中的大型表可能返回部分构造的 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="1f57c-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="1f57c-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> 默认值为 1024。</span><span class="sxs-lookup"><span data-stu-id="1f57c-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="1f57c-371">以下代码将设置 `MaxModelBindingCollectionSize`：</span><span class="sxs-lookup"><span data-stu-id="1f57c-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="1f57c-372">稍后将在教程中介绍分页。</span><span class="sxs-lookup"><span data-stu-id="1f57c-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1f57c-373">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1f57c-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1f57c-374">下一教程</span><span class="sxs-lookup"><span data-stu-id="1f57c-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="1f57c-375">本文是系列教程的第一篇，这些教程展示如何在 [ASP.NET Core Razor Pages](xref:razor-pages/index) 应用中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="1f57c-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="1f57c-376">这些教程为虚构的 Contoso University 生成一个网站。</span><span class="sxs-lookup"><span data-stu-id="1f57c-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1f57c-377">网站包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="1f57c-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1f57c-378">本教程使用代码优先方法。</span><span class="sxs-lookup"><span data-stu-id="1f57c-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="1f57c-379">有关使用数据库优先方法学习本教程的信息，请参阅[此 Github 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="1f57c-380">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1f57c-381">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f57c-382">先决条件</span><span class="sxs-lookup"><span data-stu-id="1f57c-382">Prerequisites</span></span>

* <span data-ttu-id="1f57c-383">如果不熟悉 Razor Pages，则在开始前，请浏览 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)系列教程。</span><span class="sxs-lookup"><span data-stu-id="1f57c-383">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-384">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="1f57c-386">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="1f57c-386">Database engines</span></span>

<span data-ttu-id="1f57c-387">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="1f57c-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="1f57c-388">Visual Studio Code 指令使用 [SQLite](https://www.sqlite.org/)，一种跨平台数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="1f57c-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="1f57c-389">如果选择使用 SQLite，请下载并安装[适用于 SQLite 的数据库浏览器](https://sqlitebrowser.org/)等第三方工具，用于管理和查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1f57c-390">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1f57c-390">Troubleshooting</span></span>

<span data-ttu-id="1f57c-391">如果遇到无法解决的问题，请将你的代码与[完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)进行比较。</span><span class="sxs-lookup"><span data-stu-id="1f57c-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1f57c-392">获取帮助的一个好方法是使用 [ASP.NET Core 标记](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 标记](https://stackoverflow.com/questions/tagged/entity-framework-core)将问题发布到 StackOverflow.com。</span><span class="sxs-lookup"><span data-stu-id="1f57c-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="1f57c-393">示例应用</span><span class="sxs-lookup"><span data-stu-id="1f57c-393">The sample app</span></span>

<span data-ttu-id="1f57c-394">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="1f57c-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="1f57c-395">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="1f57c-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1f57c-396">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="1f57c-396">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index30.png)

![学生编辑页](intro/_static/student-edit30.png)

<span data-ttu-id="1f57c-399">此网站的 UI 样式基于内置的项目模板。</span><span class="sxs-lookup"><span data-stu-id="1f57c-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="1f57c-400">本教程侧重于如何使用 EF Core，而不是如何自定义 UI。</span><span class="sxs-lookup"><span data-stu-id="1f57c-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="1f57c-401">单击页面顶部的链接，获取已完成项目的源代码。</span><span class="sxs-lookup"><span data-stu-id="1f57c-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="1f57c-402">“cu30”文件夹中有本教程的 ASP.NET Core 3.0 版本的代码。</span><span class="sxs-lookup"><span data-stu-id="1f57c-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="1f57c-403">在“cu30snapshots”文件夹中可以找到反映教程 1-7 代码状态的文件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f57c-405">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1f57c-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="1f57c-406">生成项目。</span><span class="sxs-lookup"><span data-stu-id="1f57c-406">Build the project.</span></span>
* <span data-ttu-id="1f57c-407">在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f57c-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="1f57c-408">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="1f57c-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f57c-410">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1f57c-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="1f57c-411">删除 ContosoUniversity.csproj，然后将 ContosoUniversitySQLite.csproj 重命名为 ContosoUniversity.csproj \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="1f57c-411">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="1f57c-412">在“Program.cs”中注释掉 `#define Startup`，以便使用 `StartupSQLite`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-412">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="1f57c-413">删除 appSettings.json，然后将 appSettingsSQLite.json 重命名为 appSettings.json \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="1f57c-413">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="1f57c-414">删除“Migrations”文件夹，然后将 MigrationsSQL 重命名为 Migrations  。</span><span class="sxs-lookup"><span data-stu-id="1f57c-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="1f57c-415">对 `#if SQLiteVersion` 执行全局搜索，并删除 `#if SQLiteVersion` 和相关 `#endif` 语句。</span><span class="sxs-lookup"><span data-stu-id="1f57c-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="1f57c-416">生成项目。</span><span class="sxs-lookup"><span data-stu-id="1f57c-416">Build the project.</span></span>
* <span data-ttu-id="1f57c-417">在项目文件夹中的命令提示符下运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f57c-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="1f57c-418">在 SQLite 工具中，运行以下 SQL 语句：</span><span class="sxs-lookup"><span data-stu-id="1f57c-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="1f57c-419">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="1f57c-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="1f57c-420">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="1f57c-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f57c-422">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1f57c-423">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="1f57c-424">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="1f57c-425">请务必使用此名称（含大写），确保在复制和粘贴代码时与命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="1f57c-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="1f57c-426">在下拉列表中选择“.NET Core”和“ASP.NET Core 3.0”，然后选择“Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="1f57c-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f57c-428">在终端中，导航到应在其中创建项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="1f57c-429">运行以下命令，在新的项目文件夹中创建 Razor Pages 项目和 `cd`：</span><span class="sxs-lookup"><span data-stu-id="1f57c-429">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1f57c-430">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="1f57c-430">Set up the site style</span></span>

<span data-ttu-id="1f57c-431">更新 Pages/Shared/_Layout.cshtml 以设置网站的页眉、页脚和菜单：</span><span class="sxs-lookup"><span data-stu-id="1f57c-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="1f57c-432">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="1f57c-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1f57c-433">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="1f57c-433">There are three occurrences.</span></span>

* <span data-ttu-id="1f57c-434">删除“主页”和“隐私”菜单项，然后添加“关于”、“学生”、“课程”、“讲师”和“院系”的菜单项      。</span><span class="sxs-lookup"><span data-stu-id="1f57c-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="1f57c-435">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="1f57c-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="1f57c-436">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET Core 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="1f57c-436">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="1f57c-437">运行应用以验证主页是否显示。</span><span class="sxs-lookup"><span data-stu-id="1f57c-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="1f57c-438">数据模型</span><span class="sxs-lookup"><span data-stu-id="1f57c-438">The data model</span></span>

<span data-ttu-id="1f57c-439">以下部分用于创建数据模型：</span><span class="sxs-lookup"><span data-stu-id="1f57c-439">The following sections create a data model:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="1f57c-441">一名学生可以修读任意数量的课程，并且某一课程可以有任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="1f57c-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="1f57c-442">Student 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-442">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

* <span data-ttu-id="1f57c-444">在项目文件夹中创建“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="1f57c-445">使用以下代码创建 Models/Student.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="1f57c-446">`ID` 属性成为此类对应的数据库表的主键列。</span><span class="sxs-lookup"><span data-stu-id="1f57c-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="1f57c-447">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1f57c-448">因此，`Student` 类主键的另一种自动识别的名称是 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="1f57c-449">有关详细信息，请参阅 [F Core - 密钥](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="1f57c-450">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1f57c-451">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="1f57c-452">在本例中，`Student` 实体的 `Enrollments` 属性包含与该 Student 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="1f57c-453">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="1f57c-454">在数据库中，如果 StudentID 列包含学生的 ID 值，则 Enrollment 行与 Student 行相关。</span><span class="sxs-lookup"><span data-stu-id="1f57c-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="1f57c-455">例如，假设某个 Student 行的 ID=1。</span><span class="sxs-lookup"><span data-stu-id="1f57c-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="1f57c-456">则相关 Enrollment 行的 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="1f57c-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="1f57c-457">StudentID 是 Enrollment 表中的外键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="1f57c-458">`Enrollments` 属性定义为 `ICollection<Enrollment>`，因为可能有多个相关的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="1f57c-459">可以使用 `List<Enrollment>` 或 `HashSet<Enrollment>` 等其他集合类型。</span><span class="sxs-lookup"><span data-stu-id="1f57c-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="1f57c-460">使用 `ICollection<Enrollment>` 时，EF Core 会默认创建 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="1f57c-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="1f57c-461">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-461">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="1f57c-463">使用以下代码创建 Models/Enrollment.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="1f57c-464">`EnrollmentID` 属性为主键；此实体使用 `classnameID` 模式而不是直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="1f57c-465">对于生产数据模型，请选择一个模式并一直使用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="1f57c-466">本教程两个都使用，只是为了说明这两个模式都能使用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="1f57c-467">使用不具有 `classname` 的 `ID` 可以更轻松地实现某些类型的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="1f57c-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="1f57c-468">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1f57c-469">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 [null](/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="1f57c-470">评级为 null 和评级为零是有区别的 &mdash; null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="1f57c-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1f57c-471">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1f57c-472">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="1f57c-473">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1f57c-474">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="1f57c-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1f57c-475">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1f57c-476">例如，`StudentID` 是 `Student` 导航属性的外键，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1f57c-477">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1f57c-478">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="1f57c-479">Course 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-479">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="1f57c-481">使用以下代码创建 Models/Course.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="1f57c-482">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="1f57c-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1f57c-483">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="1f57c-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1f57c-484">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="1f57c-485">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="1f57c-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="1f57c-486">搭建“学生”页的基架</span><span class="sxs-lookup"><span data-stu-id="1f57c-486">Scaffold Student pages</span></span>

<span data-ttu-id="1f57c-487">本部分使用 ASP.NET Core 基架工具生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="1f57c-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="1f57c-488">EF Core 上下文类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-488">An EF Core *context* class.</span></span> <span data-ttu-id="1f57c-489">上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="1f57c-490">它派生自 `Microsoft.EntityFrameworkCore.DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="1f57c-491">Razor 页面，可处理 `Student` 实体的创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-491">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-492">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f57c-493">在“Pages”文件夹中创建“Students”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="1f57c-494">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹，然后选择“添加”>“新搭建基架的项目” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-494">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1f57c-495">在“添加基架”对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-495">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="1f57c-496">在“添加使用实体框架的 Razor Pages (CRUD)”对话框中：</span><span class="sxs-lookup"><span data-stu-id="1f57c-496">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="1f57c-497">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="1f57c-498">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="1f57c-499">将数据上下文名称从 ContosoUniversity.Models.ContosoUniversityContext 更改为 ContosoUniversity.Data.SchoolContext 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="1f57c-500">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="1f57c-500">Select **Add**.</span></span>

<span data-ttu-id="1f57c-501">自动安装以下包：</span><span class="sxs-lookup"><span data-stu-id="1f57c-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f57c-503">运行以下 .NET Core CLI 命令，安装所需的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="1f57c-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="1f57c-504">基架需要 Microsoft.VisualStudio.Web.CodeGeneration.Design 包。</span><span class="sxs-lookup"><span data-stu-id="1f57c-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="1f57c-505">虽然应用不使用 SQL Server，但基架工具需要 SQL Server 包。</span><span class="sxs-lookup"><span data-stu-id="1f57c-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="1f57c-506">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="1f57c-507">运行以下命令安装 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="1f57c-508">运行以下命令，搭建“学生”页的基架。</span><span class="sxs-lookup"><span data-stu-id="1f57c-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="1f57c-509">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="1f57c-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="1f57c-510">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="1f57c-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="1f57c-511">如果对上述步骤有疑问，请生成项目并重试基架搭建步骤。</span><span class="sxs-lookup"><span data-stu-id="1f57c-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="1f57c-512">基架流程：</span><span class="sxs-lookup"><span data-stu-id="1f57c-512">The scaffolding process:</span></span>

* <span data-ttu-id="1f57c-513">在“Pages/Students”文件夹中创建 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="1f57c-513">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="1f57c-514">Create.cshtml 和 Create.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="1f57c-515">Delete.cshtml 和 Delete.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="1f57c-516">Details.cshtml 和 Details.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="1f57c-517">Edit.cshtml 和 Edit.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="1f57c-518">Index.cshtml 和 Index.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="1f57c-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="1f57c-519">创建 Data/SchoolContext.cs。</span><span class="sxs-lookup"><span data-stu-id="1f57c-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="1f57c-520">将上下文添加到 Startup.cs 中的依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="1f57c-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="1f57c-521">将数据库连接字符串添加到 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-521">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="1f57c-522">数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="1f57c-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-523">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f57c-524">*appsettings.json* 文件指定了连接字符串 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-524">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="1f57c-525">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1f57c-526">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 文件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f57c-528">更改连接字符串以指向名为 CU.db 的 SQLite 数据库文件：</span><span class="sxs-lookup"><span data-stu-id="1f57c-528">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="1f57c-529">更新数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="1f57c-529">Update the database context class</span></span>

<span data-ttu-id="1f57c-530">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="1f57c-531">上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1f57c-532">上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1f57c-533">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1f57c-534">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="1f57c-535">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="1f57c-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1f57c-536">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="1f57c-536">In EF Core terminology:</span></span>

* <span data-ttu-id="1f57c-537">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="1f57c-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="1f57c-538">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1f57c-539">由于实体集包含多个实体，因此 DBSet 属性应为复数名称。</span><span class="sxs-lookup"><span data-stu-id="1f57c-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="1f57c-540">由于基架工具创建了 `Student` DBSet，因此此步骤将其更改为复数 `Students`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="1f57c-541">为了使 Razor Pages 代码与新的 DBSet 名称相匹配，请在整个项目中进行全局更改，将 `_context.Student` 更改为 `_context.Students`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-541">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="1f57c-542">更改发生 8 次。</span><span class="sxs-lookup"><span data-stu-id="1f57c-542">There are 8 occurrences.</span></span>

<span data-ttu-id="1f57c-543">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="1f57c-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="1f57c-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1f57c-544">Startup.cs</span></span>

<span data-ttu-id="1f57c-545">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1f57c-546">在应用程序启动过程通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="1f57c-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1f57c-547">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="1f57c-547">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1f57c-548">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="1f57c-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1f57c-549">基架工具自动将上下文类注册到了依赖项注入容器。</span><span class="sxs-lookup"><span data-stu-id="1f57c-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-550">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f57c-551">在 `ConfigureServices` 中，基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="1f57c-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f57c-553">在 `ConfigureServices` 中，确保基架所添加的代码调用 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="1f57c-554">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="1f57c-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1f57c-555">进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1f57c-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="1f57c-556">创建数据库</span><span class="sxs-lookup"><span data-stu-id="1f57c-556">Create the database</span></span>

<span data-ttu-id="1f57c-557">如果没有数据库，请更新 Program.cs 以创建数据库：</span><span class="sxs-lookup"><span data-stu-id="1f57c-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="1f57c-558">如果有上下文的数据库，则 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="1f57c-559">如果没有数据库，则它将创建数据库和架构。</span><span class="sxs-lookup"><span data-stu-id="1f57c-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="1f57c-560">`EnsureCreated` 启用以下工作流来处理数据模型更改：</span><span class="sxs-lookup"><span data-stu-id="1f57c-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="1f57c-561">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-561">Delete the database.</span></span> <span data-ttu-id="1f57c-562">任何现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="1f57c-562">Any existing data is lost.</span></span>
* <span data-ttu-id="1f57c-563">更改数据模型。</span><span class="sxs-lookup"><span data-stu-id="1f57c-563">Change the data model.</span></span> <span data-ttu-id="1f57c-564">例如，添加 `EmailAddress` 字段。</span><span class="sxs-lookup"><span data-stu-id="1f57c-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="1f57c-565">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-565">Run the app.</span></span>
* <span data-ttu-id="1f57c-566">`EnsureCreated` 创建具有新架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="1f57c-567">在无需保存数据的情况下，当架构快速发展时，此工作流在早期开发过程中表现良好。</span><span class="sxs-lookup"><span data-stu-id="1f57c-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="1f57c-568">如果需要保存已输入数据库的数据，情况就有所不同了。</span><span class="sxs-lookup"><span data-stu-id="1f57c-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="1f57c-569">如果是这种情况，请使用迁移。</span><span class="sxs-lookup"><span data-stu-id="1f57c-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="1f57c-570">本系列教程的后续部分将删除 `EnsureCreated` 创建的数据库，转而使用迁移。</span><span class="sxs-lookup"><span data-stu-id="1f57c-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="1f57c-571">无法使用迁移更新 `EnsureCreated` 创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1f57c-572">测试应用</span><span class="sxs-lookup"><span data-stu-id="1f57c-572">Test the app</span></span>

* <span data-ttu-id="1f57c-573">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-573">Run the app.</span></span>
* <span data-ttu-id="1f57c-574">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1f57c-575">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="1f57c-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="1f57c-576">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="1f57c-576">Seed the database</span></span>

<span data-ttu-id="1f57c-577">`EnsureCreated` 方法将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="1f57c-578">本节添加用测试数据填充数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="1f57c-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="1f57c-579">使用以下代码创建 Data/DbInitializer.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="1f57c-580">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="1f57c-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="1f57c-581">如果不存在学生，它将向数据库添加测试数据。</span><span class="sxs-lookup"><span data-stu-id="1f57c-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="1f57c-582">该代码使用数组创建测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="1f57c-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="1f57c-583">在 Program.cs 中，将 `EnsureCreated` 调用替换为 `DbInitializer.Initialize` 调用：</span><span class="sxs-lookup"><span data-stu-id="1f57c-583">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-584">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f57c-585">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f57c-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f57c-587">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="1f57c-588">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-588">Restart the app.</span></span>

* <span data-ttu-id="1f57c-589">选择“学生”页查看已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="1f57c-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="1f57c-590">查看数据库</span><span class="sxs-lookup"><span data-stu-id="1f57c-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-591">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f57c-592">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="1f57c-593">在 SSOX 中，依次选择“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="1f57c-594">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="1f57c-595">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="1f57c-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="1f57c-596">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="1f57c-597">右键单击 Student 表，然后单击“查看代码”查看 `Student` 模型如何映射到 `Student` 表架构 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f57c-599">使用 SQLite 工具查看数据库架构和已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="1f57c-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="1f57c-600">数据库文件名为 CU.db，位于项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="1f57c-601">异步代码</span><span class="sxs-lookup"><span data-stu-id="1f57c-601">Asynchronous code</span></span>

<span data-ttu-id="1f57c-602">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="1f57c-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1f57c-603">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1f57c-604">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="1f57c-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1f57c-605">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1f57c-606">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="1f57c-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1f57c-607">因此，使用异步代码可以更有效地利用服务器资源，并且服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="1f57c-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="1f57c-608">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="1f57c-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1f57c-609">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="1f57c-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1f57c-610">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="1f57c-611">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1f57c-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1f57c-612">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="1f57c-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1f57c-613">创建返回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 对象。</span><span class="sxs-lookup"><span data-stu-id="1f57c-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="1f57c-614">返回类型 `Task<T>` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="1f57c-615">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="1f57c-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1f57c-616">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1f57c-617">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="1f57c-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1f57c-618">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="1f57c-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1f57c-619">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="1f57c-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1f57c-620">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="1f57c-621">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1f57c-622">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1f57c-623">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1f57c-624">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="1f57c-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="1f57c-625">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="1f57c-626">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1f57c-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1f57c-627">下一教程</span><span class="sxs-lookup"><span data-stu-id="1f57c-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f57c-628">Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 创建 ASP.NET Core Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="1f57c-629">该示例应用是一个虚构的 Contoso University 的网站。</span><span class="sxs-lookup"><span data-stu-id="1f57c-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1f57c-630">其中包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="1f57c-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1f57c-631">本页是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。</span><span class="sxs-lookup"><span data-stu-id="1f57c-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="1f57c-632">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1f57c-633">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f57c-634">先决条件</span><span class="sxs-lookup"><span data-stu-id="1f57c-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-635">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="1f57c-637">熟悉 [Razor Pages](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-637">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="1f57c-638">新程序员在开始学习本系列之前，应先完成 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-638">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1f57c-639">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1f57c-639">Troubleshooting</span></span>

<span data-ttu-id="1f57c-640">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="1f57c-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1f57c-641">获取帮助的一个好方法是将问题发布到适用于 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 的 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="1f57c-642">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="1f57c-642">The Contoso University web app</span></span>

<span data-ttu-id="1f57c-643">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="1f57c-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="1f57c-644">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="1f57c-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1f57c-645">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="1f57c-645">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

<span data-ttu-id="1f57c-648">此网站的 UI 样式与内置模板生成的 UI 样式类似。</span><span class="sxs-lookup"><span data-stu-id="1f57c-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="1f57c-649">教程的重点是 EF Core 和 Razor Pages，而非 UI。</span><span class="sxs-lookup"><span data-stu-id="1f57c-649">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="1f57c-650">创建 ContosoUniversity Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="1f57c-650">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-651">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f57c-652">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1f57c-653">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="1f57c-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="1f57c-654">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="1f57c-655">务必将该项目命名为 ContosoUniversity，以便复制/粘贴代码时命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="1f57c-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="1f57c-656">在下拉列表中选择“ASP.NET Core 2.1”，然后选择“Web 应用程序” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="1f57c-657">有关上述步骤的图像，请参阅[创建 Razor Web 应用](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-657">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="1f57c-658">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1f57c-660">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="1f57c-660">Set up the site style</span></span>

<span data-ttu-id="1f57c-661">设置网站菜单、布局和主页时需作少量更改。</span><span class="sxs-lookup"><span data-stu-id="1f57c-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="1f57c-662">进行以下更改以更新 Pages/Shared/_Layout.cshtml：</span><span class="sxs-lookup"><span data-stu-id="1f57c-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="1f57c-663">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="1f57c-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1f57c-664">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="1f57c-664">There are three occurrences.</span></span>

* <span data-ttu-id="1f57c-665">添加菜单项 **Students**，**Courses**，**Instructors**，和 **Department**，并删除 **Contact** 菜单项。</span><span class="sxs-lookup"><span data-stu-id="1f57c-665">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="1f57c-666">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="1f57c-666">The changes are highlighted.</span></span> <span data-ttu-id="1f57c-667">（没有显示全部标记。）</span><span class="sxs-lookup"><span data-stu-id="1f57c-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="1f57c-668">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET 和 MVC 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="1f57c-668">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="1f57c-669">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="1f57c-669">Create the data model</span></span>

<span data-ttu-id="1f57c-670">创建 Contoso University 应用的实体类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="1f57c-671">从以下三个实体开始：</span><span class="sxs-lookup"><span data-stu-id="1f57c-671">Start with the following three entities:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="1f57c-673">`Student` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="1f57c-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="1f57c-674">`Course` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="1f57c-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="1f57c-675">一名学生可以报名参加任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="1f57c-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="1f57c-676">一门课程中可以包含任意数量的学生。</span><span class="sxs-lookup"><span data-stu-id="1f57c-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="1f57c-677">以下部分将为这几个实体中的每一个实体创建一个类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="1f57c-678">Student 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-678">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="1f57c-680">创建 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-680">Create a *Models* folder.</span></span> <span data-ttu-id="1f57c-681">在 Models 文件夹中，使用以下代码创建一个名为 Student.cs 的类文件 ：</span><span class="sxs-lookup"><span data-stu-id="1f57c-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="1f57c-682">`ID` 属性成为此类对应的数据库 (DB) 表的主键列。</span><span class="sxs-lookup"><span data-stu-id="1f57c-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="1f57c-683">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1f57c-684">在 `classnameID` 中，`classname` 为类名称。</span><span class="sxs-lookup"><span data-stu-id="1f57c-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="1f57c-685">另一种自动识别的主键是上例中的 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="1f57c-686">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1f57c-687">导航属性链接到与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="1f57c-688">在这种情况下，`Student entity` 的 `Enrollments` 属性包含与该 `Student` 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="1f57c-689">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="1f57c-690">相关的 `Enrollment` 行是 `StudentID` 列中包含该学生的主键值的行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="1f57c-691">例如，假设 ID=1 的学生在 `Enrollment` 表中有两行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="1f57c-692">`Enrollment` 表中有两行的 `StudentID` = 1。</span><span class="sxs-lookup"><span data-stu-id="1f57c-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="1f57c-693">`StudentID` 是 `Enrollment` 表中的外键，用于指定 `Student` 表中的学生。</span><span class="sxs-lookup"><span data-stu-id="1f57c-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="1f57c-694">如果导航属性包含多个实体，则导航属性必须是列表类型，例如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="1f57c-695">可以指定 `ICollection<T>` 或诸如 `List<T>` 或 `HashSet<T>` 的类型。</span><span class="sxs-lookup"><span data-stu-id="1f57c-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="1f57c-696">使用 `ICollection<T>` 时，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="1f57c-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="1f57c-697">包含多个实体的导航属性来自于多对多和一对多关系。</span><span class="sxs-lookup"><span data-stu-id="1f57c-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="1f57c-698">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-698">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="1f57c-700">在 Models 文件夹中，使用以下代码创建 Enrollment.cs ：</span><span class="sxs-lookup"><span data-stu-id="1f57c-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="1f57c-701">`EnrollmentID` 属性为主键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="1f57c-702">`Student` 实体使用的是 `ID` 模式，而本实体使用的是 `classnameID` 模式。</span><span class="sxs-lookup"><span data-stu-id="1f57c-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="1f57c-703">通常情况下，开发者会选择一种模式并在整个数据模型中都使用该模式。</span><span class="sxs-lookup"><span data-stu-id="1f57c-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="1f57c-704">下一个教程将介绍如何使用不带类名的 ID，以便更轻松地在数据模型中实现集成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="1f57c-705">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1f57c-706">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="1f57c-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="1f57c-707">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="1f57c-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1f57c-708">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1f57c-709">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="1f57c-710">`Student` 实体与 `Student.Enrollments` 导航属性不同，后者包含多个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="1f57c-711">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1f57c-712">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="1f57c-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1f57c-713">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="1f57c-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1f57c-714">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1f57c-715">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1f57c-716">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="1f57c-717">Course 实体</span><span class="sxs-lookup"><span data-stu-id="1f57c-717">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="1f57c-719">在 Models 文件夹中，使用以下代码创建 Course.cs ：</span><span class="sxs-lookup"><span data-stu-id="1f57c-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="1f57c-720">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="1f57c-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1f57c-721">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="1f57c-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1f57c-722">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="1f57c-723">为“学生”模型搭建基架</span><span class="sxs-lookup"><span data-stu-id="1f57c-723">Scaffold the student model</span></span>

<span data-ttu-id="1f57c-724">本部分将为“学生”模型搭建基架。</span><span class="sxs-lookup"><span data-stu-id="1f57c-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="1f57c-725">确切地说，基架工具将生成页面，用于对“学生”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="1f57c-726">生成项目。</span><span class="sxs-lookup"><span data-stu-id="1f57c-726">Build the project.</span></span>
* <span data-ttu-id="1f57c-727">创建 Pages/Students 文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f57c-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-728">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f57c-729">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-729">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1f57c-730">在“添加基架”对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-730">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="1f57c-731">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="1f57c-731">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1f57c-732">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="1f57c-733">在“数据上下文类”行中，选择加号 (+) 并将生成的名称更改为 ContosoUniversity.Models.SchoolContext  。</span><span class="sxs-lookup"><span data-stu-id="1f57c-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="1f57c-734">在“数据上下文类”下拉列表中，选择“ContosoUniversity.Models.SchoolContext” </span><span class="sxs-lookup"><span data-stu-id="1f57c-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="1f57c-735">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="1f57c-735">Select **Add**.</span></span>

![CRUD 对话框](intro/_static/s1.png)

<span data-ttu-id="1f57c-737">如果对前面的步骤有疑问，请参阅[搭建“电影”模型的基架](xref:tutorials/razor-pages/model#scaffold-the-movie-model)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1f57c-739">运行以下命令，搭建“学生”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="1f57c-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="1f57c-740">搭建基架的过程会创建并更改以下文件：</span><span class="sxs-lookup"><span data-stu-id="1f57c-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="1f57c-741">创建的文件</span><span class="sxs-lookup"><span data-stu-id="1f57c-741">Files created</span></span>

* <span data-ttu-id="1f57c-742">Pages/Students：“创建”、“删除”、“详细信息”、“编辑”、“索引”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="1f57c-743">Data/SchoolContext.cs</span><span class="sxs-lookup"><span data-stu-id="1f57c-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="1f57c-744">更新的文件</span><span class="sxs-lookup"><span data-stu-id="1f57c-744">File updates</span></span>

* <span data-ttu-id="1f57c-745">*Startup.cs*：下一部分详细介绍对此文件所作的更改。</span><span class="sxs-lookup"><span data-stu-id="1f57c-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="1f57c-746">*appsettings.json* ：添加用于连接到本地数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1f57c-746">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1f57c-747">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="1f57c-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1f57c-748">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1f57c-749">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="1f57c-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1f57c-750">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="1f57c-750">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1f57c-751">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="1f57c-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1f57c-752">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="1f57c-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1f57c-753">在 Startup.cs 中检查 `ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="1f57c-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="1f57c-754">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="1f57c-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="1f57c-755">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="1f57c-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1f57c-756">进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1f57c-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="1f57c-757">更新 main</span><span class="sxs-lookup"><span data-stu-id="1f57c-757">Update main</span></span>

<span data-ttu-id="1f57c-758">在 Program.cs 中，修改 `Main` 方法以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1f57c-758">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="1f57c-759">从依赖关系注入容器获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="1f57c-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="1f57c-760">调用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="1f57c-761">`EnsureCreated` 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="1f57c-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="1f57c-762">下面的代码显示更新后的 Program.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="1f57c-763">`EnsureCreated` 确保存在上下文数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="1f57c-764">如果存在，则不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-764">If it exists, no action is taken.</span></span> <span data-ttu-id="1f57c-765">如果不存在，则会创建数据库及其所有架构。</span><span class="sxs-lookup"><span data-stu-id="1f57c-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="1f57c-766">`EnsureCreated` 不使用迁移创建数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="1f57c-767">使用 `EnsureCreated` 创建的数据库稍后无法使用迁移更新。</span><span class="sxs-lookup"><span data-stu-id="1f57c-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="1f57c-768">启动应用时会调用 `EnsureCreated`，以进行以下工作流：</span><span class="sxs-lookup"><span data-stu-id="1f57c-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="1f57c-769">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-769">Delete the DB.</span></span>
* <span data-ttu-id="1f57c-770">更改数据库架构（例如添加一个 `EmailAddress` 字段）。</span><span class="sxs-lookup"><span data-stu-id="1f57c-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="1f57c-771">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-771">Run the app.</span></span>
* <span data-ttu-id="1f57c-772">`EnsureCreated` 创建一个带有 `EmailAddress` 列的数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="1f57c-773">架构快速演变时，在开发初期使用 `EnsureCreated` 很方便。</span><span class="sxs-lookup"><span data-stu-id="1f57c-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="1f57c-774">本教程后面将删除 DB 并使用迁移。</span><span class="sxs-lookup"><span data-stu-id="1f57c-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1f57c-775">测试应用</span><span class="sxs-lookup"><span data-stu-id="1f57c-775">Test the app</span></span>

<span data-ttu-id="1f57c-776">运行应用并接受 cookie 策略。</span><span class="sxs-lookup"><span data-stu-id="1f57c-776">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="1f57c-777">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="1f57c-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="1f57c-778">有关 cookie 策略的信息，请参阅[欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-778">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="1f57c-779">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1f57c-780">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="1f57c-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="1f57c-781">检查 SchoolContext DB 上下文</span><span class="sxs-lookup"><span data-stu-id="1f57c-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="1f57c-782">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="1f57c-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="1f57c-783">数据上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1f57c-784">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1f57c-785">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1f57c-786">使用以下代码更新 SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="1f57c-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="1f57c-787">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="1f57c-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1f57c-788">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="1f57c-788">In EF Core terminology:</span></span>

* <span data-ttu-id="1f57c-789">实体集通常对应一个数据库表。</span><span class="sxs-lookup"><span data-stu-id="1f57c-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="1f57c-790">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1f57c-791">可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="1f57c-792">EF Core 隐式包含了它们，因为 `Student` 实体引用 `Enrollment` 实体，而 `Enrollment` 实体引用 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="1f57c-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="1f57c-793">在本教程中，将 `DbSet<Enrollment>` 和 `DbSet<Course>` 保留在 `SchoolContext` 中。</span><span class="sxs-lookup"><span data-stu-id="1f57c-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="1f57c-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="1f57c-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="1f57c-795">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="1f57c-796">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1f57c-797">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="1f57c-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="1f57c-798">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="1f57c-799">添加代码，以使用测试数据初始化该数据库</span><span class="sxs-lookup"><span data-stu-id="1f57c-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="1f57c-800">EF Core 会创建一个空的数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="1f57c-801">本部分中编写了 `Initialize` 方法来使用测试数据填充该数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="1f57c-802">在 Data 文件夹中，新建一个名为 DbInitializer.cs 的类文件，并添加以下代码 ：</span><span class="sxs-lookup"><span data-stu-id="1f57c-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="1f57c-803">注意：上面的代码对命名空间使用 `Models` (`namespace ContosoUniversity.Models`)，而不是 `Data`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="1f57c-804">`Models` 与基架生成的代码一致。</span><span class="sxs-lookup"><span data-stu-id="1f57c-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="1f57c-805">有关详细信息，请参阅[此 GitHub 基架问题](https://github.com/aspnet/Scaffolding/issues/822)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="1f57c-806">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="1f57c-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="1f57c-807">如果 DB 中没有任何学生，则会使用测试数据初始化该 DB。</span><span class="sxs-lookup"><span data-stu-id="1f57c-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="1f57c-808">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="1f57c-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="1f57c-809">`EnsureCreated` 方法自动为数据库上下文创建数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="1f57c-810">如果数据库已存在，则返回 `EnsureCreated`，并且不修改数据库。</span><span class="sxs-lookup"><span data-stu-id="1f57c-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="1f57c-811">在 Program.cs 中，将 `Main` 方法修改为调用 `Initialize`：</span><span class="sxs-lookup"><span data-stu-id="1f57c-811">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="1f57c-812">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f57c-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1f57c-813">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f57c-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1f57c-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f57c-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f57c-815">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="1f57c-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="1f57c-816">查看数据库</span><span class="sxs-lookup"><span data-stu-id="1f57c-816">View the DB</span></span>

<span data-ttu-id="1f57c-817">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="1f57c-818">因此，数据库名称为“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="1f57c-819">GUID 因用户而异。</span><span class="sxs-lookup"><span data-stu-id="1f57c-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="1f57c-820">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="1f57c-821">在 SSOX 中，依次单击“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="1f57c-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="1f57c-822">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="1f57c-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="1f57c-823">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="1f57c-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="1f57c-824">异步代码</span><span class="sxs-lookup"><span data-stu-id="1f57c-824">Asynchronous code</span></span>

<span data-ttu-id="1f57c-825">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="1f57c-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1f57c-826">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="1f57c-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1f57c-827">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="1f57c-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1f57c-828">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="1f57c-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1f57c-829">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="1f57c-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1f57c-830">因此，使用异步代码可以更有效地利用服务器资源，并且可以让服务器在没有延迟的情况下处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="1f57c-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="1f57c-831">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="1f57c-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1f57c-832">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="1f57c-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1f57c-833">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="1f57c-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="1f57c-834">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1f57c-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1f57c-835">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="1f57c-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1f57c-836">自动创建返回的 [Task](/dotnet/api/system.threading.tasks.task) 对象。</span><span class="sxs-lookup"><span data-stu-id="1f57c-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="1f57c-837">有关详细信息，请参阅[任务返回类型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="1f57c-838">隐式返回类型 `Task` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="1f57c-839">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="1f57c-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1f57c-840">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1f57c-841">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="1f57c-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1f57c-842">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="1f57c-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1f57c-843">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="1f57c-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1f57c-844">只会异步执行导致查询或命令被发送到数据库的语句。</span><span class="sxs-lookup"><span data-stu-id="1f57c-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="1f57c-845">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1f57c-846">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="1f57c-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1f57c-847">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1f57c-848">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="1f57c-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="1f57c-849">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="1f57c-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="1f57c-850">下一个教程将介绍基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="1f57c-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="1f57c-851">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f57c-851">Additional resources</span></span>

* [<span data-ttu-id="1f57c-852">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="1f57c-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="1f57c-853">下一页</span><span class="sxs-lookup"><span data-stu-id="1f57c-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
