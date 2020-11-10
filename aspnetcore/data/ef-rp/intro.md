---
title: 'ASP.NET Core 中的 :::no-loc(Razor)::: Pages 和 Entity Framework Core - 第 1 个教程（共 8 个）'
author: rick-anderson
description: '介绍如何使用 Entity Framework Core 创建 :::no-loc(Razor)::: Pages 应用'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/intro
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365413"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="bbbda-103">ASP.NET Core 中的 :::no-loc(Razor)::: Pages 和 Entity Framework Core - 第 1 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="bbbda-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="bbbda-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bbbda-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bbbda-105">本文是系列教程的第一篇，这些教程展示如何在 [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) 应用中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="bbbda-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="bbbda-106">这些教程为虚构的 Contoso University 生成一个网站。</span><span class="sxs-lookup"><span data-stu-id="bbbda-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="bbbda-107">网站包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="bbbda-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="bbbda-108">本教程使用代码优先方法。</span><span class="sxs-lookup"><span data-stu-id="bbbda-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="bbbda-109">有关使用数据库优先方法学习本教程的信息，请参阅[此 Github 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="bbbda-110">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="bbbda-111">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bbbda-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="bbbda-112">Prerequisites</span></span>

* <span data-ttu-id="bbbda-113">如果不熟悉 :::no-loc(Razor)::: Pages，则在开始前，请浏览 [:::no-loc(Razor)::: Pages 入门](xref:tutorials/razor-pages/razor-pages-start)系列教程。</span><span class="sxs-lookup"><span data-stu-id="bbbda-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="bbbda-116">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="bbbda-116">Database engines</span></span>

<span data-ttu-id="bbbda-117">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="bbbda-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="bbbda-118">Visual Studio Code 指令使用 [SQLite](https://www.sqlite.org/)，一种跨平台数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="bbbda-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="bbbda-119">如果选择使用 SQLite，请下载并安装[适用于 SQLite 的数据库浏览器](https://sqlitebrowser.org/)等第三方工具，用于管理和查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bbbda-120">疑难解答</span><span class="sxs-lookup"><span data-stu-id="bbbda-120">Troubleshooting</span></span>

<span data-ttu-id="bbbda-121">如果遇到无法解决的问题，请将你的代码与[完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)进行比较。</span><span class="sxs-lookup"><span data-stu-id="bbbda-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="bbbda-122">获取帮助的一个好方法是使用 [ASP.NET Core 标记](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 标记](https://stackoverflow.com/questions/tagged/entity-framework-core)将问题发布到 StackOverflow.com。</span><span class="sxs-lookup"><span data-stu-id="bbbda-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="bbbda-123">示例应用</span><span class="sxs-lookup"><span data-stu-id="bbbda-123">The sample app</span></span>

<span data-ttu-id="bbbda-124">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="bbbda-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="bbbda-125">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="bbbda-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="bbbda-126">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="bbbda-126">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index30.png)

![学生编辑页](intro/_static/student-edit30.png)

<span data-ttu-id="bbbda-129">此网站的 UI 样式基于内置的项目模板。</span><span class="sxs-lookup"><span data-stu-id="bbbda-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="bbbda-130">本教程侧重于如何将 EF Core 和 ASP.NET Core 结合在一起使用，而不是如何自定义 UI。</span><span class="sxs-lookup"><span data-stu-id="bbbda-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="bbbda-131">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="bbbda-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-133">启动 Visual Studio 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="bbbda-134">选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="bbbda-135">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="bbbda-136">请务必使用此名称（含大写），确保在复制和粘贴代码时与命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="bbbda-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="bbbda-137">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-137">Select **Create**.</span></span>
* <span data-ttu-id="bbbda-138">在下拉列表中选择“.NET Core”和“ASP.NET Core 5.0”，然后选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bbbda-140">在终端中，导航到应在其中创建项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="bbbda-141">运行以下命令，在新的项目文件夹中创建 :::no-loc(Razor)::: Pages 项目和 `cd`：</span><span class="sxs-lookup"><span data-stu-id="bbbda-141">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="bbbda-142">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="bbbda-142">Set up the site style</span></span>

<span data-ttu-id="bbbda-143">将下面的代码复制并粘贴到 Pages/Shared/_Layout.cshtml 文件中：[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="bbbda-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="bbbda-144">布局文件会设置网站页眉、页脚和菜单。</span><span class="sxs-lookup"><span data-stu-id="bbbda-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="bbbda-145">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="bbbda-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="bbbda-146">将文件中的“ContosoUniversity”更改为“Contoso University”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="bbbda-147">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="bbbda-147">There are three occurrences.</span></span>
* <span data-ttu-id="bbbda-148">删除“主页”和“隐私”菜单项。</span><span class="sxs-lookup"><span data-stu-id="bbbda-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="bbbda-149">添加“关于”、“学生”、“课程”、“讲师”和“部门”项。</span><span class="sxs-lookup"><span data-stu-id="bbbda-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="bbbda-150">在 Pages/Index.cshtml 中，将该文件的内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="bbbda-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="bbbda-151">前面的代码会将关于 ASP.NET Core 的文本替换为关于此应用的文本。</span><span class="sxs-lookup"><span data-stu-id="bbbda-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="bbbda-152">运行应用以验证主页是否显示。</span><span class="sxs-lookup"><span data-stu-id="bbbda-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="bbbda-153">数据模型</span><span class="sxs-lookup"><span data-stu-id="bbbda-153">The data model</span></span>

<span data-ttu-id="bbbda-154">以下部分用于创建数据模型：</span><span class="sxs-lookup"><span data-stu-id="bbbda-154">The following sections create a data model:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="bbbda-156">一名学生可以修读任意数量的课程，并且某一课程可以有任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="bbbda-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="bbbda-157">Student 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-157">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

* <span data-ttu-id="bbbda-159">在项目文件夹中创建“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="bbbda-160">使用以下代码创建 Models/Student.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="bbbda-161">`ID` 属性成为此类对应的数据库表的主键列。</span><span class="sxs-lookup"><span data-stu-id="bbbda-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="bbbda-162">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="bbbda-163">因此，`Student` 类主键的另一种自动识别的名称是 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="bbbda-164">有关详细信息，请参阅 [F Core - 密钥](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="bbbda-165">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="bbbda-166">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="bbbda-167">在本例中，`Student` 实体的 `Enrollments` 属性包含与该 Student 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="bbbda-168">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="bbbda-169">在数据库中，如果 StudentID 列包含学生的 ID 值，则 Enrollment 行与 Student 行相关。</span><span class="sxs-lookup"><span data-stu-id="bbbda-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="bbbda-170">例如，假设某个 Student 行的 ID=1。</span><span class="sxs-lookup"><span data-stu-id="bbbda-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="bbbda-171">则相关 Enrollment 行的 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="bbbda-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="bbbda-172">StudentID 是 Enrollment 表中的外键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="bbbda-173">`Enrollments` 属性定义为 `ICollection<Enrollment>`，因为可能有多个相关的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="bbbda-174">可以使用 `List<Enrollment>` 或 `HashSet<Enrollment>` 等其他集合类型。</span><span class="sxs-lookup"><span data-stu-id="bbbda-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="bbbda-175">使用 `ICollection<Enrollment>` 时，EF Core 会默认创建 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="bbbda-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="bbbda-176">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-176">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="bbbda-178">使用以下代码创建 Models/Enrollment.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="bbbda-179">`EnrollmentID` 属性为主键；此实体使用 `classnameID` 模式而不是直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="bbbda-180">对于生产数据模型，请选择一个模式并一直使用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="bbbda-181">本教程两个都使用，只是为了说明这两个模式都能使用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="bbbda-182">使用不具有 `classname` 的 `ID` 可以更轻松地实现某些类型的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="bbbda-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="bbbda-183">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="bbbda-184">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 [null](/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="bbbda-185">评级为 null 和评级为零是有区别的 &mdash; null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="bbbda-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="bbbda-186">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="bbbda-187">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="bbbda-188">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="bbbda-189">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="bbbda-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="bbbda-190">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="bbbda-191">例如，`StudentID` 是 `Student` 导航属性的外键，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="bbbda-192">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="bbbda-193">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="bbbda-194">Course 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-194">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="bbbda-196">使用以下代码创建 Models/Course.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="bbbda-197">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="bbbda-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="bbbda-198">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="bbbda-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="bbbda-199">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="bbbda-200">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="bbbda-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="bbbda-201">搭建“学生”页的基架</span><span class="sxs-lookup"><span data-stu-id="bbbda-201">Scaffold Student pages</span></span>

<span data-ttu-id="bbbda-202">本部分使用 ASP.NET Core 基架工具生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="bbbda-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="bbbda-203">EF Core `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="bbbda-204">上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="bbbda-205">它派生自 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="bbbda-206">:::no-loc(Razor)::: 页面，可处理 `Student` 实体的创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-206">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-208">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="bbbda-209">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹，然后选择“添加”>“新搭建基架的项目” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="bbbda-210">在“添加新基架项”对话框中：</span><span class="sxs-lookup"><span data-stu-id="bbbda-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="bbbda-211">在左侧选项卡中，依次选择“已安装 > 通用 > :::no-loc(Razor)::: 页面”</span><span class="sxs-lookup"><span data-stu-id="bbbda-211">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="bbbda-212">依次选择“使用实体框架的 :::no-loc(Razor)::: 页面(CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-212">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="bbbda-213">在“添加使用实体框架的 :::no-loc(Razor)::: Pages (CRUD)”对话框中：</span><span class="sxs-lookup"><span data-stu-id="bbbda-213">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="bbbda-214">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="bbbda-215">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="bbbda-216">将数据上下文名称更改为以 `SchoolContext` 结尾，而不以 `ContosoUniversityContext` 结尾。</span><span class="sxs-lookup"><span data-stu-id="bbbda-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="bbbda-217">更新后的上下文名称为：`ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="bbbda-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="bbbda-218">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-218">Select **Add**.</span></span>

<span data-ttu-id="bbbda-219">自动安装以下包：</span><span class="sxs-lookup"><span data-stu-id="bbbda-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bbbda-221">运行以下 .NET Core CLI 命令，安装所需的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="bbbda-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="bbbda-222">基架需要 Microsoft.VisualStudio.Web.CodeGeneration.Design 包。</span><span class="sxs-lookup"><span data-stu-id="bbbda-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="bbbda-223">虽然应用不使用 SQL Server，但基架工具需要 SQL Server 包。</span><span class="sxs-lookup"><span data-stu-id="bbbda-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="bbbda-224">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="bbbda-225">运行以下命令安装 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="bbbda-226">运行以下命令，搭建“学生”页的基架。</span><span class="sxs-lookup"><span data-stu-id="bbbda-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="bbbda-227">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="bbbda-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="bbbda-228">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="bbbda-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="bbbda-229">如果上述步骤失败，请生成项目并重试基架搭建步骤。</span><span class="sxs-lookup"><span data-stu-id="bbbda-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="bbbda-230">基架流程：</span><span class="sxs-lookup"><span data-stu-id="bbbda-230">The scaffolding process:</span></span>

* <span data-ttu-id="bbbda-231">在“Pages/Students”文件夹中创建 :::no-loc(Razor)::: 页面：</span><span class="sxs-lookup"><span data-stu-id="bbbda-231">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="bbbda-232">Create.cshtml 和 Create.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="bbbda-233">Delete.cshtml 和 Delete.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="bbbda-234">Details.cshtml 和 Details.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="bbbda-235">Edit.cshtml 和 Edit.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="bbbda-236">Index.cshtml 和 Index.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="bbbda-237">创建 Data/SchoolContext.cs。</span><span class="sxs-lookup"><span data-stu-id="bbbda-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="bbbda-238">将上下文添加到 Startup.cs 中的依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="bbbda-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="bbbda-239">将数据库连接字符串添加到 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-239">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="bbbda-240">数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="bbbda-240">Database connection string</span></span>

<span data-ttu-id="bbbda-241">基架工具会在 *:::no-loc(appsettings.json):::* 文件中生成连接字符串。</span><span class="sxs-lookup"><span data-stu-id="bbbda-241">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bbbda-243">此连接字符串指定了 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)：</span><span class="sxs-lookup"><span data-stu-id="bbbda-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="bbbda-244">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="bbbda-245">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 文件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bbbda-247">将 SQLite 连接字符串缩写为 CU.db：</span><span class="sxs-lookup"><span data-stu-id="bbbda-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="bbbda-248">更新数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="bbbda-248">Update the database context class</span></span>

<span data-ttu-id="bbbda-249">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="bbbda-250">上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="bbbda-251">上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="bbbda-252">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="bbbda-253">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="bbbda-254">上述代码会将单数形式的 `DbSet<Student> Student` 更改为复数形式的 `DbSet<Student> Students`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="bbbda-255">若要使 :::no-loc(Razor)::: 页面代码与新的 `DBSet` 名称匹配，请进行以下全局更改：`_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="bbbda-255">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="bbbda-256">更改为 `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="bbbda-256">to: `_context.Students.`</span></span>

<span data-ttu-id="bbbda-257">更改发生 8 次。</span><span class="sxs-lookup"><span data-stu-id="bbbda-257">There are 8 occurrences.</span></span>

<span data-ttu-id="bbbda-258">由于一个实体集包含多个实体，因此许多开发人员更倾向于使用复数形式的 `DBSet` 属性名称。</span><span class="sxs-lookup"><span data-stu-id="bbbda-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="bbbda-259">突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="bbbda-259">The highlighted code:</span></span>

* <span data-ttu-id="bbbda-260">为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="bbbda-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="bbbda-261">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="bbbda-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="bbbda-262">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="bbbda-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="bbbda-263">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="bbbda-264">调用 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>。</span><span class="sxs-lookup"><span data-stu-id="bbbda-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="bbbda-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="bbbda-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="bbbda-266">在完成对 `SchoolContext` 的初始化后，并在模型已锁定并用于初始化上下文之前，进行调用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="bbbda-267">是必需的，因为在本教程的后续部分中，`Student` 实体将引用其他实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="bbbda-268">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="bbbda-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="bbbda-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="bbbda-269">Startup.cs</span></span>

<span data-ttu-id="bbbda-270">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bbbda-271">服务（例如 `SchoolContext`）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="bbbda-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="bbbda-272">需要这些服务（如 :::no-loc(Razor)::: 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="bbbda-272">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="bbbda-273">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="bbbda-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="bbbda-274">基架工具自动将上下文类注册到了依赖项注入容器。</span><span class="sxs-lookup"><span data-stu-id="bbbda-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bbbda-276">基架添加了下列突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="bbbda-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bbbda-278">验证基架所添加的代码是否调用 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="bbbda-279">有关使用生产数据库的信息，请参阅[将 SQLite 用于开发，将 SQL Server 用于生产](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="bbbda-280">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="bbbda-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="bbbda-281">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *:::no-loc(appsettings.json):::* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="bbbda-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="bbbda-282">添加数据库异常筛选器</span><span class="sxs-lookup"><span data-stu-id="bbbda-282">Add the database exception filter</span></span>

<span data-ttu-id="bbbda-283">将 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> 添加到 `ConfigureServices`，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="bbbda-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="bbbda-285">添加 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="bbbda-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="bbbda-286">在 PMC 中，输入以下命令来添加此 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="bbbda-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="bbbda-288">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 包提供 Entity Framework Core 错误页的 ASP.NET Core 中间件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="bbbda-289">此中间件有助于检测和诊断 Entity Framework Core 迁移错误。</span><span class="sxs-lookup"><span data-stu-id="bbbda-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="bbbda-290">`AddDatabaseDeveloperPageExceptionFilter` 在[开发环境](xref:fundamentals/environments)中提供有用的错误信息。</span><span class="sxs-lookup"><span data-stu-id="bbbda-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="bbbda-291">创建数据库</span><span class="sxs-lookup"><span data-stu-id="bbbda-291">Create the database</span></span>

<span data-ttu-id="bbbda-292">如果没有数据库，请更新 Program.cs 以创建数据库：</span><span class="sxs-lookup"><span data-stu-id="bbbda-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="bbbda-293">如果有上下文的数据库，则 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="bbbda-294">如果没有数据库，则它将创建数据库和架构。</span><span class="sxs-lookup"><span data-stu-id="bbbda-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="bbbda-295">`EnsureCreated` 启用以下工作流来处理数据模型更改：</span><span class="sxs-lookup"><span data-stu-id="bbbda-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="bbbda-296">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-296">Delete the database.</span></span> <span data-ttu-id="bbbda-297">任何现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="bbbda-297">Any existing data is lost.</span></span>
* <span data-ttu-id="bbbda-298">更改数据模型。</span><span class="sxs-lookup"><span data-stu-id="bbbda-298">Change the data model.</span></span> <span data-ttu-id="bbbda-299">例如，添加 `EmailAddress` 字段。</span><span class="sxs-lookup"><span data-stu-id="bbbda-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="bbbda-300">运行应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-300">Run the app.</span></span>
* <span data-ttu-id="bbbda-301">`EnsureCreated` 创建具有新架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="bbbda-302">在无需保存数据的情况下，当架构快速发展时，此工作流在早期开发过程中表现良好。</span><span class="sxs-lookup"><span data-stu-id="bbbda-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="bbbda-303">如果需要保存已输入数据库的数据，情况就有所不同了。</span><span class="sxs-lookup"><span data-stu-id="bbbda-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="bbbda-304">如果是这种情况，请使用迁移。</span><span class="sxs-lookup"><span data-stu-id="bbbda-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="bbbda-305">本系列教程的后续部分将删除 `EnsureCreated` 创建的数据库，转而使用迁移。</span><span class="sxs-lookup"><span data-stu-id="bbbda-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="bbbda-306">无法使用迁移更新 `EnsureCreated` 创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="bbbda-307">测试应用</span><span class="sxs-lookup"><span data-stu-id="bbbda-307">Test the app</span></span>

* <span data-ttu-id="bbbda-308">运行应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-308">Run the app.</span></span>
* <span data-ttu-id="bbbda-309">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="bbbda-310">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="bbbda-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="bbbda-311">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="bbbda-311">Seed the database</span></span>

<span data-ttu-id="bbbda-312">`EnsureCreated` 方法将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="bbbda-313">本节添加用测试数据填充数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="bbbda-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="bbbda-314">使用以下代码创建 Data/DbInitializer.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="bbbda-315">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="bbbda-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="bbbda-316">如果不存在学生，它将向数据库添加测试数据。</span><span class="sxs-lookup"><span data-stu-id="bbbda-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="bbbda-317">该代码使用数组创建测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="bbbda-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="bbbda-318">在 Program.cs 中，将 `EnsureCreated` 调用替换为 `DbInitializer.Initialize` 调用：</span><span class="sxs-lookup"><span data-stu-id="bbbda-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bbbda-320">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bbbda-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="bbbda-321">使用 `Y` 进行响应，以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bbbda-323">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="bbbda-324">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-324">Restart the app.</span></span>
* <span data-ttu-id="bbbda-325">选择“学生”页查看已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="bbbda-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="bbbda-326">查看数据库</span><span class="sxs-lookup"><span data-stu-id="bbbda-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-328">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="bbbda-329">在 SSOX 中，依次选择“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="bbbda-330">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="bbbda-331">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="bbbda-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="bbbda-332">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="bbbda-333">右键单击 Student 表，然后单击“查看代码”查看 `Student` 模型如何映射到 `Student` 表架构 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bbbda-335">使用 SQLite 工具查看数据库架构和已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="bbbda-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="bbbda-336">数据库文件名为 CU.db，位于项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="bbbda-337">异步代码</span><span class="sxs-lookup"><span data-stu-id="bbbda-337">Asynchronous code</span></span>

<span data-ttu-id="bbbda-338">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="bbbda-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="bbbda-339">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="bbbda-340">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="bbbda-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="bbbda-341">使用同步代码时，可能会出现多个线程被占用但不能执行操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="bbbda-342">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="bbbda-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="bbbda-343">因此，使用异步代码可以更有效地利用服务器资源，并且服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="bbbda-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="bbbda-344">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="bbbda-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="bbbda-345">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="bbbda-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="bbbda-346">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="bbbda-347">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bbbda-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="bbbda-348">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="bbbda-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="bbbda-349">创建返回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 对象。</span><span class="sxs-lookup"><span data-stu-id="bbbda-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="bbbda-350">返回类型 `Task<T>` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="bbbda-351">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="bbbda-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="bbbda-352">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="bbbda-353">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="bbbda-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="bbbda-354">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="bbbda-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="bbbda-355">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="bbbda-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="bbbda-356">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="bbbda-357">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="bbbda-358">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="bbbda-359">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="bbbda-360">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="bbbda-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="bbbda-361">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="bbbda-362">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="bbbda-362">Performance considerations</span></span>

<span data-ttu-id="bbbda-363">通常，网页不应加载任何数量的行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="bbbda-364">查询应使用分页或限制方法。</span><span class="sxs-lookup"><span data-stu-id="bbbda-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="bbbda-365">例如，上述查询可以使用 `Take` 来限制返回的行数：</span><span class="sxs-lookup"><span data-stu-id="bbbda-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="bbbda-366">如果在枚举过程中出现异常数据库，则枚举视图中的大型表可能返回部分构造的 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="bbbda-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="bbbda-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> 默认值为 1024。</span><span class="sxs-lookup"><span data-stu-id="bbbda-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="bbbda-368">以下代码将设置 `MaxModelBindingCollectionSize`：</span><span class="sxs-lookup"><span data-stu-id="bbbda-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="bbbda-369">稍后将在教程中介绍分页。</span><span class="sxs-lookup"><span data-stu-id="bbbda-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bbbda-370">后续步骤</span><span class="sxs-lookup"><span data-stu-id="bbbda-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bbbda-371">下一教程</span><span class="sxs-lookup"><span data-stu-id="bbbda-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="bbbda-372">本文是系列教程的第一篇，这些教程展示如何在 [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) 应用中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="bbbda-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="bbbda-373">这些教程为虚构的 Contoso University 生成一个网站。</span><span class="sxs-lookup"><span data-stu-id="bbbda-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="bbbda-374">网站包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="bbbda-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="bbbda-375">本教程使用代码优先方法。</span><span class="sxs-lookup"><span data-stu-id="bbbda-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="bbbda-376">有关使用数据库优先方法学习本教程的信息，请参阅[此 Github 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="bbbda-377">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="bbbda-378">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bbbda-379">先决条件</span><span class="sxs-lookup"><span data-stu-id="bbbda-379">Prerequisites</span></span>

* <span data-ttu-id="bbbda-380">如果不熟悉 :::no-loc(Razor)::: Pages，则在开始前，请浏览 [:::no-loc(Razor)::: Pages 入门](xref:tutorials/razor-pages/razor-pages-start)系列教程。</span><span class="sxs-lookup"><span data-stu-id="bbbda-380">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="bbbda-383">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="bbbda-383">Database engines</span></span>

<span data-ttu-id="bbbda-384">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="bbbda-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="bbbda-385">Visual Studio Code 指令使用 [SQLite](https://www.sqlite.org/)，一种跨平台数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="bbbda-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="bbbda-386">如果选择使用 SQLite，请下载并安装[适用于 SQLite 的数据库浏览器](https://sqlitebrowser.org/)等第三方工具，用于管理和查看 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bbbda-387">疑难解答</span><span class="sxs-lookup"><span data-stu-id="bbbda-387">Troubleshooting</span></span>

<span data-ttu-id="bbbda-388">如果遇到无法解决的问题，请将你的代码与[完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)进行比较。</span><span class="sxs-lookup"><span data-stu-id="bbbda-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="bbbda-389">获取帮助的一个好方法是使用 [ASP.NET Core 标记](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 标记](https://stackoverflow.com/questions/tagged/entity-framework-core)将问题发布到 StackOverflow.com。</span><span class="sxs-lookup"><span data-stu-id="bbbda-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="bbbda-390">示例应用</span><span class="sxs-lookup"><span data-stu-id="bbbda-390">The sample app</span></span>

<span data-ttu-id="bbbda-391">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="bbbda-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="bbbda-392">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="bbbda-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="bbbda-393">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="bbbda-393">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index30.png)

![学生编辑页](intro/_static/student-edit30.png)

<span data-ttu-id="bbbda-396">此网站的 UI 样式基于内置的项目模板。</span><span class="sxs-lookup"><span data-stu-id="bbbda-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="bbbda-397">本教程侧重于如何使用 EF Core，而不是如何自定义 UI。</span><span class="sxs-lookup"><span data-stu-id="bbbda-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="bbbda-398">单击页面顶部的链接，获取已完成项目的源代码。</span><span class="sxs-lookup"><span data-stu-id="bbbda-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="bbbda-399">“cu30”文件夹中有本教程的 ASP.NET Core 3.0 版本的代码。</span><span class="sxs-lookup"><span data-stu-id="bbbda-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="bbbda-400">在“cu30snapshots”文件夹中可以找到反映教程 1-7 代码状态的文件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bbbda-402">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bbbda-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="bbbda-403">生成项目。</span><span class="sxs-lookup"><span data-stu-id="bbbda-403">Build the project.</span></span>
* <span data-ttu-id="bbbda-404">在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bbbda-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="bbbda-405">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="bbbda-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bbbda-407">若要在下载完成的项目之后运行应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bbbda-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="bbbda-408">删除 ContosoUniversity.csproj，然后将 ContosoUniversitySQLite.csproj 重命名为 ContosoUniversity.csproj \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="bbbda-409">在“Program.cs”中注释掉 `#define Startup`，以便使用 `StartupSQLite`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="bbbda-410">删除 appSettings.json，然后将 appSettingsSQLite.json 重命名为 appSettings.json \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="bbbda-411">删除“Migrations”文件夹，然后将 MigrationsSQL 重命名为 Migrations  。</span><span class="sxs-lookup"><span data-stu-id="bbbda-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="bbbda-412">对 `#if SQLiteVersion` 执行全局搜索，并删除 `#if SQLiteVersion` 和相关 `#endif` 语句。</span><span class="sxs-lookup"><span data-stu-id="bbbda-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="bbbda-413">生成项目。</span><span class="sxs-lookup"><span data-stu-id="bbbda-413">Build the project.</span></span>
* <span data-ttu-id="bbbda-414">在项目文件夹中的命令提示符下运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bbbda-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="bbbda-415">在 SQLite 工具中，运行以下 SQL 语句：</span><span class="sxs-lookup"><span data-stu-id="bbbda-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="bbbda-416">运行项目，设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="bbbda-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="bbbda-417">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="bbbda-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-419">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bbbda-420">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bbbda-421">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="bbbda-422">请务必使用此名称（含大写），确保在复制和粘贴代码时与命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="bbbda-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="bbbda-423">在下拉列表中选择“.NET Core”和“ASP.NET Core 3.0”，然后选择“Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="bbbda-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bbbda-425">在终端中，导航到应在其中创建项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="bbbda-426">运行以下命令，在新的项目文件夹中创建 :::no-loc(Razor)::: Pages 项目和 `cd`：</span><span class="sxs-lookup"><span data-stu-id="bbbda-426">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="bbbda-427">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="bbbda-427">Set up the site style</span></span>

<span data-ttu-id="bbbda-428">更新 Pages/Shared/_Layout.cshtml 以设置网站的页眉、页脚和菜单：</span><span class="sxs-lookup"><span data-stu-id="bbbda-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="bbbda-429">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="bbbda-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="bbbda-430">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="bbbda-430">There are three occurrences.</span></span>

* <span data-ttu-id="bbbda-431">删除“主页”和“隐私”菜单项，然后添加“关于”、“学生”、“课程”、“讲师”和“院系”的菜单项      。</span><span class="sxs-lookup"><span data-stu-id="bbbda-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="bbbda-432">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="bbbda-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="bbbda-433">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET Core 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="bbbda-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="bbbda-434">运行应用以验证主页是否显示。</span><span class="sxs-lookup"><span data-stu-id="bbbda-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="bbbda-435">数据模型</span><span class="sxs-lookup"><span data-stu-id="bbbda-435">The data model</span></span>

<span data-ttu-id="bbbda-436">以下部分用于创建数据模型：</span><span class="sxs-lookup"><span data-stu-id="bbbda-436">The following sections create a data model:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="bbbda-438">一名学生可以修读任意数量的课程，并且某一课程可以有任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="bbbda-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="bbbda-439">Student 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-439">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

* <span data-ttu-id="bbbda-441">在项目文件夹中创建“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="bbbda-442">使用以下代码创建 Models/Student.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="bbbda-443">`ID` 属性成为此类对应的数据库表的主键列。</span><span class="sxs-lookup"><span data-stu-id="bbbda-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="bbbda-444">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="bbbda-445">因此，`Student` 类主键的另一种自动识别的名称是 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="bbbda-446">有关详细信息，请参阅 [F Core - 密钥](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="bbbda-447">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="bbbda-448">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="bbbda-449">在本例中，`Student` 实体的 `Enrollments` 属性包含与该 Student 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="bbbda-450">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="bbbda-451">在数据库中，如果 StudentID 列包含学生的 ID 值，则 Enrollment 行与 Student 行相关。</span><span class="sxs-lookup"><span data-stu-id="bbbda-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="bbbda-452">例如，假设某个 Student 行的 ID=1。</span><span class="sxs-lookup"><span data-stu-id="bbbda-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="bbbda-453">则相关 Enrollment 行的 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="bbbda-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="bbbda-454">StudentID 是 Enrollment 表中的外键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="bbbda-455">`Enrollments` 属性定义为 `ICollection<Enrollment>`，因为可能有多个相关的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="bbbda-456">可以使用 `List<Enrollment>` 或 `HashSet<Enrollment>` 等其他集合类型。</span><span class="sxs-lookup"><span data-stu-id="bbbda-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="bbbda-457">使用 `ICollection<Enrollment>` 时，EF Core 会默认创建 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="bbbda-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="bbbda-458">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-458">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="bbbda-460">使用以下代码创建 Models/Enrollment.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="bbbda-461">`EnrollmentID` 属性为主键；此实体使用 `classnameID` 模式而不是直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="bbbda-462">对于生产数据模型，请选择一个模式并一直使用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="bbbda-463">本教程两个都使用，只是为了说明这两个模式都能使用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="bbbda-464">使用不具有 `classname` 的 `ID` 可以更轻松地实现某些类型的数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="bbbda-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="bbbda-465">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="bbbda-466">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 [null](/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="bbbda-467">评级为 null 和评级为零是有区别的 &mdash; null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="bbbda-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="bbbda-468">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="bbbda-469">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="bbbda-470">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="bbbda-471">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="bbbda-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="bbbda-472">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="bbbda-473">例如，`StudentID` 是 `Student` 导航属性的外键，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="bbbda-474">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="bbbda-475">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="bbbda-476">Course 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-476">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="bbbda-478">使用以下代码创建 Models/Course.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="bbbda-479">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="bbbda-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="bbbda-480">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="bbbda-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="bbbda-481">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="bbbda-482">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="bbbda-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="bbbda-483">搭建“学生”页的基架</span><span class="sxs-lookup"><span data-stu-id="bbbda-483">Scaffold Student pages</span></span>

<span data-ttu-id="bbbda-484">本部分使用 ASP.NET Core 基架工具生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="bbbda-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="bbbda-485">EF Core 上下文类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-485">An EF Core *context* class.</span></span> <span data-ttu-id="bbbda-486">上下文是为给定数据模型协调实体框架功能的主类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="bbbda-487">它派生自 `Microsoft.EntityFrameworkCore.DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="bbbda-488">:::no-loc(Razor)::: 页面，可处理 `Student` 实体的创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-488">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-490">在“Pages”文件夹中创建“Students”文件夹 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="bbbda-491">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹，然后选择“添加”>“新搭建基架的项目” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="bbbda-492">在“添加基架”对话框中，依次选择“使用实体框架的 :::no-loc(Razor)::: Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-492">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="bbbda-493">在“添加使用实体框架的 :::no-loc(Razor)::: Pages (CRUD)”对话框中：</span><span class="sxs-lookup"><span data-stu-id="bbbda-493">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="bbbda-494">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="bbbda-495">在“数据上下文类”行中，选择 +（加号） 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="bbbda-496">将数据上下文名称从 ContosoUniversity.Models.ContosoUniversityContext 更改为 ContosoUniversity.Data.SchoolContext 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="bbbda-497">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="bbbda-497">Select **Add**.</span></span>

<span data-ttu-id="bbbda-498">自动安装以下包：</span><span class="sxs-lookup"><span data-stu-id="bbbda-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bbbda-500">运行以下 .NET Core CLI 命令，安装所需的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="bbbda-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="bbbda-501">基架需要 Microsoft.VisualStudio.Web.CodeGeneration.Design 包。</span><span class="sxs-lookup"><span data-stu-id="bbbda-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="bbbda-502">虽然应用不使用 SQL Server，但基架工具需要 SQL Server 包。</span><span class="sxs-lookup"><span data-stu-id="bbbda-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="bbbda-503">创建“Pages/Students”文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="bbbda-504">运行以下命令安装 [aspnet-codegenerator 基架工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="bbbda-505">运行以下命令，搭建“学生”页的基架。</span><span class="sxs-lookup"><span data-stu-id="bbbda-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="bbbda-506">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="bbbda-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="bbbda-507">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="bbbda-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="bbbda-508">如果对上述步骤有疑问，请生成项目并重试基架搭建步骤。</span><span class="sxs-lookup"><span data-stu-id="bbbda-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="bbbda-509">基架流程：</span><span class="sxs-lookup"><span data-stu-id="bbbda-509">The scaffolding process:</span></span>

* <span data-ttu-id="bbbda-510">在“Pages/Students”文件夹中创建 :::no-loc(Razor)::: 页面：</span><span class="sxs-lookup"><span data-stu-id="bbbda-510">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="bbbda-511">Create.cshtml 和 Create.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="bbbda-512">Delete.cshtml 和 Delete.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="bbbda-513">Details.cshtml 和 Details.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="bbbda-514">Edit.cshtml 和 Edit.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="bbbda-515">Index.cshtml 和 Index.cshtml.cs </span><span class="sxs-lookup"><span data-stu-id="bbbda-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="bbbda-516">创建 Data/SchoolContext.cs。</span><span class="sxs-lookup"><span data-stu-id="bbbda-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="bbbda-517">将上下文添加到 Startup.cs 中的依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="bbbda-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="bbbda-518">将数据库连接字符串添加到 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-518">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="bbbda-519">数据库连接字符串</span><span class="sxs-lookup"><span data-stu-id="bbbda-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bbbda-521">*:::no-loc(appsettings.json):::* 文件指定了连接字符串 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-521">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="bbbda-522">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="bbbda-523">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 文件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bbbda-525">更改连接字符串以指向名为 CU.db 的 SQLite 数据库文件：</span><span class="sxs-lookup"><span data-stu-id="bbbda-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="bbbda-526">更新数据库上下文类</span><span class="sxs-lookup"><span data-stu-id="bbbda-526">Update the database context class</span></span>

<span data-ttu-id="bbbda-527">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="bbbda-528">上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="bbbda-529">上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="bbbda-530">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="bbbda-531">使用以下代码更新 Data/SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="bbbda-532">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="bbbda-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="bbbda-533">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="bbbda-533">In EF Core terminology:</span></span>

* <span data-ttu-id="bbbda-534">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="bbbda-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="bbbda-535">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="bbbda-536">由于实体集包含多个实体，因此 DBSet 属性应为复数名称。</span><span class="sxs-lookup"><span data-stu-id="bbbda-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="bbbda-537">由于基架工具创建了 `Student` DBSet，因此此步骤将其更改为复数 `Students`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="bbbda-538">为了使 :::no-loc(Razor)::: Pages 代码与新的 DBSet 名称相匹配，请在整个项目中进行全局更改，将 `_context.Student` 更改为 `_context.Students`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-538">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="bbbda-539">更改发生 8 次。</span><span class="sxs-lookup"><span data-stu-id="bbbda-539">There are 8 occurrences.</span></span>

<span data-ttu-id="bbbda-540">生成项目以验证没有任何编译器错误。</span><span class="sxs-lookup"><span data-stu-id="bbbda-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="bbbda-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="bbbda-541">Startup.cs</span></span>

<span data-ttu-id="bbbda-542">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bbbda-543">在应用程序启动过程通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="bbbda-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="bbbda-544">需要这些服务（如 :::no-loc(Razor)::: 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="bbbda-544">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="bbbda-545">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="bbbda-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="bbbda-546">基架工具自动将上下文类注册到了依赖项注入容器。</span><span class="sxs-lookup"><span data-stu-id="bbbda-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-548">在 `ConfigureServices` 中，基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="bbbda-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bbbda-550">在 `ConfigureServices` 中，确保基架所添加的代码调用 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="bbbda-551">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="bbbda-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="bbbda-552">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *:::no-loc(appsettings.json):::* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="bbbda-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="bbbda-553">创建数据库</span><span class="sxs-lookup"><span data-stu-id="bbbda-553">Create the database</span></span>

<span data-ttu-id="bbbda-554">如果没有数据库，请更新 Program.cs 以创建数据库：</span><span class="sxs-lookup"><span data-stu-id="bbbda-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="bbbda-555">如果有上下文的数据库，则 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="bbbda-556">如果没有数据库，则它将创建数据库和架构。</span><span class="sxs-lookup"><span data-stu-id="bbbda-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="bbbda-557">`EnsureCreated` 启用以下工作流来处理数据模型更改：</span><span class="sxs-lookup"><span data-stu-id="bbbda-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="bbbda-558">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-558">Delete the database.</span></span> <span data-ttu-id="bbbda-559">任何现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="bbbda-559">Any existing data is lost.</span></span>
* <span data-ttu-id="bbbda-560">更改数据模型。</span><span class="sxs-lookup"><span data-stu-id="bbbda-560">Change the data model.</span></span> <span data-ttu-id="bbbda-561">例如，添加 `EmailAddress` 字段。</span><span class="sxs-lookup"><span data-stu-id="bbbda-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="bbbda-562">运行应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-562">Run the app.</span></span>
* <span data-ttu-id="bbbda-563">`EnsureCreated` 创建具有新架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="bbbda-564">在无需保存数据的情况下，当架构快速发展时，此工作流在早期开发过程中表现良好。</span><span class="sxs-lookup"><span data-stu-id="bbbda-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="bbbda-565">如果需要保存已输入数据库的数据，情况就有所不同了。</span><span class="sxs-lookup"><span data-stu-id="bbbda-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="bbbda-566">如果是这种情况，请使用迁移。</span><span class="sxs-lookup"><span data-stu-id="bbbda-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="bbbda-567">本系列教程的后续部分将删除 `EnsureCreated` 创建的数据库，转而使用迁移。</span><span class="sxs-lookup"><span data-stu-id="bbbda-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="bbbda-568">无法使用迁移更新 `EnsureCreated` 创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="bbbda-569">测试应用</span><span class="sxs-lookup"><span data-stu-id="bbbda-569">Test the app</span></span>

* <span data-ttu-id="bbbda-570">运行应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-570">Run the app.</span></span>
* <span data-ttu-id="bbbda-571">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="bbbda-572">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="bbbda-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="bbbda-573">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="bbbda-573">Seed the database</span></span>

<span data-ttu-id="bbbda-574">`EnsureCreated` 方法将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="bbbda-575">本节添加用测试数据填充数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="bbbda-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="bbbda-576">使用以下代码创建 Data/DbInitializer.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="bbbda-577">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="bbbda-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="bbbda-578">如果不存在学生，它将向数据库添加测试数据。</span><span class="sxs-lookup"><span data-stu-id="bbbda-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="bbbda-579">该代码使用数组创建测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="bbbda-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="bbbda-580">在 Program.cs 中，将 `EnsureCreated` 调用替换为 `DbInitializer.Initialize` 调用：</span><span class="sxs-lookup"><span data-stu-id="bbbda-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bbbda-582">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bbbda-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bbbda-584">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="bbbda-585">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-585">Restart the app.</span></span>

* <span data-ttu-id="bbbda-586">选择“学生”页查看已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="bbbda-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="bbbda-587">查看数据库</span><span class="sxs-lookup"><span data-stu-id="bbbda-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-589">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="bbbda-590">在 SSOX 中，依次选择“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="bbbda-591">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="bbbda-592">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="bbbda-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="bbbda-593">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="bbbda-594">右键单击 Student 表，然后单击“查看代码”查看 `Student` 模型如何映射到 `Student` 表架构 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bbbda-596">使用 SQLite 工具查看数据库架构和已设定种子的数据。</span><span class="sxs-lookup"><span data-stu-id="bbbda-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="bbbda-597">数据库文件名为 CU.db，位于项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="bbbda-598">异步代码</span><span class="sxs-lookup"><span data-stu-id="bbbda-598">Asynchronous code</span></span>

<span data-ttu-id="bbbda-599">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="bbbda-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="bbbda-600">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="bbbda-601">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="bbbda-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="bbbda-602">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="bbbda-603">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="bbbda-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="bbbda-604">因此，使用异步代码可以更有效地利用服务器资源，并且服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="bbbda-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="bbbda-605">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="bbbda-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="bbbda-606">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="bbbda-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="bbbda-607">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="bbbda-608">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bbbda-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="bbbda-609">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="bbbda-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="bbbda-610">创建返回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 对象。</span><span class="sxs-lookup"><span data-stu-id="bbbda-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="bbbda-611">返回类型 `Task<T>` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="bbbda-612">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="bbbda-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="bbbda-613">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="bbbda-614">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="bbbda-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="bbbda-615">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="bbbda-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="bbbda-616">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="bbbda-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="bbbda-617">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="bbbda-618">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="bbbda-619">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="bbbda-620">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="bbbda-621">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="bbbda-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="bbbda-622">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="bbbda-623">后续步骤</span><span class="sxs-lookup"><span data-stu-id="bbbda-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="bbbda-624">下一教程</span><span class="sxs-lookup"><span data-stu-id="bbbda-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bbbda-625">Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 创建 ASP.NET Core :::no-loc(Razor)::: Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="bbbda-626">该示例应用是一个虚构的 Contoso University 的网站。</span><span class="sxs-lookup"><span data-stu-id="bbbda-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="bbbda-627">其中包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="bbbda-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="bbbda-628">本页是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。</span><span class="sxs-lookup"><span data-stu-id="bbbda-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="bbbda-629">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="bbbda-630">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bbbda-631">先决条件</span><span class="sxs-lookup"><span data-stu-id="bbbda-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="bbbda-634">熟悉 [:::no-loc(Razor)::: Pages](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-634">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="bbbda-635">新程序员在开始学习本系列之前，应先完成 [:::no-loc(Razor)::: Pages 入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-635">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bbbda-636">疑难解答</span><span class="sxs-lookup"><span data-stu-id="bbbda-636">Troubleshooting</span></span>

<span data-ttu-id="bbbda-637">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="bbbda-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="bbbda-638">获取帮助的一个好方法是将问题发布到适用于 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 的 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="bbbda-639">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="bbbda-639">The Contoso University web app</span></span>

<span data-ttu-id="bbbda-640">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="bbbda-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="bbbda-641">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="bbbda-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="bbbda-642">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="bbbda-642">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

<span data-ttu-id="bbbda-645">此网站的 UI 样式与内置模板生成的 UI 样式类似。</span><span class="sxs-lookup"><span data-stu-id="bbbda-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="bbbda-646">教程的重点是 EF Core 和 :::no-loc(Razor)::: Pages，而非 UI。</span><span class="sxs-lookup"><span data-stu-id="bbbda-646">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="bbbda-647">创建 ContosoUniversity :::no-loc(Razor)::: Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="bbbda-647">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-649">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bbbda-650">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="bbbda-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="bbbda-651">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="bbbda-652">务必将该项目命名为 ContosoUniversity，以便复制/粘贴代码时命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="bbbda-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="bbbda-653">在下拉列表中选择“ASP.NET Core 2.1”，然后选择“Web 应用程序” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="bbbda-654">有关上述步骤的图像，请参阅[创建 :::no-loc(Razor)::: Web 应用](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-654">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="bbbda-655">运行应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="bbbda-657">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="bbbda-657">Set up the site style</span></span>

<span data-ttu-id="bbbda-658">设置网站菜单、布局和主页时需作少量更改。</span><span class="sxs-lookup"><span data-stu-id="bbbda-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="bbbda-659">进行以下更改以更新 Pages/Shared/_Layout.cshtml：</span><span class="sxs-lookup"><span data-stu-id="bbbda-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="bbbda-660">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="bbbda-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="bbbda-661">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="bbbda-661">There are three occurrences.</span></span>

* <span data-ttu-id="bbbda-662">添加菜单项 **Students** ， **Courses** ， **Instructors** ，和 **Department** ，并删除 **Contact** 菜单项。</span><span class="sxs-lookup"><span data-stu-id="bbbda-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="bbbda-663">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="bbbda-663">The changes are highlighted.</span></span> <span data-ttu-id="bbbda-664">（没有显示全部标记。）</span><span class="sxs-lookup"><span data-stu-id="bbbda-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="bbbda-665">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET 和 MVC 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="bbbda-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="bbbda-666">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="bbbda-666">Create the data model</span></span>

<span data-ttu-id="bbbda-667">创建 Contoso University 应用的实体类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="bbbda-668">从以下三个实体开始：</span><span class="sxs-lookup"><span data-stu-id="bbbda-668">Start with the following three entities:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="bbbda-670">`Student` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="bbbda-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="bbbda-671">`Course` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="bbbda-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="bbbda-672">一名学生可以报名参加任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="bbbda-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="bbbda-673">一门课程中可以包含任意数量的学生。</span><span class="sxs-lookup"><span data-stu-id="bbbda-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="bbbda-674">以下部分将为这几个实体中的每一个实体创建一个类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="bbbda-675">Student 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-675">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="bbbda-677">创建 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-677">Create a *Models* folder.</span></span> <span data-ttu-id="bbbda-678">在 Models 文件夹中，使用以下代码创建一个名为 Student.cs 的类文件 ：</span><span class="sxs-lookup"><span data-stu-id="bbbda-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="bbbda-679">`ID` 属性成为此类对应的数据库 (DB) 表的主键列。</span><span class="sxs-lookup"><span data-stu-id="bbbda-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="bbbda-680">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="bbbda-681">在 `classnameID` 中，`classname` 为类名称。</span><span class="sxs-lookup"><span data-stu-id="bbbda-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="bbbda-682">另一种自动识别的主键是上例中的 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="bbbda-683">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="bbbda-684">导航属性链接到与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="bbbda-685">在这种情况下，`Student entity` 的 `Enrollments` 属性包含与该 `Student` 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="bbbda-686">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="bbbda-687">相关的 `Enrollment` 行是 `StudentID` 列中包含该学生的主键值的行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="bbbda-688">例如，假设 ID=1 的学生在 `Enrollment` 表中有两行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="bbbda-689">`Enrollment` 表中有两行的 `StudentID` = 1。</span><span class="sxs-lookup"><span data-stu-id="bbbda-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="bbbda-690">`StudentID` 是 `Enrollment` 表中的外键，用于指定 `Student` 表中的学生。</span><span class="sxs-lookup"><span data-stu-id="bbbda-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="bbbda-691">如果导航属性包含多个实体，则导航属性必须是列表类型，例如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="bbbda-692">可以指定 `ICollection<T>` 或诸如 `List<T>` 或 `HashSet<T>` 的类型。</span><span class="sxs-lookup"><span data-stu-id="bbbda-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="bbbda-693">使用 `ICollection<T>` 时，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="bbbda-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="bbbda-694">包含多个实体的导航属性来自于多对多和一对多关系。</span><span class="sxs-lookup"><span data-stu-id="bbbda-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="bbbda-695">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-695">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="bbbda-697">在 Models 文件夹中，使用以下代码创建 Enrollment.cs ：</span><span class="sxs-lookup"><span data-stu-id="bbbda-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="bbbda-698">`EnrollmentID` 属性为主键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="bbbda-699">`Student` 实体使用的是 `ID` 模式，而本实体使用的是 `classnameID` 模式。</span><span class="sxs-lookup"><span data-stu-id="bbbda-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="bbbda-700">通常情况下，开发者会选择一种模式并在整个数据模型中都使用该模式。</span><span class="sxs-lookup"><span data-stu-id="bbbda-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="bbbda-701">下一个教程将介绍如何使用不带类名的 ID，以便更轻松地在数据模型中实现集成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="bbbda-702">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="bbbda-703">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="bbbda-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="bbbda-704">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="bbbda-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="bbbda-705">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="bbbda-706">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="bbbda-707">`Student` 实体与 `Student.Enrollments` 导航属性不同，后者包含多个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="bbbda-708">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="bbbda-709">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="bbbda-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="bbbda-710">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="bbbda-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="bbbda-711">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="bbbda-712">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="bbbda-713">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="bbbda-714">Course 实体</span><span class="sxs-lookup"><span data-stu-id="bbbda-714">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="bbbda-716">在 Models 文件夹中，使用以下代码创建 Course.cs ：</span><span class="sxs-lookup"><span data-stu-id="bbbda-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="bbbda-717">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="bbbda-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="bbbda-718">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="bbbda-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="bbbda-719">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="bbbda-720">为“学生”模型搭建基架</span><span class="sxs-lookup"><span data-stu-id="bbbda-720">Scaffold the student model</span></span>

<span data-ttu-id="bbbda-721">本部分将为“学生”模型搭建基架。</span><span class="sxs-lookup"><span data-stu-id="bbbda-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="bbbda-722">确切地说，基架工具将生成页面，用于对“学生”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="bbbda-723">生成项目。</span><span class="sxs-lookup"><span data-stu-id="bbbda-723">Build the project.</span></span>
* <span data-ttu-id="bbbda-724">创建 Pages/Students 文件夹。</span><span class="sxs-lookup"><span data-stu-id="bbbda-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bbbda-726">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="bbbda-727">在“添加基架”对话框中，依次选择“使用实体框架的 :::no-loc(Razor)::: Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-727">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="bbbda-728">完成“添加使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="bbbda-728">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="bbbda-729">在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="bbbda-730">在“数据上下文类”行中，选择加号 (+) 并将生成的名称更改为 ContosoUniversity.Models.SchoolContext  。</span><span class="sxs-lookup"><span data-stu-id="bbbda-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="bbbda-731">在“数据上下文类”下拉列表中，选择“ContosoUniversity.Models.SchoolContext” </span><span class="sxs-lookup"><span data-stu-id="bbbda-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="bbbda-732">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="bbbda-732">Select **Add**.</span></span>

![CRUD 对话框](intro/_static/s1.png)

<span data-ttu-id="bbbda-734">如果对前面的步骤有疑问，请参阅[搭建“电影”模型的基架](xref:tutorials/razor-pages/model#scaffold-the-movie-model)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bbbda-736">运行以下命令，搭建“学生”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="bbbda-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="bbbda-737">搭建基架的过程会创建并更改以下文件：</span><span class="sxs-lookup"><span data-stu-id="bbbda-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="bbbda-738">创建的文件</span><span class="sxs-lookup"><span data-stu-id="bbbda-738">Files created</span></span>

* <span data-ttu-id="bbbda-739">Pages/Students：“创建”、“删除”、“详细信息”、“编辑”、“索引”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="bbbda-740">Data/SchoolContext.cs</span><span class="sxs-lookup"><span data-stu-id="bbbda-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="bbbda-741">更新的文件</span><span class="sxs-lookup"><span data-stu-id="bbbda-741">File updates</span></span>

* <span data-ttu-id="bbbda-742">*Startup.cs* ：下一部分详细介绍对此文件所作的更改。</span><span class="sxs-lookup"><span data-stu-id="bbbda-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="bbbda-743">*:::no-loc(appsettings.json):::* ：添加用于连接到本地数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="bbbda-743">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="bbbda-744">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="bbbda-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="bbbda-745">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bbbda-746">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="bbbda-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="bbbda-747">需要这些服务（如 :::no-loc(Razor)::: 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="bbbda-747">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="bbbda-748">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="bbbda-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="bbbda-749">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="bbbda-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="bbbda-750">在 Startup.cs 中检查 `ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="bbbda-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="bbbda-751">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="bbbda-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="bbbda-752">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="bbbda-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="bbbda-753">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *:::no-loc(appsettings.json):::* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="bbbda-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="bbbda-754">更新 main</span><span class="sxs-lookup"><span data-stu-id="bbbda-754">Update main</span></span>

<span data-ttu-id="bbbda-755">在 Program.cs 中，修改 `Main` 方法以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bbbda-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="bbbda-756">从依赖关系注入容器获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="bbbda-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="bbbda-757">调用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="bbbda-758">`EnsureCreated` 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="bbbda-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="bbbda-759">下面的代码显示更新后的 Program.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="bbbda-760">`EnsureCreated` 确保存在上下文数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="bbbda-761">如果存在，则不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-761">If it exists, no action is taken.</span></span> <span data-ttu-id="bbbda-762">如果不存在，则会创建数据库及其所有架构。</span><span class="sxs-lookup"><span data-stu-id="bbbda-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="bbbda-763">`EnsureCreated` 不使用迁移创建数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="bbbda-764">使用 `EnsureCreated` 创建的数据库稍后无法使用迁移更新。</span><span class="sxs-lookup"><span data-stu-id="bbbda-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="bbbda-765">启动应用时会调用 `EnsureCreated`，以进行以下工作流：</span><span class="sxs-lookup"><span data-stu-id="bbbda-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="bbbda-766">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-766">Delete the DB.</span></span>
* <span data-ttu-id="bbbda-767">更改数据库架构（例如添加一个 `EmailAddress` 字段）。</span><span class="sxs-lookup"><span data-stu-id="bbbda-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="bbbda-768">运行应用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-768">Run the app.</span></span>
* <span data-ttu-id="bbbda-769">`EnsureCreated` 创建一个带有 `EmailAddress` 列的数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="bbbda-770">架构快速演变时，在开发初期使用 `EnsureCreated` 很方便。</span><span class="sxs-lookup"><span data-stu-id="bbbda-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="bbbda-771">本教程后面将删除 DB 并使用迁移。</span><span class="sxs-lookup"><span data-stu-id="bbbda-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="bbbda-772">测试应用</span><span class="sxs-lookup"><span data-stu-id="bbbda-772">Test the app</span></span>

<span data-ttu-id="bbbda-773">运行应用并接受 :::no-loc(cookie)::: 策略。</span><span class="sxs-lookup"><span data-stu-id="bbbda-773">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="bbbda-774">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="bbbda-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="bbbda-775">有关 :::no-loc(cookie)::: 策略的信息，请参阅[欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-775">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="bbbda-776">依次选择“学生”链接、“新建” 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="bbbda-777">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="bbbda-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="bbbda-778">检查 SchoolContext DB 上下文</span><span class="sxs-lookup"><span data-stu-id="bbbda-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="bbbda-779">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="bbbda-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="bbbda-780">数据上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="bbbda-781">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="bbbda-782">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="bbbda-783">使用以下代码更新 SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="bbbda-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="bbbda-784">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="bbbda-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="bbbda-785">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="bbbda-785">In EF Core terminology:</span></span>

* <span data-ttu-id="bbbda-786">实体集通常对应一个数据库表。</span><span class="sxs-lookup"><span data-stu-id="bbbda-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="bbbda-787">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="bbbda-788">可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="bbbda-789">EF Core 隐式包含了它们，因为 `Student` 实体引用 `Enrollment` 实体，而 `Enrollment` 实体引用 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="bbbda-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="bbbda-790">在本教程中，将 `DbSet<Enrollment>` 和 `DbSet<Course>` 保留在 `SchoolContext` 中。</span><span class="sxs-lookup"><span data-stu-id="bbbda-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="bbbda-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="bbbda-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="bbbda-792">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="bbbda-793">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="bbbda-794">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="bbbda-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="bbbda-795">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="bbbda-796">添加代码，以使用测试数据初始化该数据库</span><span class="sxs-lookup"><span data-stu-id="bbbda-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="bbbda-797">EF Core 会创建一个空的数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="bbbda-798">本部分中编写了 `Initialize` 方法来使用测试数据填充该数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="bbbda-799">在 Data 文件夹中，新建一个名为 DbInitializer.cs 的类文件，并添加以下代码 ：</span><span class="sxs-lookup"><span data-stu-id="bbbda-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="bbbda-800">注意：上面的代码对命名空间使用 `Models` (`namespace ContosoUniversity.Models`)，而不是 `Data`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="bbbda-801">`Models` 与基架生成的代码一致。</span><span class="sxs-lookup"><span data-stu-id="bbbda-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="bbbda-802">有关详细信息，请参阅[此 GitHub 基架问题](https://github.com/aspnet/Scaffolding/issues/822)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="bbbda-803">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="bbbda-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="bbbda-804">如果 DB 中没有任何学生，则会使用测试数据初始化该 DB。</span><span class="sxs-lookup"><span data-stu-id="bbbda-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="bbbda-805">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="bbbda-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="bbbda-806">`EnsureCreated` 方法自动为数据库上下文创建数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="bbbda-807">如果数据库已存在，则返回 `EnsureCreated`，并且不修改数据库。</span><span class="sxs-lookup"><span data-stu-id="bbbda-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="bbbda-808">在 Program.cs 中，将 `Main` 方法修改为调用 `Initialize`：</span><span class="sxs-lookup"><span data-stu-id="bbbda-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="bbbda-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bbbda-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bbbda-810">如果应用正在运行，则停止应用，然后在包管理器控制台 (PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bbbda-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="bbbda-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bbbda-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bbbda-812">如果应用正在运行，则停止应用，然后删除 CU.db 文件。</span><span class="sxs-lookup"><span data-stu-id="bbbda-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="bbbda-813">查看数据库</span><span class="sxs-lookup"><span data-stu-id="bbbda-813">View the DB</span></span>

<span data-ttu-id="bbbda-814">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="bbbda-815">因此，数据库名称为“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="bbbda-816">GUID 因用户而异。</span><span class="sxs-lookup"><span data-stu-id="bbbda-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="bbbda-817">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX) 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="bbbda-818">在 SSOX 中，依次单击“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="bbbda-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="bbbda-819">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="bbbda-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="bbbda-820">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行 。</span><span class="sxs-lookup"><span data-stu-id="bbbda-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="bbbda-821">异步代码</span><span class="sxs-lookup"><span data-stu-id="bbbda-821">Asynchronous code</span></span>

<span data-ttu-id="bbbda-822">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="bbbda-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="bbbda-823">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="bbbda-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="bbbda-824">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="bbbda-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="bbbda-825">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="bbbda-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="bbbda-826">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="bbbda-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="bbbda-827">因此，使用异步代码可以更有效地利用服务器资源，并且可以让服务器在没有延迟的情况下处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="bbbda-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="bbbda-828">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="bbbda-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="bbbda-829">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="bbbda-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="bbbda-830">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="bbbda-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="bbbda-831">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bbbda-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="bbbda-832">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="bbbda-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="bbbda-833">自动创建返回的 [Task](/dotnet/api/system.threading.tasks.task) 对象。</span><span class="sxs-lookup"><span data-stu-id="bbbda-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="bbbda-834">有关详细信息，请参阅[任务返回类型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="bbbda-835">隐式返回类型 `Task` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="bbbda-836">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="bbbda-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="bbbda-837">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="bbbda-838">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="bbbda-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="bbbda-839">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="bbbda-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="bbbda-840">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="bbbda-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="bbbda-841">只会异步执行导致查询或命令被发送到数据库的语句。</span><span class="sxs-lookup"><span data-stu-id="bbbda-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="bbbda-842">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="bbbda-843">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="bbbda-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="bbbda-844">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="bbbda-845">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="bbbda-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="bbbda-846">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="bbbda-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="bbbda-847">下一个教程将介绍基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="bbbda-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="bbbda-848">其他资源</span><span class="sxs-lookup"><span data-stu-id="bbbda-848">Additional resources</span></span>

* [<span data-ttu-id="bbbda-849">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="bbbda-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="bbbda-850">下一页</span><span class="sxs-lookup"><span data-stu-id="bbbda-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
