---
title: ASP.NET Core 中的 Razor Pages 和 Entity Framework Core - 第 1 个教程（共 8 个）
author: rick-anderson
description: 介绍如何使用 Entity Framework Core 创建 Razor Pages 应用
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
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
ms.openlocfilehash: 35a5758500ae2bc691c8d08eccb22340f9998c39
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424268"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="6b9bc-103">ASP.NET Core 中的 Razor Pages 和 Entity Framework Core - 第 1 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="6b9bc-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="6b9bc-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6b9bc-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6b9bc-105">本文是系列教程的第一篇，这些教程展示如何在 [ASP.NET Core Razor Pages](xref:razor-pages/index) 应用中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="6b9bc-106">这些教程为虚构的 Contoso University 生成一个网站。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="6b9bc-107">网站包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="6b9bc-108">本教程使用代码优先方法。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="6b9bc-109">有关使用数据库优先方法学习本教程的信息，请参阅[此 Github 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="6b9bc-110">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="6b9bc-111">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b9bc-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="6b9bc-112">Prerequisites</span></span>

* <span data-ttu-id="6b9bc-113">如果不熟悉 Razor Pages，则在开始前，请浏览 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)系列教程。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="6b9bc-116">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="6b9bc-116">Database engines</span></span>

<span data-ttu-id="6b9bc-117">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="6b9bc-118">Visual Studio Code 指令使用 [SQLite](https://www.sqlite.org/)，一种跨平台数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="6b9bc-119">如果选择使用 SQLite，请下载并安装[适用于 SQLite 的数据库浏览器](https://sqlitebrowser.org/)等第三方工具，用于管理和查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6b9bc-120">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6b9bc-120">Troubleshooting</span></span>

<span data-ttu-id="6b9bc-121">如果遇到无法解决的问题，请将你的代码与[完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)进行比较。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="6b9bc-122">获取帮助的一个好方法是使用 [ASP.NET Core 标记](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 标记](https://stackoverflow.com/questions/tagged/entity-framework-core)将问题发布到 StackOverflow.com。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="6b9bc-123">示例应用</span><span class="sxs-lookup"><span data-stu-id="6b9bc-123">The sample app</span></span>

<span data-ttu-id="6b9bc-124">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="6b9bc-125">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="6b9bc-126">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-126">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index30.png)

![学生编辑页](intro/_static/student-edit30.png)

<span data-ttu-id="6b9bc-129">此网站的 UI 样式基于内置的项目模板。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="6b9bc-130">本教程侧重于如何将 EF Core 和 ASP.NET Core 结合在一起使用，而不是如何自定义 UI。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="6b9bc-131">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="6b9bc-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-133">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-133">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6b9bc-134">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-134">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="6b9bc-135">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="6b9bc-136">请务必使用此名称（含大写），确保在复制和粘贴代码时与命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="6b9bc-137">在下拉列表中选择“.NET Core”和“ASP.NET Core 5.0”，然后选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b9bc-139">在终端中，导航到应在其中创建项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="6b9bc-140">运行以下命令，在新的项目文件夹中创建 Razor Pages 项目和 `cd`：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-140">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="6b9bc-141">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="6b9bc-141">Set up the site style</span></span>

<span data-ttu-id="6b9bc-142">将下面的代码复制并粘贴到 Pages/Shared/_Layout.cshtml 文件中：[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="6b9bc-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="6b9bc-143">布局文件会设置网站页眉、页脚和菜单。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="6b9bc-144">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="6b9bc-145">将文件中的“ContosoUniversity”更改为“Contoso University”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="6b9bc-146">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-146">There are three occurrences.</span></span>
* <span data-ttu-id="6b9bc-147">删除“主页”和“隐私”菜单项。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="6b9bc-148">添加“关于”、“学生”、“课程”、“讲师”和“部门”项。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-148">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="6b9bc-149">在 Pages/Index.cshtml 中，将该文件的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-149">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="6b9bc-150">前面的代码会将关于 ASP.NET Core 的文本替换为关于此应用的文本。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="6b9bc-151">运行应用以验证主页是否显示。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="6b9bc-152">数据模型</span><span class="sxs-lookup"><span data-stu-id="6b9bc-152">The data model</span></span>

<span data-ttu-id="6b9bc-153">以下部分用于创建数据模型：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-153">The following sections create a data model:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="6b9bc-155">一名学生可以修读任意数量的课程，并且某一课程可以有任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="6b9bc-156">Student 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-156">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

* <span data-ttu-id="6b9bc-158">在项目文件夹中创建“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="6b9bc-159">使用以下代码创建 Models/Student.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="6b9bc-160">`ID` 属性成为此类对应的数据库表的主键列。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="6b9bc-161">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="6b9bc-162">因此，`Student` 类主键的另一种自动识别的名称是 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="6b9bc-163">有关详细信息，请参阅 [F Core - 密钥](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="6b9bc-164">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="6b9bc-165">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="6b9bc-166">在本例中，`Student` 实体的 `Enrollments` 属性包含与该 Student 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="6b9bc-167">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="6b9bc-168">在数据库中，如果 StudentID 列包含学生的 ID 值，则 Enrollment 行与 Student 行相关。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="6b9bc-169">例如，假设某个 Student 行的 ID=1。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="6b9bc-170">则相关 Enrollment 行的 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="6b9bc-171">StudentID 是 Enrollment 表中的外键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="6b9bc-172">`Enrollments` 属性定义为 `ICollection<Enrollment>`，因为可能有多个相关的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="6b9bc-173">可以使用 `List<Enrollment>` 或 `HashSet<Enrollment>` 等其他集合类型。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="6b9bc-174">使用 `ICollection<Enrollment>` 时，EF Core 会默认创建 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="6b9bc-175">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-175">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="6b9bc-177">使用以下代码创建 Models/Enrollment.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="6b9bc-178">`EnrollmentID` 属性为主键；此实体使用 `classnameID` 模式而不是直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="6b9bc-179">对于生产数据模型，请选择一个模式并一直使用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="6b9bc-180">本教程两个都使用，只是为了说明这两个模式都能使用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="6b9bc-181">使用不具有 `classname` 的 `ID` 可以更轻松地实现某些类型的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="6b9bc-182">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="6b9bc-183">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 [null](/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="6b9bc-184">评级为 null 和评级为零是有区别的 &mdash; null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="6b9bc-185">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="6b9bc-186">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="6b9bc-187">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="6b9bc-188">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="6b9bc-189">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="6b9bc-190">例如，`StudentID` 是 `Student` 导航属性的外键，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="6b9bc-191">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="6b9bc-192">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="6b9bc-193">Course 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-193">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="6b9bc-195">使用以下代码创建 Models/Course.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="6b9bc-196">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="6b9bc-197">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="6b9bc-198">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="6b9bc-199">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="6b9bc-200">搭建“学生”页的基架</span><span class="sxs-lookup"><span data-stu-id="6b9bc-200">Scaffold Student pages</span></span>

<span data-ttu-id="6b9bc-201">本部分使用 ASP.NET Core 基架工具生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="6b9bc-202">EF Core `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="6b9bc-203">上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="6b9bc-204">它派生自 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="6b9bc-205">Razor 页面，可处理 `Student` 实体的创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-205">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-207">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="6b9bc-208">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹，然后选择“添加”>“新搭建基架的项目” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-208">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="6b9bc-209">在“添加新基架项”对话框中：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="6b9bc-210">在左侧选项卡中，依次选择“已安装 > 通用 > Razor 页面”</span><span class="sxs-lookup"><span data-stu-id="6b9bc-210">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="6b9bc-211">依次选择“使用实体框架的 Razor 页面(CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-211">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="6b9bc-212">在“添加使用实体框架的 Razor Pages (CRUD)”对话框中：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-212">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="6b9bc-213">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="6b9bc-214">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="6b9bc-215">将数据上下文名称更改为以 `SchoolContext` 结尾，而不以 `ContosoUniversityContext` 结尾。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="6b9bc-216">更新后的上下文名称为：`ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="6b9bc-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="6b9bc-217">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-217">Select **Add**.</span></span>

<span data-ttu-id="6b9bc-218">自动安装以下包：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b9bc-220">运行以下 .NET Core CLI 命令，安装所需的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="6b9bc-221">基架需要 Microsoft.VisualStudio.Web.CodeGeneration.Design 包。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="6b9bc-222">虽然应用不使用 SQL Server，但基架工具需要 SQL Server 包。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="6b9bc-223">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="6b9bc-224">运行以下命令安装 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="6b9bc-225">运行以下命令，搭建“学生”页的基架。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="6b9bc-226">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="6b9bc-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="6b9bc-227">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="6b9bc-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="6b9bc-228">如果上述步骤失败，请生成项目并重试基架搭建步骤。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="6b9bc-229">基架流程：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-229">The scaffolding process:</span></span>

* <span data-ttu-id="6b9bc-230">在“Pages/Students”文件夹中创建 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-230">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="6b9bc-231">Create.cshtml 和 Create.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="6b9bc-232">Delete.cshtml 和 Delete.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="6b9bc-233">Details.cshtml 和 Details.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="6b9bc-234">Edit.cshtml 和 Edit.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="6b9bc-235">Index.cshtml 和 Index.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="6b9bc-236">创建 Data/SchoolContext.cs。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-236">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="6b9bc-237">将上下文添加到 Startup.cs 中的依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-237">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="6b9bc-238">将数据库连接字符串添加到 appsettings.json。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-238">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="6b9bc-239">数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="6b9bc-239">Database connection string</span></span>

<span data-ttu-id="6b9bc-240">基架工具会在 appsettings.json 文件中生成连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-240">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b9bc-242">此连接字符串指定了 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="6b9bc-243">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="6b9bc-244">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 文件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b9bc-246">将 SQLite 连接字符串缩写为 CU.db：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-246">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="6b9bc-247">更新数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="6b9bc-247">Update the database context class</span></span>

<span data-ttu-id="6b9bc-248">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="6b9bc-249">上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6b9bc-250">上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="6b9bc-251">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="6b9bc-252">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="6b9bc-253">上述代码会将单数形式的 `DbSet<Student> Student` 更改为复数形式的 `DbSet<Student> Students`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="6b9bc-254">若要使 Razor 页面代码与新的 `DBSet` 名称匹配，请进行以下全局更改：`_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="6b9bc-254">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="6b9bc-255">更改为 `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="6b9bc-255">to: `_context.Students.`</span></span>

<span data-ttu-id="6b9bc-256">更改发生 8 次。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-256">There are 8 occurrences.</span></span>

<span data-ttu-id="6b9bc-257">由于一个实体集包含多个实体，因此许多开发人员更倾向于使用复数形式的 `DBSet` 属性名称。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="6b9bc-258">突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-258">The highlighted code:</span></span>

* <span data-ttu-id="6b9bc-259">为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="6b9bc-260">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="6b9bc-261">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="6b9bc-262">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="6b9bc-263">调用 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="6b9bc-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="6b9bc-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="6b9bc-265">在完成对 `SchoolContext` 的初始化后，并在模型已锁定并用于初始化上下文之前，进行调用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="6b9bc-266">是必需的，因为在本教程的后续部分中，`Student` 实体将引用其他实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="6b9bc-267">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="6b9bc-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="6b9bc-268">Startup.cs</span></span>

<span data-ttu-id="6b9bc-269">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6b9bc-270">服务（例如 `SchoolContext`）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="6b9bc-271">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-271">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="6b9bc-272">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6b9bc-273">基架工具自动将上下文类注册到了依赖项注入容器。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b9bc-275">基架添加了下列突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b9bc-277">验证基架所添加的代码是否调用 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="6b9bc-278">有关使用生产数据库的信息，请参阅[将 SQLite 用于开发，将 SQL Server 用于生产](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="6b9bc-279">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6b9bc-280">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="6b9bc-281">添加数据库异常筛选器</span><span class="sxs-lookup"><span data-stu-id="6b9bc-281">Add the database exception filter</span></span>

<span data-ttu-id="6b9bc-282">将 `AddDatabaseDeveloperPageExceptionFilter` 添加到 `ConfigureServices`，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="6b9bc-284">添加 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="6b9bc-285">在 PMC 中，输入以下命令来添加此 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="6b9bc-287">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 包提供 Entity Framework Core 错误页的 ASP.NET Core 中间件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="6b9bc-288">此中间件有助于检测和诊断 Entity Framework Core 迁移错误。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="6b9bc-289">创建数据库</span><span class="sxs-lookup"><span data-stu-id="6b9bc-289">Create the database</span></span>

<span data-ttu-id="6b9bc-290">如果没有数据库，请更新 Program.cs 以创建数据库：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="6b9bc-291">如果有上下文的数据库，则 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="6b9bc-292">如果没有数据库，则它将创建数据库和架构。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="6b9bc-293">`EnsureCreated` 启用以下工作流来处理数据模型更改：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="6b9bc-294">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-294">Delete the database.</span></span> <span data-ttu-id="6b9bc-295">任何现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-295">Any existing data is lost.</span></span>
* <span data-ttu-id="6b9bc-296">更改数据模型。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-296">Change the data model.</span></span> <span data-ttu-id="6b9bc-297">例如，添加 `EmailAddress` 字段。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="6b9bc-298">运行应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-298">Run the app.</span></span>
* <span data-ttu-id="6b9bc-299">`EnsureCreated` 创建具有新架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="6b9bc-300">在无需保存数据的情况下，当架构快速发展时，此工作流在早期开发过程中表现良好。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="6b9bc-301">如果需要保存已输入数据库的数据，情况就有所不同了。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="6b9bc-302">如果是这种情况，请使用迁移。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="6b9bc-303">本系列教程的后续部分将删除 `EnsureCreated` 创建的数据库，转而使用迁移。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="6b9bc-304">无法使用迁移更新 `EnsureCreated` 创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="6b9bc-305">测试应用</span><span class="sxs-lookup"><span data-stu-id="6b9bc-305">Test the app</span></span>

* <span data-ttu-id="6b9bc-306">运行应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-306">Run the app.</span></span>
* <span data-ttu-id="6b9bc-307">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-307">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="6b9bc-308">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="6b9bc-309">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="6b9bc-309">Seed the database</span></span>

<span data-ttu-id="6b9bc-310">`EnsureCreated` 方法将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="6b9bc-311">本节添加用测试数据填充数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="6b9bc-312">使用以下代码创建 Data/DbInitializer.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="6b9bc-313">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="6b9bc-314">如果不存在学生，它将向数据库添加测试数据。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="6b9bc-315">该代码使用数组创建测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="6b9bc-316">在 Program.cs 中，将 `EnsureCreated` 调用替换为 `DbInitializer.Initialize` 调用：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-316">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b9bc-318">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="6b9bc-319">使用 `Y` 进行响应，以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b9bc-321">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="6b9bc-322">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-322">Restart the app.</span></span>
* <span data-ttu-id="6b9bc-323">选择“学生”页查看已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="6b9bc-324">查看数据库</span><span class="sxs-lookup"><span data-stu-id="6b9bc-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-326">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="6b9bc-327">在 SSOX 中，依次选择“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="6b9bc-328">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="6b9bc-329">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="6b9bc-330">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="6b9bc-331">右键单击 Student 表，然后单击“查看代码”查看 `Student` 模型如何映射到 `Student` 表架构 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b9bc-333">使用 SQLite 工具查看数据库架构和已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="6b9bc-334">数据库文件名为 CU.db，位于项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="6b9bc-335">异步代码</span><span class="sxs-lookup"><span data-stu-id="6b9bc-335">Asynchronous code</span></span>

<span data-ttu-id="6b9bc-336">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="6b9bc-337">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="6b9bc-338">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="6b9bc-339">使用同步代码时，可能会出现多个线程被占用但不能执行操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="6b9bc-340">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="6b9bc-341">因此，使用异步代码可以更有效地利用服务器资源，并且服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="6b9bc-342">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="6b9bc-343">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="6b9bc-344">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="6b9bc-345">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="6b9bc-346">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="6b9bc-347">创建返回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 对象。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="6b9bc-348">返回类型 `Task<T>` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="6b9bc-349">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="6b9bc-350">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="6b9bc-351">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="6b9bc-352">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="6b9bc-353">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="6b9bc-354">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="6b9bc-355">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="6b9bc-356">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="6b9bc-357">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="6b9bc-358">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="6b9bc-359">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="6b9bc-360">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="6b9bc-360">Performance considerations</span></span>

<span data-ttu-id="6b9bc-361">通常，网页不应加载任何数量的行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="6b9bc-362">查询应使用分页或限制方法。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="6b9bc-363">例如，上述查询可以使用 `Take` 来限制返回的行数：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="6b9bc-364">如果在枚举过程中出现异常数据库，则枚举视图中的大型表可能返回部分构造的 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="6b9bc-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> 默认值为 1024。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="6b9bc-366">以下代码将设置 `MaxModelBindingCollectionSize`：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="6b9bc-367">稍后将在教程中介绍分页。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6b9bc-368">后续步骤</span><span class="sxs-lookup"><span data-stu-id="6b9bc-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6b9bc-369">下一教程</span><span class="sxs-lookup"><span data-stu-id="6b9bc-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="6b9bc-370">本文是系列教程的第一篇，这些教程展示如何在 [ASP.NET Core Razor Pages](xref:razor-pages/index) 应用中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="6b9bc-371">这些教程为虚构的 Contoso University 生成一个网站。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="6b9bc-372">网站包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="6b9bc-373">本教程使用代码优先方法。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="6b9bc-374">有关使用数据库优先方法学习本教程的信息，请参阅[此 Github 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="6b9bc-375">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="6b9bc-376">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b9bc-377">先决条件</span><span class="sxs-lookup"><span data-stu-id="6b9bc-377">Prerequisites</span></span>

* <span data-ttu-id="6b9bc-378">如果不熟悉 Razor Pages，则在开始前，请浏览 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)系列教程。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-378">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="6b9bc-381">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="6b9bc-381">Database engines</span></span>

<span data-ttu-id="6b9bc-382">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="6b9bc-383">Visual Studio Code 指令使用 [SQLite](https://www.sqlite.org/)，一种跨平台数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="6b9bc-384">如果选择使用 SQLite，请下载并安装[适用于 SQLite 的数据库浏览器](https://sqlitebrowser.org/)等第三方工具，用于管理和查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6b9bc-385">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6b9bc-385">Troubleshooting</span></span>

<span data-ttu-id="6b9bc-386">如果遇到无法解决的问题，请将你的代码与[完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)进行比较。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="6b9bc-387">获取帮助的一个好方法是使用 [ASP.NET Core 标记](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 标记](https://stackoverflow.com/questions/tagged/entity-framework-core)将问题发布到 StackOverflow.com。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="6b9bc-388">示例应用</span><span class="sxs-lookup"><span data-stu-id="6b9bc-388">The sample app</span></span>

<span data-ttu-id="6b9bc-389">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="6b9bc-390">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="6b9bc-391">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-391">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index30.png)

![学生编辑页](intro/_static/student-edit30.png)

<span data-ttu-id="6b9bc-394">此网站的 UI 样式基于内置的项目模板。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="6b9bc-395">本教程侧重于如何使用 EF Core，而不是如何自定义 UI。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="6b9bc-396">单击页面顶部的链接，获取已完成项目的源代码。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="6b9bc-397">“cu30”文件夹中有本教程的 ASP.NET Core 3.0 版本的代码。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="6b9bc-398">在“cu30snapshots”文件夹中可以找到反映教程 1-7 代码状态的文件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b9bc-400">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="6b9bc-401">生成项目。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-401">Build the project.</span></span>
* <span data-ttu-id="6b9bc-402">在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="6b9bc-403">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b9bc-405">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="6b9bc-406">删除 ContosoUniversity.csproj，然后将 ContosoUniversitySQLite.csproj 重命名为 ContosoUniversity.csproj\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-406">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="6b9bc-407">在“Program.cs”中注释掉 `#define Startup`，以便使用 `StartupSQLite`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-407">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="6b9bc-408">删除 appSettings.json，然后将 appSettingsSQLite.json 重命名为 appSettings.json\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-408">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="6b9bc-409">删除“Migrations”文件夹，然后将 MigrationsSQL 重命名为 Migrations  。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="6b9bc-410">对 `#if SQLiteVersion` 执行全局搜索，并删除 `#if SQLiteVersion` 和相关 `#endif` 语句。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="6b9bc-411">生成项目。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-411">Build the project.</span></span>
* <span data-ttu-id="6b9bc-412">在项目文件夹中的命令提示符下运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="6b9bc-413">在 SQLite 工具中，运行以下 SQL 语句：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="6b9bc-414">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="6b9bc-415">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="6b9bc-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-417">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-417">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6b9bc-418">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-418">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="6b9bc-419">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-419">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="6b9bc-420">请务必使用此名称（含大写），确保在复制和粘贴代码时与命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="6b9bc-421">在下拉列表中选择“.NET Core”和“ASP.NET Core 3.0”，然后选择“Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b9bc-423">在终端中，导航到应在其中创建项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="6b9bc-424">运行以下命令，在新的项目文件夹中创建 Razor Pages 项目和 `cd`：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-424">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="6b9bc-425">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="6b9bc-425">Set up the site style</span></span>

<span data-ttu-id="6b9bc-426">更新 Pages/Shared/_Layout.cshtml 以设置网站的页眉、页脚和菜单：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="6b9bc-427">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="6b9bc-428">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-428">There are three occurrences.</span></span>

* <span data-ttu-id="6b9bc-429">删除“主页”和“隐私”菜单项，然后添加“关于”、“学生”、“课程”、“讲师”和“院系”的菜单项      。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="6b9bc-430">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="6b9bc-431">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET Core 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-431">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="6b9bc-432">运行应用以验证主页是否显示。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="6b9bc-433">数据模型</span><span class="sxs-lookup"><span data-stu-id="6b9bc-433">The data model</span></span>

<span data-ttu-id="6b9bc-434">以下部分用于创建数据模型：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-434">The following sections create a data model:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="6b9bc-436">一名学生可以修读任意数量的课程，并且某一课程可以有任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="6b9bc-437">Student 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-437">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

* <span data-ttu-id="6b9bc-439">在项目文件夹中创建“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="6b9bc-440">使用以下代码创建 Models/Student.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="6b9bc-441">`ID` 属性成为此类对应的数据库表的主键列。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="6b9bc-442">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="6b9bc-443">因此，`Student` 类主键的另一种自动识别的名称是 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="6b9bc-444">有关详细信息，请参阅 [F Core - 密钥](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="6b9bc-445">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="6b9bc-446">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="6b9bc-447">在本例中，`Student` 实体的 `Enrollments` 属性包含与该 Student 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="6b9bc-448">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="6b9bc-449">在数据库中，如果 StudentID 列包含学生的 ID 值，则 Enrollment 行与 Student 行相关。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="6b9bc-450">例如，假设某个 Student 行的 ID=1。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="6b9bc-451">则相关 Enrollment 行的 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="6b9bc-452">StudentID 是 Enrollment 表中的外键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="6b9bc-453">`Enrollments` 属性定义为 `ICollection<Enrollment>`，因为可能有多个相关的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="6b9bc-454">可以使用 `List<Enrollment>` 或 `HashSet<Enrollment>` 等其他集合类型。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="6b9bc-455">使用 `ICollection<Enrollment>` 时，EF Core 会默认创建 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="6b9bc-456">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-456">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="6b9bc-458">使用以下代码创建 Models/Enrollment.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="6b9bc-459">`EnrollmentID` 属性为主键；此实体使用 `classnameID` 模式而不是直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="6b9bc-460">对于生产数据模型，请选择一个模式并一直使用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="6b9bc-461">本教程两个都使用，只是为了说明这两个模式都能使用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="6b9bc-462">使用不具有 `classname` 的 `ID` 可以更轻松地实现某些类型的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="6b9bc-463">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="6b9bc-464">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 [null](/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="6b9bc-465">评级为 null 和评级为零是有区别的 &mdash; null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="6b9bc-466">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="6b9bc-467">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="6b9bc-468">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="6b9bc-469">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="6b9bc-470">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="6b9bc-471">例如，`StudentID` 是 `Student` 导航属性的外键，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="6b9bc-472">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="6b9bc-473">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="6b9bc-474">Course 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-474">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="6b9bc-476">使用以下代码创建 Models/Course.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="6b9bc-477">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="6b9bc-478">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="6b9bc-479">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="6b9bc-480">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="6b9bc-481">搭建“学生”页的基架</span><span class="sxs-lookup"><span data-stu-id="6b9bc-481">Scaffold Student pages</span></span>

<span data-ttu-id="6b9bc-482">本部分使用 ASP.NET Core 基架工具生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="6b9bc-483">EF Core 上下文类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-483">An EF Core *context* class.</span></span> <span data-ttu-id="6b9bc-484">上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="6b9bc-485">它派生自 `Microsoft.EntityFrameworkCore.DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="6b9bc-486">Razor 页面，可处理 `Student` 实体的创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-486">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-488">在“Pages”文件夹中创建“Students”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="6b9bc-489">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹，然后选择“添加”>“新搭建基架的项目” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-489">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="6b9bc-490">在“添加基架”对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-490">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="6b9bc-491">在“添加使用实体框架的 Razor Pages (CRUD)”对话框中：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-491">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="6b9bc-492">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="6b9bc-493">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="6b9bc-494">将数据上下文名称从 ContosoUniversity.Models.ContosoUniversityContext 更改为 ContosoUniversity.Data.SchoolContext 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="6b9bc-495">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-495">Select **Add**.</span></span>

<span data-ttu-id="6b9bc-496">自动安装以下包：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b9bc-498">运行以下 .NET Core CLI 命令，安装所需的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="6b9bc-499">基架需要 Microsoft.VisualStudio.Web.CodeGeneration.Design 包。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="6b9bc-500">虽然应用不使用 SQL Server，但基架工具需要 SQL Server 包。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="6b9bc-501">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="6b9bc-502">运行以下命令安装 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="6b9bc-503">运行以下命令，搭建“学生”页的基架。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="6b9bc-504">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="6b9bc-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="6b9bc-505">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="6b9bc-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="6b9bc-506">如果对上述步骤有疑问，请生成项目并重试基架搭建步骤。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="6b9bc-507">基架流程：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-507">The scaffolding process:</span></span>

* <span data-ttu-id="6b9bc-508">在“Pages/Students”文件夹中创建 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-508">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="6b9bc-509">Create.cshtml 和 Create.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="6b9bc-510">Delete.cshtml 和 Delete.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="6b9bc-511">Details.cshtml 和 Details.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="6b9bc-512">Edit.cshtml 和 Edit.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="6b9bc-513">Index.cshtml 和 Index.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="6b9bc-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="6b9bc-514">创建 Data/SchoolContext.cs。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-514">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="6b9bc-515">将上下文添加到 Startup.cs 中的依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-515">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="6b9bc-516">将数据库连接字符串添加到 appsettings.json。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-516">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="6b9bc-517">数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="6b9bc-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b9bc-519">appsettings.json 文件指定了连接字符串 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-519">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="6b9bc-520">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="6b9bc-521">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 文件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b9bc-523">更改连接字符串以指向名为 CU.db 的 SQLite 数据库文件：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-523">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="6b9bc-524">更新数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="6b9bc-524">Update the database context class</span></span>

<span data-ttu-id="6b9bc-525">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="6b9bc-526">上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6b9bc-527">上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="6b9bc-528">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="6b9bc-529">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="6b9bc-530">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="6b9bc-531">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-531">In EF Core terminology:</span></span>

* <span data-ttu-id="6b9bc-532">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="6b9bc-533">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6b9bc-534">由于实体集包含多个实体，因此 DBSet 属性应为复数名称。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="6b9bc-535">由于基架工具创建了 `Student` DBSet，因此此步骤将其更改为复数 `Students`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="6b9bc-536">为了使 Razor Pages 代码与新的 DBSet 名称相匹配，请在整个项目中进行全局更改，将 `_context.Student` 更改为 `_context.Students`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-536">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="6b9bc-537">更改发生 8 次。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-537">There are 8 occurrences.</span></span>

<span data-ttu-id="6b9bc-538">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="6b9bc-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="6b9bc-539">Startup.cs</span></span>

<span data-ttu-id="6b9bc-540">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6b9bc-541">在应用程序启动过程通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6b9bc-542">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-542">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6b9bc-543">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6b9bc-544">基架工具自动将上下文类注册到了依赖项注入容器。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-546">在 `ConfigureServices` 中，基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b9bc-548">在 `ConfigureServices` 中，确保基架所添加的代码调用 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="6b9bc-549">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6b9bc-550">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="6b9bc-551">创建数据库</span><span class="sxs-lookup"><span data-stu-id="6b9bc-551">Create the database</span></span>

<span data-ttu-id="6b9bc-552">如果没有数据库，请更新 Program.cs 以创建数据库：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="6b9bc-553">如果有上下文的数据库，则 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="6b9bc-554">如果没有数据库，则它将创建数据库和架构。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="6b9bc-555">`EnsureCreated` 启用以下工作流来处理数据模型更改：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="6b9bc-556">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-556">Delete the database.</span></span> <span data-ttu-id="6b9bc-557">任何现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-557">Any existing data is lost.</span></span>
* <span data-ttu-id="6b9bc-558">更改数据模型。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-558">Change the data model.</span></span> <span data-ttu-id="6b9bc-559">例如，添加 `EmailAddress` 字段。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="6b9bc-560">运行应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-560">Run the app.</span></span>
* <span data-ttu-id="6b9bc-561">`EnsureCreated` 创建具有新架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="6b9bc-562">在无需保存数据的情况下，当架构快速发展时，此工作流在早期开发过程中表现良好。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="6b9bc-563">如果需要保存已输入数据库的数据，情况就有所不同了。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="6b9bc-564">如果是这种情况，请使用迁移。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="6b9bc-565">本系列教程的后续部分将删除 `EnsureCreated` 创建的数据库，转而使用迁移。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="6b9bc-566">无法使用迁移更新 `EnsureCreated` 创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="6b9bc-567">测试应用</span><span class="sxs-lookup"><span data-stu-id="6b9bc-567">Test the app</span></span>

* <span data-ttu-id="6b9bc-568">运行应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-568">Run the app.</span></span>
* <span data-ttu-id="6b9bc-569">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-569">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="6b9bc-570">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="6b9bc-571">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="6b9bc-571">Seed the database</span></span>

<span data-ttu-id="6b9bc-572">`EnsureCreated` 方法将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="6b9bc-573">本节添加用测试数据填充数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="6b9bc-574">使用以下代码创建 Data/DbInitializer.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="6b9bc-575">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="6b9bc-576">如果不存在学生，它将向数据库添加测试数据。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="6b9bc-577">该代码使用数组创建测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="6b9bc-578">在 Program.cs 中，将 `EnsureCreated` 调用替换为 `DbInitializer.Initialize` 调用：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-578">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b9bc-580">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b9bc-582">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="6b9bc-583">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-583">Restart the app.</span></span>

* <span data-ttu-id="6b9bc-584">选择“学生”页查看已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="6b9bc-585">查看数据库</span><span class="sxs-lookup"><span data-stu-id="6b9bc-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-587">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="6b9bc-588">在 SSOX 中，依次选择“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="6b9bc-589">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="6b9bc-590">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="6b9bc-591">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="6b9bc-592">右键单击 Student 表，然后单击“查看代码”查看 `Student` 模型如何映射到 `Student` 表架构 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b9bc-594">使用 SQLite 工具查看数据库架构和已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="6b9bc-595">数据库文件名为 CU.db，位于项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="6b9bc-596">异步代码</span><span class="sxs-lookup"><span data-stu-id="6b9bc-596">Asynchronous code</span></span>

<span data-ttu-id="6b9bc-597">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="6b9bc-598">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="6b9bc-599">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="6b9bc-600">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="6b9bc-601">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="6b9bc-602">因此，使用异步代码可以更有效地利用服务器资源，并且服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="6b9bc-603">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="6b9bc-604">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="6b9bc-605">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="6b9bc-606">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="6b9bc-607">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="6b9bc-608">创建返回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 对象。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="6b9bc-609">返回类型 `Task<T>` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="6b9bc-610">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="6b9bc-611">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="6b9bc-612">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="6b9bc-613">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="6b9bc-614">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="6b9bc-615">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="6b9bc-616">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="6b9bc-617">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="6b9bc-618">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="6b9bc-619">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="6b9bc-620">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="6b9bc-621">后续步骤</span><span class="sxs-lookup"><span data-stu-id="6b9bc-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="6b9bc-622">下一教程</span><span class="sxs-lookup"><span data-stu-id="6b9bc-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6b9bc-623">Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 创建 ASP.NET Core Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="6b9bc-624">该示例应用是一个虚构的 Contoso University 的网站。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="6b9bc-625">其中包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="6b9bc-626">本页是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="6b9bc-627">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="6b9bc-628">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b9bc-629">先决条件</span><span class="sxs-lookup"><span data-stu-id="6b9bc-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="6b9bc-632">熟悉 [Razor Pages](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-632">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="6b9bc-633">新程序员在开始学习本系列之前，应先完成 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-633">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6b9bc-634">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6b9bc-634">Troubleshooting</span></span>

<span data-ttu-id="6b9bc-635">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="6b9bc-636">获取帮助的一个好方法是将问题发布到适用于 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 的 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="6b9bc-637">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="6b9bc-637">The Contoso University web app</span></span>

<span data-ttu-id="6b9bc-638">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="6b9bc-639">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="6b9bc-640">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-640">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

<span data-ttu-id="6b9bc-643">此网站的 UI 样式与内置模板生成的 UI 样式类似。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="6b9bc-644">教程的重点是 EF Core 和 Razor Pages，而非 UI。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-644">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="6b9bc-645">创建 ContosoUniversity Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="6b9bc-645">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-647">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-647">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6b9bc-648">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="6b9bc-649">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-649">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="6b9bc-650">务必将该项目命名为 ContosoUniversity，以便复制/粘贴代码时命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="6b9bc-651">在下拉列表中选择“ASP.NET Core 2.1”，然后选择“Web 应用程序” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="6b9bc-652">有关上述步骤的图像，请参阅[创建 Razor Web 应用](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-652">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="6b9bc-653">运行应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="6b9bc-655">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="6b9bc-655">Set up the site style</span></span>

<span data-ttu-id="6b9bc-656">设置网站菜单、布局和主页时需作少量更改。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="6b9bc-657">进行以下更改以更新 Pages/Shared/_Layout.cshtml：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="6b9bc-658">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="6b9bc-659">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-659">There are three occurrences.</span></span>

* <span data-ttu-id="6b9bc-660">添加菜单项 **Students**，**Courses**，**Instructors**，和 **Department**，并删除 **Contact**菜单项。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-660">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="6b9bc-661">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-661">The changes are highlighted.</span></span> <span data-ttu-id="6b9bc-662">（没有显示全部标记。）</span><span class="sxs-lookup"><span data-stu-id="6b9bc-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="6b9bc-663">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET 和 MVC 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-663">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="6b9bc-664">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="6b9bc-664">Create the data model</span></span>

<span data-ttu-id="6b9bc-665">创建 Contoso University 应用的实体类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="6b9bc-666">从以下三个实体开始：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-666">Start with the following three entities:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="6b9bc-668">`Student` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="6b9bc-669">`Course` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="6b9bc-670">一名学生可以报名参加任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="6b9bc-671">一门课程中可以包含任意数量的学生。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="6b9bc-672">以下部分将为这几个实体中的每一个实体创建一个类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="6b9bc-673">Student 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-673">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="6b9bc-675">创建 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-675">Create a *Models* folder.</span></span> <span data-ttu-id="6b9bc-676">在 Models 文件夹中，使用以下代码创建一个名为 Student.cs 的类文件 ：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="6b9bc-677">`ID` 属性成为此类对应的数据库 (DB) 表的主键列。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="6b9bc-678">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="6b9bc-679">在 `classnameID` 中，`classname` 为类名称。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="6b9bc-680">另一种自动识别的主键是上例中的 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="6b9bc-681">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="6b9bc-682">导航属性链接到与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="6b9bc-683">在这种情况下，`Student entity` 的 `Enrollments` 属性包含与该 `Student` 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="6b9bc-684">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="6b9bc-685">相关的 `Enrollment` 行是 `StudentID` 列中包含该学生的主键值的行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="6b9bc-686">例如，假设 ID=1 的学生在 `Enrollment` 表中有两行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="6b9bc-687">`Enrollment` 表中有两行的 `StudentID` = 1。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="6b9bc-688">`StudentID` 是 `Enrollment` 表中的外键，用于指定 `Student` 表中的学生。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="6b9bc-689">如果导航属性包含多个实体，则导航属性必须是列表类型，例如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="6b9bc-690">可以指定 `ICollection<T>` 或诸如 `List<T>` 或 `HashSet<T>` 的类型。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="6b9bc-691">使用 `ICollection<T>` 时，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="6b9bc-692">包含多个实体的导航属性来自于多对多和一对多关系。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="6b9bc-693">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-693">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="6b9bc-695">在 Models 文件夹中，使用以下代码创建 Enrollment.cs ：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="6b9bc-696">`EnrollmentID` 属性为主键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="6b9bc-697">`Student` 实体使用的是 `ID` 模式，而本实体使用的是 `classnameID` 模式。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="6b9bc-698">通常情况下，开发者会选择一种模式并在整个数据模型中都使用该模式。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="6b9bc-699">下一个教程将介绍如何使用不带类名的 ID，以便更轻松地在数据模型中实现集成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="6b9bc-700">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="6b9bc-701">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="6b9bc-702">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="6b9bc-703">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="6b9bc-704">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="6b9bc-705">`Student` 实体与 `Student.Enrollments` 导航属性不同，后者包含多个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="6b9bc-706">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="6b9bc-707">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="6b9bc-708">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="6b9bc-709">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="6b9bc-710">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="6b9bc-711">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="6b9bc-712">Course 实体</span><span class="sxs-lookup"><span data-stu-id="6b9bc-712">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="6b9bc-714">在 Models 文件夹中，使用以下代码创建 Course.cs ：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="6b9bc-715">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="6b9bc-716">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="6b9bc-717">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="6b9bc-718">为“学生”模型搭建基架</span><span class="sxs-lookup"><span data-stu-id="6b9bc-718">Scaffold the student model</span></span>

<span data-ttu-id="6b9bc-719">本部分将为“学生”模型搭建基架。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="6b9bc-720">确切地说，基架工具将生成页面，用于对“学生”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="6b9bc-721">生成项目。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-721">Build the project.</span></span>
* <span data-ttu-id="6b9bc-722">创建 Pages/Students 文件夹。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6b9bc-724">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-724">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="6b9bc-725">在“添加基架”对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-725">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="6b9bc-726">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-726">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="6b9bc-727">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="6b9bc-728">在“数据上下文类”行中，选择加号 (+) 并将生成的名称更改为 ContosoUniversity.Models.SchoolContext  。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="6b9bc-729">在“数据上下文类”下拉列表中，选择“ContosoUniversity.Models.SchoolContext” </span><span class="sxs-lookup"><span data-stu-id="6b9bc-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="6b9bc-730">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-730">Select **Add**.</span></span>

![CRUD 对话框](intro/_static/s1.png)

<span data-ttu-id="6b9bc-732">如果对前面的步骤有疑问，请参阅[搭建“电影”模型的基架](xref:tutorials/razor-pages/model#scaffold-the-movie-model)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6b9bc-734">运行以下命令，搭建“学生”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="6b9bc-735">搭建基架的过程会创建并更改以下文件：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="6b9bc-736">创建的文件</span><span class="sxs-lookup"><span data-stu-id="6b9bc-736">Files created</span></span>

* <span data-ttu-id="6b9bc-737">Pages/Students：“创建”、“删除”、“详细信息”、“编辑”、“索引”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="6b9bc-738">Data/SchoolContext.cs</span><span class="sxs-lookup"><span data-stu-id="6b9bc-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="6b9bc-739">更新的文件</span><span class="sxs-lookup"><span data-stu-id="6b9bc-739">File updates</span></span>

* <span data-ttu-id="6b9bc-740">*Startup.cs*：下一部分详细介绍对此文件所作的更改。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="6b9bc-741">*appsettings.json*：添加用于连接到本地数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-741">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6b9bc-742">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="6b9bc-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6b9bc-743">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6b9bc-744">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6b9bc-745">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-745">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6b9bc-746">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6b9bc-747">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="6b9bc-748">在 Startup.cs 中检查 `ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-748">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="6b9bc-749">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="6b9bc-750">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6b9bc-751">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="6b9bc-752">更新 main</span><span class="sxs-lookup"><span data-stu-id="6b9bc-752">Update main</span></span>

<span data-ttu-id="6b9bc-753">在 Program.cs 中，修改 `Main` 方法以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-753">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="6b9bc-754">从依赖关系注入容器获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="6b9bc-755">调用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="6b9bc-756">`EnsureCreated` 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="6b9bc-757">下面的代码显示更新后的 Program.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="6b9bc-758">`EnsureCreated` 确保存在上下文数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="6b9bc-759">如果存在，则不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-759">If it exists, no action is taken.</span></span> <span data-ttu-id="6b9bc-760">如果不存在，则会创建数据库及其所有架构。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="6b9bc-761">`EnsureCreated` 不使用迁移创建数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="6b9bc-762">使用 `EnsureCreated` 创建的数据库稍后无法使用迁移更新。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="6b9bc-763">启动应用时会调用 `EnsureCreated`，以进行以下工作流：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="6b9bc-764">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-764">Delete the DB.</span></span>
* <span data-ttu-id="6b9bc-765">更改数据库架构（例如添加一个 `EmailAddress` 字段）。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="6b9bc-766">运行应用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-766">Run the app.</span></span>
* <span data-ttu-id="6b9bc-767">`EnsureCreated` 创建一个带有 `EmailAddress` 列的数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="6b9bc-768">架构快速演变时，在开发初期使用 `EnsureCreated` 很方便。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="6b9bc-769">本教程后面将删除 DB 并使用迁移。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="6b9bc-770">测试应用</span><span class="sxs-lookup"><span data-stu-id="6b9bc-770">Test the app</span></span>

<span data-ttu-id="6b9bc-771">运行应用并接受 cookie 策略。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-771">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="6b9bc-772">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="6b9bc-773">有关 cookie 策略的信息，请参阅[欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-773">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="6b9bc-774">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-774">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="6b9bc-775">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="6b9bc-776">检查 SchoolContext DB 上下文</span><span class="sxs-lookup"><span data-stu-id="6b9bc-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="6b9bc-777">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="6b9bc-778">数据上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6b9bc-779">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="6b9bc-780">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="6b9bc-781">使用以下代码更新 SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="6b9bc-782">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="6b9bc-783">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-783">In EF Core terminology:</span></span>

* <span data-ttu-id="6b9bc-784">实体集通常对应一个数据库表。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="6b9bc-785">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6b9bc-786">可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="6b9bc-787">EF Core 隐式包含了它们，因为 `Student` 实体引用 `Enrollment` 实体，而 `Enrollment` 实体引用 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="6b9bc-788">在本教程中，将 `DbSet<Enrollment>` 和 `DbSet<Course>` 保留在 `SchoolContext` 中。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="6b9bc-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="6b9bc-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6b9bc-790">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="6b9bc-791">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="6b9bc-792">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6b9bc-793">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="6b9bc-794">添加代码，以使用测试数据初始化该数据库</span><span class="sxs-lookup"><span data-stu-id="6b9bc-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="6b9bc-795">EF Core 会创建一个空的数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="6b9bc-796">本部分中编写了 `Initialize` 方法来使用测试数据填充该数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="6b9bc-797">在 Data 文件夹中，新建一个名为 DbInitializer.cs 的类文件，并添加以下代码 ：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="6b9bc-798">注意：上面的代码对命名空间使用 `Models` (`namespace ContosoUniversity.Models`)，而不是 `Data`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="6b9bc-799">`Models` 与基架生成的代码一致。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="6b9bc-800">有关详细信息，请参阅[此 GitHub 基架问题](https://github.com/aspnet/Scaffolding/issues/822)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="6b9bc-801">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="6b9bc-802">如果 DB 中没有任何学生，则会使用测试数据初始化该 DB。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="6b9bc-803">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="6b9bc-804">`EnsureCreated` 方法自动为数据库上下文创建数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="6b9bc-805">如果数据库已存在，则返回 `EnsureCreated`，并且不修改数据库。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="6b9bc-806">在 Program.cs 中，将 `Main` 方法修改为调用 `Initialize`：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-806">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="6b9bc-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6b9bc-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6b9bc-808">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="6b9bc-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6b9bc-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6b9bc-810">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="6b9bc-811">查看数据库</span><span class="sxs-lookup"><span data-stu-id="6b9bc-811">View the DB</span></span>

<span data-ttu-id="6b9bc-812">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="6b9bc-813">因此，数据库名称为“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="6b9bc-814">GUID 因用户而异。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="6b9bc-815">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="6b9bc-816">在 SSOX 中，依次单击“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="6b9bc-817">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="6b9bc-818">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="6b9bc-819">异步代码</span><span class="sxs-lookup"><span data-stu-id="6b9bc-819">Asynchronous code</span></span>

<span data-ttu-id="6b9bc-820">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="6b9bc-821">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="6b9bc-822">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="6b9bc-823">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="6b9bc-824">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="6b9bc-825">因此，使用异步代码可以更有效地利用服务器资源，并且可以让服务器在没有延迟的情况下处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="6b9bc-826">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="6b9bc-827">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="6b9bc-828">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="6b9bc-829">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="6b9bc-830">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="6b9bc-831">自动创建返回的 [Task](/dotnet/api/system.threading.tasks.task) 对象。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="6b9bc-832">有关详细信息，请参阅[任务返回类型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="6b9bc-833">隐式返回类型 `Task` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="6b9bc-834">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="6b9bc-835">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="6b9bc-836">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="6b9bc-837">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="6b9bc-838">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="6b9bc-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="6b9bc-839">只会异步执行导致查询或命令被发送到数据库的语句。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="6b9bc-840">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="6b9bc-841">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="6b9bc-842">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="6b9bc-843">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="6b9bc-844">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="6b9bc-845">下一个教程将介绍基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="6b9bc-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="6b9bc-846">其他资源</span><span class="sxs-lookup"><span data-stu-id="6b9bc-846">Additional resources</span></span>

* [<span data-ttu-id="6b9bc-847">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="6b9bc-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="6b9bc-848">下一页</span><span class="sxs-lookup"><span data-stu-id="6b9bc-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
