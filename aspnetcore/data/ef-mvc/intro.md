---
title: 教程：在 ASP.NET MVC Web 应用中使用 EF Core 入门
description: 本页是介绍如何构建“Contoso University 示例 EF/MVC 应用”系列教程中的第一部分。
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 77cf1e9ad51b7044a35e1a9b2c125b0fdd91435e
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365365"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="9d326-103">教程：在 ASP.NET MVC Web 应用中使用 EF Core 入门</span><span class="sxs-lookup"><span data-stu-id="9d326-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="9d326-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9d326-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="9d326-105">Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 和 Visual Studio 创建 ASP.NET Core MVC Web 应用。</span><span class="sxs-lookup"><span data-stu-id="9d326-105">The Contoso University sample web ap demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="9d326-106">该示例应用是一个虚构的 Contoso University 的网站。</span><span class="sxs-lookup"><span data-stu-id="9d326-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="9d326-107">其中包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="9d326-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="9d326-108">这是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。</span><span class="sxs-lookup"><span data-stu-id="9d326-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9d326-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="9d326-109">Prerequisites</span></span>

* <span data-ttu-id="9d326-110">如果不熟悉 ASP.NET Core MVC，则在开始前，请浏览 [ASP.NET Core MVC 入门](xref:tutorials/first-mvc-app/start-mvc)教程。</span><span class="sxs-lookup"><span data-stu-id="9d326-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="9d326-111">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="9d326-111">Database engines</span></span>

<span data-ttu-id="9d326-112">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="9d326-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="9d326-113">解决问题和进行故障排除</span><span class="sxs-lookup"><span data-stu-id="9d326-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="9d326-114">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="9d326-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="9d326-115">常见错误以及对应的解决方案，请参阅 [最新教程中的故障排除](advanced.md#common-errors)。</span><span class="sxs-lookup"><span data-stu-id="9d326-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="9d326-116">如果没有找到遇到的问题的解决方案，可以将问题发布到StackOverflow.com 的 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 版块。</span><span class="sxs-lookup"><span data-stu-id="9d326-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="9d326-117">这是一系列一共有十个教程，其中每个都是在前面教程已完成的基础上继续。</span><span class="sxs-lookup"><span data-stu-id="9d326-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="9d326-118">请考虑在完成每一个教程后保存项目的副本。</span><span class="sxs-lookup"><span data-stu-id="9d326-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="9d326-119">之后如果遇到问题，你可以从保存的副本中开始寻找问题，而不是从头开始。</span><span class="sxs-lookup"><span data-stu-id="9d326-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="9d326-120">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="9d326-120">Contoso University web app</span></span>

<span data-ttu-id="9d326-121">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="9d326-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="9d326-122">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="9d326-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="9d326-123">以下是该应用中的几个屏幕：</span><span class="sxs-lookup"><span data-stu-id="9d326-123">Here are a few of the screens in the app:</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="9d326-126">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="9d326-126">Create web app</span></span>

* <span data-ttu-id="9d326-127">打开 Visual Studio 并选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="9d326-127">Start Visual Studio and select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="9d326-128">将项目命名为 `ContosoUniversity`。</span><span class="sxs-lookup"><span data-stu-id="9d326-128">Name the project `ContosoUniversity`.</span></span> <span data-ttu-id="9d326-129">请务必使用此名称（含大写），确保在复制代码时与命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="9d326-129">It's important to use this exact name including capitalization, so the namespaces match when code is copied.</span></span>
* <span data-ttu-id="9d326-130">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="9d326-130">Select **Create**.</span></span>
* <span data-ttu-id="9d326-131">在下拉列表中选择“.NET Core”和“ASP.NET Core 5.0”，然后选择“Web 应用程序(Model-View-Controller)”模板。</span><span class="sxs-lookup"><span data-stu-id="9d326-131">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application (Model-View-Controller)** template.</span></span>
  <span data-ttu-id="9d326-132">![新的 ASP.NET Core 项目对话框](intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="9d326-132">![New ASP.NET Core Project dialog](intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="9d326-133">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="9d326-133">Set up the site style</span></span>

<span data-ttu-id="9d326-134">设置网站菜单、布局和主页时需作少量基本更改。</span><span class="sxs-lookup"><span data-stu-id="9d326-134">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="9d326-135">打开 Views/Shared/_Layout.cshtml 并进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="9d326-135">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="9d326-136">将每次出现的 `ContosoUniversity` 更改为 `Contoso University`。</span><span class="sxs-lookup"><span data-stu-id="9d326-136">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="9d326-137">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="9d326-137">There are three occurrences.</span></span>
* <span data-ttu-id="9d326-138">添加“关于”、“学生”、“课程”“讲师”和“院系”的菜单项，并删除“隐私”菜单项。</span><span class="sxs-lookup"><span data-stu-id="9d326-138">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="9d326-139">前面的更改在以下代码中突出显示：</span><span class="sxs-lookup"><span data-stu-id="9d326-139">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="9d326-140">在“Views/Home/Index.cshtml”中，将文件内容替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="9d326-140">In *Views/Home/Index.cshtml* , replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="9d326-141">按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-141">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="9d326-142">主页将显示与本教程中创建的页面对应的选项卡。</span><span class="sxs-lookup"><span data-stu-id="9d326-142">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Contoso University 主页](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="9d326-144">EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="9d326-144">EF Core NuGet packages</span></span>

<span data-ttu-id="9d326-145">本教程使用 SQL Server，相关驱动包[Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="9d326-145">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="9d326-146">EF SQL Server 包与其依赖项 `Microsoft.EntityFrameworkCore` 和 `Microsoft.EntityFrameworkCore.Relational` 一起提供 EF 的运行时支持。</span><span class="sxs-lookup"><span data-stu-id="9d326-146">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="9d326-147">添加 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包和 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9d326-147">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="9d326-148">在程序管理器控制台 (PMC) 中输入以下命令以添加 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="9d326-148">In the Program Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

<span data-ttu-id="9d326-149">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 包提供 EF Core 错误页的 ASP.NET Core 中间件。</span><span class="sxs-lookup"><span data-stu-id="9d326-149">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="9d326-150">此中间件有助于检测和诊断 EF Core 迁移错误。</span><span class="sxs-lookup"><span data-stu-id="9d326-150">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="9d326-151">有关其他可用于 EF Core 的数据库提供程序，请参阅[数据库提供程序](/ef/core/providers/)。</span><span class="sxs-lookup"><span data-stu-id="9d326-151">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="9d326-152">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="9d326-152">Create the data model</span></span>

<span data-ttu-id="9d326-153">将为此应用创建以下实体类：</span><span class="sxs-lookup"><span data-stu-id="9d326-153">The following entity classes are created for this app:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="9d326-155">前面的实体具有以下关系：</span><span class="sxs-lookup"><span data-stu-id="9d326-155">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="9d326-156">`Student` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="9d326-156">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="9d326-157">一名学生可以报名参加任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="9d326-157">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="9d326-158">`Course` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="9d326-158">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="9d326-159">一门课程中可以包含任意数量的学生。</span><span class="sxs-lookup"><span data-stu-id="9d326-159">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="9d326-160">以下部分将为这几个实体中的每一个实体创建一个类。</span><span class="sxs-lookup"><span data-stu-id="9d326-160">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="9d326-161">Student 实体</span><span class="sxs-lookup"><span data-stu-id="9d326-161">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="9d326-163">在 Models 文件夹中，使用以下代码创建 `Student` 类：</span><span class="sxs-lookup"><span data-stu-id="9d326-163">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-164">`ID` 属性是对应于此类的数据库表的主键 (PK) 列。</span><span class="sxs-lookup"><span data-stu-id="9d326-164">The `ID` property is the primary key ( **PK** ) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="9d326-165">默认情况下，EF 将名为 `ID` 或 `classnameID` 的属性解释为主键。</span><span class="sxs-lookup"><span data-stu-id="9d326-165">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="9d326-166">例如，可以将 PK 命名为 `StudentID` 而不是 `ID`。</span><span class="sxs-lookup"><span data-stu-id="9d326-166">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="9d326-167">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="9d326-167">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="9d326-168">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-168">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="9d326-169">`Student` 实体的 `Enrollments` 属性：</span><span class="sxs-lookup"><span data-stu-id="9d326-169">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="9d326-170">包含与该 `Student` 实体相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-170">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="9d326-171">如果数据库中的特定 `Student` 行具有两个相关的 `Enrollment` 行：</span><span class="sxs-lookup"><span data-stu-id="9d326-171">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="9d326-172">`Student` 实体的 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-172">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="9d326-173">`Enrollment` 行在 `StudentID` 外键 (FK) 列中包含学生的 PK 值。</span><span class="sxs-lookup"><span data-stu-id="9d326-173">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key ( **FK** ) column.</span></span>

<span data-ttu-id="9d326-174">如果导航属性可以包含多个实体：</span><span class="sxs-lookup"><span data-stu-id="9d326-174">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="9d326-175">类型必须为列表，如 `ICollection<T>`、`List<T>` 或 `HashSet<T>`。</span><span class="sxs-lookup"><span data-stu-id="9d326-175">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="9d326-176">可以添加、删除和更新实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-176">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="9d326-177">多对多和一对多导航关系可包含多个实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-177">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="9d326-178">使用 `ICollection<T>` 时，EF 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="9d326-178">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="9d326-179">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="9d326-179">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="9d326-181">在 Models 文件夹中，使用以下代码创建 `Enrollment` 类：</span><span class="sxs-lookup"><span data-stu-id="9d326-181">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-182">`EnrollmentID` 属性为 PK。</span><span class="sxs-lookup"><span data-stu-id="9d326-182">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="9d326-183">该实体使用 `ID` 模式，而不是本身使用的 `classnameID` 模式。</span><span class="sxs-lookup"><span data-stu-id="9d326-183">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="9d326-184">`Student` 实体使用 `ID` 模式。</span><span class="sxs-lookup"><span data-stu-id="9d326-184">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="9d326-185">某些开发人员更喜欢在整个数据模型中使用一种模式。</span><span class="sxs-lookup"><span data-stu-id="9d326-185">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="9d326-186">在本教程中，这种变化说明了两种模式都可以使用。</span><span class="sxs-lookup"><span data-stu-id="9d326-186">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="9d326-187">[后面的教程](inheritance.md)介绍，使用不包含 classname 的 `ID` 可以更轻松地在数据模型中实现继承。</span><span class="sxs-lookup"><span data-stu-id="9d326-187">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="9d326-188">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="9d326-188">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="9d326-189">`Grade` 类型声明后的 `?` 表示 `Grade` 属性可以为 [null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)。</span><span class="sxs-lookup"><span data-stu-id="9d326-189">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="9d326-190">`null` 级别与零级别不同。</span><span class="sxs-lookup"><span data-stu-id="9d326-190">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="9d326-191">`null` 表示级别未知或尚未分配。</span><span class="sxs-lookup"><span data-stu-id="9d326-191">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="9d326-192">`StudentID` 属性是外键 (FK)，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="9d326-192">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="9d326-193">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只能包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-193">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="9d326-194">这与 `Student.Enrollments` 导航属性不同，后者可包含多个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-194">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="9d326-195">`CourseID` 属性是 FK，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="9d326-195">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="9d326-196">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="9d326-196">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="9d326-197">如果将属性命名为`<`导航属性名称`><`主键属性名称`>`，则 Entity Framework 将该属性解释为 FK 属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-197">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="9d326-198">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的 PK 为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="9d326-198">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="9d326-199">还可以将 FK 属性命名为 `<`主键属性名称`>`。</span><span class="sxs-lookup"><span data-stu-id="9d326-199">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="9d326-200">例如 `CourseID`，因为 `Course` 实体的 PK 为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="9d326-200">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="9d326-201">Course 实体</span><span class="sxs-lookup"><span data-stu-id="9d326-201">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="9d326-203">在 Models 文件夹中，使用以下代码创建 `Course` 类：</span><span class="sxs-lookup"><span data-stu-id="9d326-203">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-204">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="9d326-205">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="9d326-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="9d326-206">[DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) 属性将在[后面的教程部分](complex-data-model.md)中介绍。</span><span class="sxs-lookup"><span data-stu-id="9d326-206">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="9d326-207">该属性允许为课程输入 PK，而不是让数据库生成 PK。</span><span class="sxs-lookup"><span data-stu-id="9d326-207">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="9d326-208">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="9d326-208">Create the database context</span></span>

<span data-ttu-id="9d326-209"><xref:Microsoft.EntityFrameworkCore.DbContext> 数据库上下文类是为给定数据模型协调 EF 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="9d326-209">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="9d326-210">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="9d326-210">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="9d326-211">`DbContext` 派生类指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-211">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="9d326-212">某些 EF 行为可自定义。</span><span class="sxs-lookup"><span data-stu-id="9d326-212">Some EF behaviors can be customized.</span></span> <span data-ttu-id="9d326-213">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="9d326-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="9d326-214">在项目文件夹中，创建名为的文件夹 `Data`。</span><span class="sxs-lookup"><span data-stu-id="9d326-214">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="9d326-215">在 Data 文件夹中，使用以下代码创建 `SchoolContext` 类：</span><span class="sxs-lookup"><span data-stu-id="9d326-215">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-216">前面的代码为每个实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-216">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="9d326-217">在 EF 术语中：</span><span class="sxs-lookup"><span data-stu-id="9d326-217">In EF terminology:</span></span>

* <span data-ttu-id="9d326-218">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="9d326-218">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="9d326-219">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="9d326-219">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9d326-220">可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>` 语句，实现的功能没有任何改变。</span><span class="sxs-lookup"><span data-stu-id="9d326-220">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="9d326-221">EF 将隐式包含它们，原因如下：</span><span class="sxs-lookup"><span data-stu-id="9d326-221">EF would include them implicitly because:</span></span>

* <span data-ttu-id="9d326-222">`Student` 实体引用 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-222">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="9d326-223">`Enrollment` 实体引用 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-223">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="9d326-224">当数据库创建完成后， EF 创建一系列数据表，表名默认和 `DbSet` 属性名相同。</span><span class="sxs-lookup"><span data-stu-id="9d326-224">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="9d326-225">集合的属性名通常采用复数形式。</span><span class="sxs-lookup"><span data-stu-id="9d326-225">Property names for collections are typically plural.</span></span> <span data-ttu-id="9d326-226">例如，使用 `Students`，而不使用 `Student`。</span><span class="sxs-lookup"><span data-stu-id="9d326-226">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="9d326-227">开发者对表名称是否应为复数意见不一。</span><span class="sxs-lookup"><span data-stu-id="9d326-227">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="9d326-228">在这些教程中，在 `DbContext` 中指定单数形式的表名称会覆盖默认行为。</span><span class="sxs-lookup"><span data-stu-id="9d326-228">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="9d326-229">此教程在最后一个 DbSet 属性之后，添加以下高亮显示的代码。</span><span class="sxs-lookup"><span data-stu-id="9d326-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="9d326-230">注册 `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="9d326-230">Register the `SchoolContext`</span></span>

<span data-ttu-id="9d326-231">ASP.NET Core 包含[依赖关系注入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="9d326-231">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="9d326-232">在应用启动过程中通过依赖项注入来注册相关服务，例如 EF 数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="9d326-232">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="9d326-233">需要这些服务的组件（如 MVC 控制器）可以通过构造函数参数来获得这些服务。</span><span class="sxs-lookup"><span data-stu-id="9d326-233">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="9d326-234">本教程后面部分将展示获取上下文实例的控制器构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="9d326-234">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="9d326-235">若要将 `SchoolContext` 注册为一种服务，打开 *Startup.cs* ，并将高亮代码添加到 `ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="9d326-235">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="9d326-236">通过调用 `DbContextOptionsBuilder` 中的一个方法将数据库连接字符串在配置文件中的名称传递给上下文对象。</span><span class="sxs-lookup"><span data-stu-id="9d326-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="9d326-237">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *:::no-loc(appsettings.json):::* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="9d326-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="9d326-238">打开 *:::no-loc(appsettings.json):::* 文件，并按以下标记所示添加连接字符串：</span><span class="sxs-lookup"><span data-stu-id="9d326-238">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="9d326-239">添加数据库异常筛选器</span><span class="sxs-lookup"><span data-stu-id="9d326-239">Add the database exception filter</span></span>

<span data-ttu-id="9d326-240">将 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> 添加到 `ConfigureServices`，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="9d326-240">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

<span data-ttu-id="9d326-241">`AddDatabaseDeveloperPageExceptionFilter` 在[开发环境](xref:fundamentals/environments)中提供有用的错误信息。</span><span class="sxs-lookup"><span data-stu-id="9d326-241">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="9d326-242">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="9d326-242">SQL Server Express LocalDB</span></span>

<span data-ttu-id="9d326-243">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="9d326-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="9d326-244">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="9d326-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="9d326-245">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="9d326-245">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="9d326-246">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="9d326-246">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="9d326-247">使用测试数据初始化数据库</span><span class="sxs-lookup"><span data-stu-id="9d326-247">Initialize DB with test data</span></span>

<span data-ttu-id="9d326-248">EF 将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-248">EF creates an empty database.</span></span> <span data-ttu-id="9d326-249">在本部分中，你将添加一个方法，该方法将在数据库创建后调用，以向数据库填充测试数据。</span><span class="sxs-lookup"><span data-stu-id="9d326-249">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="9d326-250">`EnsureCreated` 方法用于自动创建数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-250">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="9d326-251">在[后面的教程](migrations.md)中，你将了解如何处理模型更改，方法是使用 Code First 迁移来更改数据库架构，而不是删除和重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-251">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="9d326-252">在 Data 文件夹中，使用以下代码创建一个名为 `DbInitializer` 的新类：</span><span class="sxs-lookup"><span data-stu-id="9d326-252">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="9d326-253">前面的代码检查数据库是否存在：</span><span class="sxs-lookup"><span data-stu-id="9d326-253">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="9d326-254">如果找不到数据库；</span><span class="sxs-lookup"><span data-stu-id="9d326-254">If the database is not found;</span></span>
  * <span data-ttu-id="9d326-255">则创建一个数据库并使用测试数据加载。</span><span class="sxs-lookup"><span data-stu-id="9d326-255">It is created and loaded with test data.</span></span> <span data-ttu-id="9d326-256">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="9d326-256">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="9d326-257">如果能找到数据库，则不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="9d326-257">If the database if found, it takes no action.</span></span>

<span data-ttu-id="9d326-258">使用以下代码更新 Program.cs：</span><span class="sxs-lookup"><span data-stu-id="9d326-258">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="9d326-259">Program.cs 在应用启动时执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9d326-259">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="9d326-260">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="9d326-260">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="9d326-261">调用 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="9d326-261">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="9d326-262">当 `Initialize` 方法完成时释放上下文，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="9d326-262">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="9d326-263">第一次运行该应用时，会创建数据库并使用测试数据加载该数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-263">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="9d326-264">每当数据模型发生更改时：</span><span class="sxs-lookup"><span data-stu-id="9d326-264">Whenever the data model changes:</span></span>

* <span data-ttu-id="9d326-265">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-265">Delete the database.</span></span>
* <span data-ttu-id="9d326-266">更新 Seed 方法，并使用新数据库重新开始。</span><span class="sxs-lookup"><span data-stu-id="9d326-266">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="9d326-267">在后面的教程中，在更改数据模型时会修改数据库，而不会删除和重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-267">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="9d326-268">数据模型发生更改时不会丢失任何数据。</span><span class="sxs-lookup"><span data-stu-id="9d326-268">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="9d326-269">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9d326-269">Create controller and views</span></span>

<span data-ttu-id="9d326-270">使用 Visual Studio 中的基架引擎添加一个 MVC 控制器，以及使用 EF 来查询和保存数据的视图。</span><span class="sxs-lookup"><span data-stu-id="9d326-270">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="9d326-271">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 操作方法和视图的自动创建被称为基架。</span><span class="sxs-lookup"><span data-stu-id="9d326-271">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="9d326-272">在“解决方案资源管理器”中，右键单击 `Controllers` 文件夹，然后选择“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="9d326-272">In **Solution Explorer** , right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="9d326-273">在“添加基架”对话框中：</span><span class="sxs-lookup"><span data-stu-id="9d326-273">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="9d326-274">选择 **视图使用 Entity Framework 的 MVC 控制器** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-274">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="9d326-275">单击 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-275">Click **Add**.</span></span> <span data-ttu-id="9d326-276">随即将显示“使用 Entity Framework 添加包含视图的 MVC 控制器”对话框：![构架 Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="9d326-276">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="9d326-277">在“模型类”中选择“Student”。</span><span class="sxs-lookup"><span data-stu-id="9d326-277">In **Model class** , select **Student**.</span></span>
  * <span data-ttu-id="9d326-278">在“数据上下文类”中选择“SchoolContext” 。</span><span class="sxs-lookup"><span data-stu-id="9d326-278">In **Data context class** , select **SchoolContext**.</span></span>
  * <span data-ttu-id="9d326-279">使用 **StudentsController** 作为默认名称。</span><span class="sxs-lookup"><span data-stu-id="9d326-279">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="9d326-280">单击 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-280">Click **Add**.</span></span>

<span data-ttu-id="9d326-281">Visual Studio 基架引擎创建 `StudentsController.cs` 文件和一组对应于控制器的视图（`*.cshtml` 文件）。</span><span class="sxs-lookup"><span data-stu-id="9d326-281">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="9d326-282">注意控制器将 `SchoolContext` 作为构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="9d326-282">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="9d326-283">ASP.NET Core 依赖关系注入负责将 `SchoolContext` 实例传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="9d326-283">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="9d326-284">你在 `Startup` 类中对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="9d326-284">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="9d326-285">控制器包含 `Index` 操作方法，用于显示数据库中的所有学生。</span><span class="sxs-lookup"><span data-stu-id="9d326-285">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="9d326-286">该方法从学生实体集中获取学生列表，学生实体集则是通过读取数据库上下文实例中的 `Students` 属性获得：</span><span class="sxs-lookup"><span data-stu-id="9d326-286">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="9d326-287">本教程后面部分将介绍此代码中的异步编程元素。</span><span class="sxs-lookup"><span data-stu-id="9d326-287">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="9d326-288">*Views/Students/Index.cshtml* 视图使用table标签显示此列表：</span><span class="sxs-lookup"><span data-stu-id="9d326-288">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="9d326-289">按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-289">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="9d326-290">单击学生选项卡以查看 `DbInitializer.Initialize` 插入的测试的数据。</span><span class="sxs-lookup"><span data-stu-id="9d326-290">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="9d326-291">你将看到 `Students` 选项卡链接在页的顶部或在单击右上角后的导航图标中，具体显示在哪里取决于浏览器窗口宽度。</span><span class="sxs-lookup"><span data-stu-id="9d326-291">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University 主页宽窄](intro/_static/home-page-narrow.png)

![“学生索引”页](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="9d326-294">查看数据库</span><span class="sxs-lookup"><span data-stu-id="9d326-294">View the database</span></span>

<span data-ttu-id="9d326-295">启动应用时，`DbInitializer.Initialize` 方法会调用 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="9d326-295">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="9d326-296">EF 看到没有数据库：</span><span class="sxs-lookup"><span data-stu-id="9d326-296">EF saw that there was no database:</span></span>

* <span data-ttu-id="9d326-297">因此，它创建了一个数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-297">So it created a database.</span></span>
* <span data-ttu-id="9d326-298">`Initialize` 方法代码用数据填充数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-298">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="9d326-299">在 Visual Studio 中使用“SQL Server 对象资源管理器”(SSOX) 查看数据库：</span><span class="sxs-lookup"><span data-stu-id="9d326-299">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="9d326-300">从 Visual Studio 中的“视图”菜单选择“SQL Server 对象资源管理器”。</span><span class="sxs-lookup"><span data-stu-id="9d326-300">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="9d326-301">在 SSOX 中，选择“(localdb)\MSSQLLocalDB”>“数据库”。</span><span class="sxs-lookup"><span data-stu-id="9d326-301">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="9d326-302">选择 *:::no-loc(appsettings.json):::* 文件中的连接字符串中的数据库名称的实体 `ContosoUniversity1`。</span><span class="sxs-lookup"><span data-stu-id="9d326-302">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *:::no-loc(appsettings.json):::* file.</span></span>
* <span data-ttu-id="9d326-303">展开“表”节点，查看数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="9d326-303">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX 中的表](intro/_static/ssox-tables.png)

<span data-ttu-id="9d326-305">右键单击“Student”表，然后单击“查看数据”以查看表中的数据。</span><span class="sxs-lookup"><span data-stu-id="9d326-305">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![SSOX 中的 Student 表](intro/_static/ssox-student-table.png)

<span data-ttu-id="9d326-307">`*.mdf` 和 `*.ldf` 数据库文件位于 *C:\Users\\\<username>* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="9d326-307">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="9d326-308">由于 `EnsureCreated` 是在应用启动时运行的初始化方法中调用的，因此可以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9d326-308">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="9d326-309">对 `Student` 类进行更改。</span><span class="sxs-lookup"><span data-stu-id="9d326-309">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="9d326-310">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-310">Delete the database.</span></span>
* <span data-ttu-id="9d326-311">停止，然后启动应用。</span><span class="sxs-lookup"><span data-stu-id="9d326-311">Stop, then start the app.</span></span> <span data-ttu-id="9d326-312">将自动重新创建数据库以匹配此更改。</span><span class="sxs-lookup"><span data-stu-id="9d326-312">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="9d326-313">例如，如果向 `Student` 类添加 `EmailAddress` 属性，则重新创建的表中会有新的 `EmailAddress` 列。</span><span class="sxs-lookup"><span data-stu-id="9d326-313">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="9d326-314">分类的视图不会显示新的 `EmailAddress` 属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-314">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="9d326-315">约定</span><span class="sxs-lookup"><span data-stu-id="9d326-315">Conventions</span></span>

<span data-ttu-id="9d326-316">因为使用了 EF 使用的约定，为使 EF 创建完整数据库而编写的代码量是最小的：</span><span class="sxs-lookup"><span data-stu-id="9d326-316">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="9d326-317">`DbSet` 类型的属性用作表名。</span><span class="sxs-lookup"><span data-stu-id="9d326-317">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="9d326-318">如果实体未被 `DbSet` 属性引用，实体类名称用作表名称。</span><span class="sxs-lookup"><span data-stu-id="9d326-318">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="9d326-319">使用实体属性名作为列名。</span><span class="sxs-lookup"><span data-stu-id="9d326-319">Entity property names are used for column names.</span></span>
* <span data-ttu-id="9d326-320">命名为 `ID` 或 `classnameID` 的实体属性识别为 PK 属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-320">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="9d326-321">如果将属性命名为`<`导航属性名称`><`PK 属性名称`>`，则该属性解释为 FK 属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-321">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="9d326-322">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的 PK 为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="9d326-322">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="9d326-323">还可以将 FK 属性命名为 `<`主键属性名称`>`。</span><span class="sxs-lookup"><span data-stu-id="9d326-323">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="9d326-324">例如 `EnrollmentID`，因为 `Enrollment` 实体的 PK 为 `EnrollmentID`。</span><span class="sxs-lookup"><span data-stu-id="9d326-324">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="9d326-325">约定行为可以重写。</span><span class="sxs-lookup"><span data-stu-id="9d326-325">Conventional behavior can be overridden.</span></span> <span data-ttu-id="9d326-326">例如，可以显式指定表名，如本教程中前面部分所示。</span><span class="sxs-lookup"><span data-stu-id="9d326-326">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="9d326-327">列名称和任何属性都可以设置为 PK 或 FK。</span><span class="sxs-lookup"><span data-stu-id="9d326-327">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="9d326-328">异步代码</span><span class="sxs-lookup"><span data-stu-id="9d326-328">Asynchronous code</span></span>

<span data-ttu-id="9d326-329">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="9d326-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="9d326-330">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="9d326-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="9d326-331">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="9d326-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="9d326-332">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="9d326-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="9d326-333">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="9d326-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="9d326-334">因此，异步代码使得服务器更有效地使用资源，并且该服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="9d326-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="9d326-335">异步代码在运行时，会引入的少量开销，在低流量时对性能的影响可以忽略不计，但在针对高流量情况下潜在的性能提升是可观的。</span><span class="sxs-lookup"><span data-stu-id="9d326-335">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="9d326-336">在下面的代码中，`async`、`Task<T>`、`await` 和 `ToListAsync` 使代码以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="9d326-336">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="9d326-337">`async` 关键字用于告知编译器该方法主体将生成回调并自动创建 `Task<IActionResult>` 返回对象。</span><span class="sxs-lookup"><span data-stu-id="9d326-337">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="9d326-338">返回类型 `Task<IActionResult>` 表示正在进行的工作返回的结果为 `IActionResult` 类型。</span><span class="sxs-lookup"><span data-stu-id="9d326-338">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="9d326-339">`await` 关键字会使得编译器将方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="9d326-339">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="9d326-340">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="9d326-340">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="9d326-341">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="9d326-341">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="9d326-342">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="9d326-342">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="9d326-343">编写使用 EF 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="9d326-343">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="9d326-344">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="9d326-344">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="9d326-345">包括 `ToListAsync`， `SingleOrDefaultAsync`，和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="9d326-345">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="9d326-346">不包括只需更改 `IQueryable` 的语句，如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="9d326-346">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="9d326-347">EF 上下文是线程不安全的： 请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="9d326-347">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="9d326-348">当调用异步 EF 方法时，始终使用 `await` 关键字。</span><span class="sxs-lookup"><span data-stu-id="9d326-348">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="9d326-349">若要利用异步代码的性能优势，请确保所使用的任何库包在它们调用导致发送至数据库的查询的任何 EF 方法时也使用异步。</span><span class="sxs-lookup"><span data-stu-id="9d326-349">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="9d326-350">有关在 .NET 异步编程的详细信息，请参阅 [异步概述](/dotnet/articles/standard/async)。</span><span class="sxs-lookup"><span data-stu-id="9d326-350">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="9d326-351">限制提取的实体数</span><span class="sxs-lookup"><span data-stu-id="9d326-351">Limit entities fetched</span></span>

<span data-ttu-id="9d326-352">有关限制从查询返回的实体数的信息，请参阅[性能注意事项](xref:data/ef-rp/intro)。</span><span class="sxs-lookup"><span data-stu-id="9d326-352">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="9d326-353">请继续阅读下一篇教程，了解如何执行基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="9d326-353">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9d326-354">实现基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="9d326-354">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="9d326-355">Contoso University 示例 Web 应用程序演示如何使用 Entity Framework (EF) Core 2.2 和 Visual Studio 2017 或 2019 创建 ASP.NET Core 2.2 MVC Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9d326-355">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="9d326-356">示例应用程序供一个虚构的 Contoso 大学网站使用。</span><span class="sxs-lookup"><span data-stu-id="9d326-356">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="9d326-357">它包括诸如学生入学、 课程创建和导师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="9d326-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="9d326-358">这是一系列教程中的第一个，这一系列教程主要展示了如何从零开始构建 Contoso 大学示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="9d326-358">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9d326-359">先决条件</span><span class="sxs-lookup"><span data-stu-id="9d326-359">Prerequisites</span></span>

* [<span data-ttu-id="9d326-360">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="9d326-360">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="9d326-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 包含以下工作负荷：</span><span class="sxs-lookup"><span data-stu-id="9d326-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="9d326-362">ASP.NET 和 Web 开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="9d326-362">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="9d326-363">.NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="9d326-363">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="9d326-364">疑难解答</span><span class="sxs-lookup"><span data-stu-id="9d326-364">Troubleshooting</span></span>

<span data-ttu-id="9d326-365">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="9d326-365">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="9d326-366">常见错误以及对应的解决方案，请参阅 [最新教程中的故障排除](advanced.md#common-errors)。</span><span class="sxs-lookup"><span data-stu-id="9d326-366">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="9d326-367">如果没有找到遇到的问题的解决方案，可以将问题发布到StackOverflow.com 的 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 版块。</span><span class="sxs-lookup"><span data-stu-id="9d326-367">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="9d326-368">这是一系列一共有十个教程，其中每个都是在前面教程已完成的基础上继续。</span><span class="sxs-lookup"><span data-stu-id="9d326-368">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="9d326-369">请考虑在完成每一个教程后保存项目的副本。</span><span class="sxs-lookup"><span data-stu-id="9d326-369">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="9d326-370">之后如果遇到问题，你可以从保存的副本中开始寻找问题，而不是从头开始。</span><span class="sxs-lookup"><span data-stu-id="9d326-370">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="9d326-371">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="9d326-371">Contoso University web app</span></span>

<span data-ttu-id="9d326-372">你将在这些教程中学习构建一个简单的大学网站的应用程序。</span><span class="sxs-lookup"><span data-stu-id="9d326-372">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="9d326-373">用户可以查看和更新学生、 课程和教师信息。</span><span class="sxs-lookup"><span data-stu-id="9d326-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="9d326-374">以下是一些你即将创建的页面。</span><span class="sxs-lookup"><span data-stu-id="9d326-374">Here are a few of the screens you'll create.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="9d326-377">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="9d326-377">Create web app</span></span>

* <span data-ttu-id="9d326-378">打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="9d326-378">Open Visual Studio.</span></span>

* <span data-ttu-id="9d326-379">从“文件”菜单中选择“新建”>“项目” 。</span><span class="sxs-lookup"><span data-stu-id="9d326-379">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="9d326-380">从左窗格中依次选择“已安装”>“Visual C#”>“Web”。</span><span class="sxs-lookup"><span data-stu-id="9d326-380">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="9d326-381">选择“ASP.NET Core Web 应用程序”项目模板。</span><span class="sxs-lookup"><span data-stu-id="9d326-381">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="9d326-382">输入“ContosoUniversity”作为名称，然后单击“确定” 。</span><span class="sxs-lookup"><span data-stu-id="9d326-382">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![“新建项目”对话框](intro/_static/new-project2.png)

* <span data-ttu-id="9d326-384">等待“新建 ASP.NET Core Web 应用程序”对话框显示出来。</span><span class="sxs-lookup"><span data-stu-id="9d326-384">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="9d326-385">选择“.NET Core”、“ASP.NET Core 2.2”和“Web 应用程序(模型-视图-控制器)”模板  。</span><span class="sxs-lookup"><span data-stu-id="9d326-385">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="9d326-386">请确保 **身份验证** 设置为  **不进行身份验证** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-386">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="9d326-387">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="9d326-387">Select **OK**</span></span>

  ![新的 ASP.NET Core 项目对话框](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="9d326-389">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="9d326-389">Set up the site style</span></span>

<span data-ttu-id="9d326-390">通过几个简单的更改设置站点菜单、 布局和主页。</span><span class="sxs-lookup"><span data-stu-id="9d326-390">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="9d326-391">打开 Views/Shared/_Layout.cshtml 并进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="9d326-391">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="9d326-392">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="9d326-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="9d326-393">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="9d326-393">There are three occurrences.</span></span>

* <span data-ttu-id="9d326-394">添加“关于”、“学生”、“课程”“讲师”和“院系”的菜单项，并删除“隐私”菜单项。</span><span class="sxs-lookup"><span data-stu-id="9d326-394">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="9d326-395">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="9d326-395">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="9d326-396">在 *Views/Home/Index.cshtml* ，将文件的内容替换为以下代码以将有关 ASP.NET 和 MVC 的内容替换为有关此应用程序的内容：</span><span class="sxs-lookup"><span data-stu-id="9d326-396">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="9d326-397">按 CTRL + F5 来运行该项目或从菜单选择 **调试 > 开始执行(不调试)** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-397">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="9d326-398">你会看到首页，以及通过这个教程创建的页对应的选项卡。</span><span class="sxs-lookup"><span data-stu-id="9d326-398">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University 主页](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="9d326-400">关于 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="9d326-400">About EF Core NuGet packages</span></span>

<span data-ttu-id="9d326-401">若要为项目添加 EF Core 支持，需要安装相应的数据库驱动包。</span><span class="sxs-lookup"><span data-stu-id="9d326-401">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="9d326-402">本教程使用 SQL Server，相关驱动包[Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="9d326-402">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="9d326-403">此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，因此无需引用该包。</span><span class="sxs-lookup"><span data-stu-id="9d326-403">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="9d326-404">EF SQL Server 包和其依赖项（`Microsoft.EntityFrameworkCore` 和 `Microsoft.EntityFrameworkCore.Relational`）一起提供 EF 的运行时支持。</span><span class="sxs-lookup"><span data-stu-id="9d326-404">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="9d326-405">你将在之后的 [迁移](migrations.md) 教程中学习添加工具包。</span><span class="sxs-lookup"><span data-stu-id="9d326-405">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="9d326-406">有关其他可用于 EF Core 的数据库驱动的信息，请参阅 [数据库驱动](/ef/core/providers/)。</span><span class="sxs-lookup"><span data-stu-id="9d326-406">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="9d326-407">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="9d326-407">Create the data model</span></span>

<span data-ttu-id="9d326-408">接下来你将创建 Contoso 大学应用程序的实体类。</span><span class="sxs-lookup"><span data-stu-id="9d326-408">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="9d326-409">你将从以下三个实体类开始。</span><span class="sxs-lookup"><span data-stu-id="9d326-409">You'll start with the following three entities.</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="9d326-411">`Student` 和 `Enrollment`实体之间是一对多的关系，`Course` 和`Enrollment` 实体之间也是一个对多的关系。</span><span class="sxs-lookup"><span data-stu-id="9d326-411">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="9d326-412">换而言之，一名学生可以修读任意数量的课程, 并且某一课程可以被任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="9d326-412">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="9d326-413">接下来，你将创建与这些实体对应的类。</span><span class="sxs-lookup"><span data-stu-id="9d326-413">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="9d326-414">Student 实体</span><span class="sxs-lookup"><span data-stu-id="9d326-414">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="9d326-416">在 *Models* 文件夹中，创建一个名为 *Student.cs* 的类文件并且将模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="9d326-416">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-417">`ID` 属性将成为对应于此类的数据库表中的主键。</span><span class="sxs-lookup"><span data-stu-id="9d326-417">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="9d326-418">默认情况下，EF 将会将名为 `ID` 或 `classnameID` 的属性解析为主键。</span><span class="sxs-lookup"><span data-stu-id="9d326-418">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="9d326-419">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="9d326-419">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="9d326-420">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-420">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="9d326-421">在这个案例下，`Student entity` 中的 `Enrollments` 属性会保留所有与 `Student` 实体相关的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="9d326-421">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="9d326-422">换而言之，如果数据库中的 `Student` 行具有两个相关的 `Enrollment` 行（在其 StudentID 外键列中包含该学生的主键值的行），则该 `Student` 实体的 `Enrollments` 导航属性将包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-422">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="9d326-423">如果导航属性可以具有多个实体 （如多对多或一对多关系），那么导航属性的类型必须是可以添加、 删除和更新条目的容器，如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="9d326-423">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="9d326-424">你可以指定 `ICollection<T>` 或实现该接口类型，如 `List<T>` 或 `HashSet<T>`。</span><span class="sxs-lookup"><span data-stu-id="9d326-424">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="9d326-425">如果指定 `ICollection<T>`，EF在默认情况下创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="9d326-425">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="9d326-426">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="9d326-426">The Enrollment entity</span></span>

![修读实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="9d326-428">在 *Models* 文件夹中，创建 *Enrollment.cs* 并且用以下代码替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="9d326-428">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-429">`EnrollmentID` 属性将被设为主键; 此实体使用 `classnameID` 模式而不是如 `Student` 实体那样直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="9d326-429">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="9d326-430">通常情况下，你选择一个主键模式，并在你的数据模型自始至终使用这种模式。</span><span class="sxs-lookup"><span data-stu-id="9d326-430">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="9d326-431">在这里，使用了两种不同的模式只是为了说明你可以使用任一模式来指定主键。</span><span class="sxs-lookup"><span data-stu-id="9d326-431">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="9d326-432">在 [后面的教程](inheritance.md)，你将了解到使用`ID`这种模式可以更轻松地在数据模型之间实现继承。</span><span class="sxs-lookup"><span data-stu-id="9d326-432">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="9d326-433">`Grade` 属性是 `enum`。</span><span class="sxs-lookup"><span data-stu-id="9d326-433">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="9d326-434">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="9d326-434">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="9d326-435">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="9d326-435">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="9d326-436">`StudentID` 属性是一个外键，`Student` 是与其且对应的导航属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-436">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="9d326-437">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含单个 `Student` 实体 (与前面所看到的 `Student.Enrollments` 导航属性不同后，`Student`中可以容纳多个 `Enrollment` 实体)。</span><span class="sxs-lookup"><span data-stu-id="9d326-437">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="9d326-438">`CourseID` 属性是一个外键， `Course` 是与其对应的导航属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="9d326-439">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="9d326-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="9d326-440">如果一个属性名为 `<navigation property name><primary key property name>`，Entity Framework 就会将这个属性解析为外键属性(例如， `Student` 实体的主键是`ID`， `Student` 是`Enrollment`的导航属性所以`Enrollment`实体中 `StudentID` 会被解析为外键)。</span><span class="sxs-lookup"><span data-stu-id="9d326-440">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="9d326-441">此外还可以将需要解析为外键的属性命名为 `<primary key property name>` (例如，`CourseID` 由于 `Course` 实体的主键所以 `CourseID` 也被解析为外键)。</span><span class="sxs-lookup"><span data-stu-id="9d326-441">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="9d326-442">Course 实体</span><span class="sxs-lookup"><span data-stu-id="9d326-442">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="9d326-444">在 *Models* 文件夹中，创建 *Course.cs* 并且用以下代码替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="9d326-444">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-445">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-445">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="9d326-446">一个 `Course` 体可以与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="9d326-446">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="9d326-447">我们在本系列 [后面的教程](complex-data-model.md) 中会有更多有关 `DatabaseGenerated` 特性的例子。</span><span class="sxs-lookup"><span data-stu-id="9d326-447">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="9d326-448">简单来说，此特性让你能自行指定主键，而不是让数据库自动指定主键。</span><span class="sxs-lookup"><span data-stu-id="9d326-448">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="9d326-449">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="9d326-449">Create the database context</span></span>

<span data-ttu-id="9d326-450">使得给定的数据模型与 Entity Framework 功能相协调的主类是数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="9d326-450">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="9d326-451">可以通过继承 `Microsoft.EntityFrameworkCore.DbContext` 类的方式创建此类。</span><span class="sxs-lookup"><span data-stu-id="9d326-451">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="9d326-452">在该类中你可以指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-452">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="9d326-453">你还可以定义某些 Entity Framework 行为。</span><span class="sxs-lookup"><span data-stu-id="9d326-453">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="9d326-454">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="9d326-454">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="9d326-455">在项目文件夹中，创建名为的文件夹 *Data* 。</span><span class="sxs-lookup"><span data-stu-id="9d326-455">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="9d326-456">在 *Data* 文件夹创建名为 *SchoolContext.cs* 的类文件，并将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="9d326-456">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-457">此代码将为每个实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-457">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="9d326-458">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="9d326-458">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9d326-459">在这里可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>` 语句，实现的功能没有任何改变。</span><span class="sxs-lookup"><span data-stu-id="9d326-459">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="9d326-460">Entity Framework 会隐式包含这两个实体因为 `Student` 实体引用了 `Enrollment` 实体、`Enrollment` 实体引用了 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-460">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="9d326-461">当数据库创建完成后， EF 创建一系列数据表，表名默认和 `DbSet` 属性名相同。</span><span class="sxs-lookup"><span data-stu-id="9d326-461">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="9d326-462">集合属性的名称一般使用复数形式，但不同的开发人员的命名习惯可能不一样，开发人员根据自己的情况确定是否使用复数形式。</span><span class="sxs-lookup"><span data-stu-id="9d326-462">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="9d326-463">在定义 DbSet 属性的代码之后，添加下面高亮代码，对 DbContext 指定单数的表名来覆盖默认的表名。</span><span class="sxs-lookup"><span data-stu-id="9d326-463">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="9d326-464">此教程在最后一个 DbSet 属性之后，添加以下高亮显示的代码。</span><span class="sxs-lookup"><span data-stu-id="9d326-464">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="9d326-465">生成项目以检查编译器错误。</span><span class="sxs-lookup"><span data-stu-id="9d326-465">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="9d326-466">注册 SchoolContext</span><span class="sxs-lookup"><span data-stu-id="9d326-466">Register the SchoolContext</span></span>

<span data-ttu-id="9d326-467">ASP.NET Core 默认实现 [依赖注入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="9d326-467">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="9d326-468">在应用程序启动过程通过依赖注入注册相关服务 （例如 EF 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="9d326-468">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9d326-469">需要这些服务的组件 （如 MVC 控制器） 可以通过向构造函数添加相关参数来获得对应服务。</span><span class="sxs-lookup"><span data-stu-id="9d326-469">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9d326-470">在本教程后面你将看到控制器构造函数的代码，就是通过上述方式获得上下文实例。</span><span class="sxs-lookup"><span data-stu-id="9d326-470">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="9d326-471">若要将 `SchoolContext` 注册为一种服务，打开 *Startup.cs* ，并将高亮代码添加到 `ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="9d326-471">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="9d326-472">通过调用 `DbContextOptionsBuilder` 中的一个方法将数据库连接字符串在配置文件中的名称传递给上下文对象。</span><span class="sxs-lookup"><span data-stu-id="9d326-472">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="9d326-473">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *:::no-loc(appsettings.json):::* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="9d326-473">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="9d326-474">添加 `using` 语句引用 `ContosoUniversity.Data` 和 `Microsoft.EntityFrameworkCore` 命名空间，然后生成项目。</span><span class="sxs-lookup"><span data-stu-id="9d326-474">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="9d326-475">打开 *:::no-loc(appsettings.json):::* 文件，并按以下所示添加连接字符串。</span><span class="sxs-lookup"><span data-stu-id="9d326-475">Open the *:::no-loc(appsettings.json):::* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="9d326-476">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="9d326-476">SQL Server Express LocalDB</span></span>

<span data-ttu-id="9d326-477">数据库连接字符串指定使用 SQL Server LocalDB 数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-477">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="9d326-478">LocalDB 是 SQL Server Express 数据库引擎的轻量级版本，用于应用程序开发，不在生产环境中使用。</span><span class="sxs-lookup"><span data-stu-id="9d326-478">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="9d326-479">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="9d326-479">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="9d326-480">默认情况下， LocalDB 在 `C:/Users/<user>` 目录下创建 *.mdf* 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="9d326-480">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="9d326-481">使用测试数据初始化数据库</span><span class="sxs-lookup"><span data-stu-id="9d326-481">Initialize DB with test data</span></span>

<span data-ttu-id="9d326-482">Entity Framework 已经为你创建了一个空数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-482">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="9d326-483">在本部分中，你将编写一个方法用于向数据库填充测试数据，该方法会在数据库创建完成之后执行。</span><span class="sxs-lookup"><span data-stu-id="9d326-483">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="9d326-484">此处将使用 `EnsureCreated` 方法来自动创建数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-484">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="9d326-485">在 [后面的教程](migrations.md) 你将了解如何通过使用 Code First Migration 来更改而不是删除并重新创建数据库来处理模型更改。</span><span class="sxs-lookup"><span data-stu-id="9d326-485">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="9d326-486">在 *Data* 文件夹中，创建名为的新类文件 *DbInitializer.cs* 并且将模板代码替换为以下代码，使得在需要时能创建数据库并向其填充测试数据。</span><span class="sxs-lookup"><span data-stu-id="9d326-486">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="9d326-487">这段代码首先检查是否有学生数据在数据库中，如果没有的话，就可以假定数据库是新建的，然后使用测试数据进行填充。</span><span class="sxs-lookup"><span data-stu-id="9d326-487">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="9d326-488">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="9d326-488">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="9d326-489">在 *Program.cs* ，修改 `Main` 方法，使得在应用程序启动时能执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9d326-489">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="9d326-490">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="9d326-490">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="9d326-491">调用 seed 方法，将上下文传递给它。</span><span class="sxs-lookup"><span data-stu-id="9d326-491">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="9d326-492">Seed 方法完成此操作时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="9d326-492">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="9d326-493">首次运行该应用程序时，将创建数据库并使用测试数据填充该数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-493">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="9d326-494">每次更改数据模型时：</span><span class="sxs-lookup"><span data-stu-id="9d326-494">Whenever you change the data model:</span></span>

 * <span data-ttu-id="9d326-495">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-495">Delete the database.</span></span>
 * <span data-ttu-id="9d326-496">更新 Seed 方法，并以相同的方式用新数据库重新开始。</span><span class="sxs-lookup"><span data-stu-id="9d326-496">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="9d326-497">在之后的教程中，你将了解如何在数据模型更改时，只需修改数据库而无需删除重建数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-497">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="9d326-498">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9d326-498">Create controller and views</span></span>

<span data-ttu-id="9d326-499">在本节，将使用 Visual Studio 中的基架引擎添加一个 MVC 控制器，以及使用 EF 来查询和保存数据的视图。</span><span class="sxs-lookup"><span data-stu-id="9d326-499">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="9d326-500">CRUD 操作方法和视图的自动创建被称为基架。</span><span class="sxs-lookup"><span data-stu-id="9d326-500">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="9d326-501">基架与代码生成不同，基架的代码是一个起点，可以修改基架以满足自己需求，而你通常无需修改生成的代码。</span><span class="sxs-lookup"><span data-stu-id="9d326-501">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="9d326-502">当你需要自定义生成代码时，可使用一部分类或需求发生变化时重新生成代码。</span><span class="sxs-lookup"><span data-stu-id="9d326-502">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="9d326-503">右键单击 **解决方案资源管理器** 中的 **Controllers** 文件夹选择 **添加 > 新搭建基架的项目** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-503">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="9d326-504">在“添加基架”对话框中：</span><span class="sxs-lookup"><span data-stu-id="9d326-504">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="9d326-505">选择 **视图使用 Entity Framework 的 MVC 控制器** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-505">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="9d326-506">单击 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-506">Click **Add**.</span></span> <span data-ttu-id="9d326-507">随即将显示“使用 Entity Framework 添加包含视图的 MVC 控制器”对话框：![构架 Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="9d326-507">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="9d326-508">在 **模型类** 选择 **Student** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-508">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="9d326-509">在“数据上下文类”中选择 SchoolContext 。</span><span class="sxs-lookup"><span data-stu-id="9d326-509">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="9d326-510">使用 **StudentsController** 作为默认名称。</span><span class="sxs-lookup"><span data-stu-id="9d326-510">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="9d326-511">单击 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-511">Click **Add**.</span></span>

<span data-ttu-id="9d326-512">Visual Studio 基架引擎创建 StudentsController.cs 文件和一组对应于控制器的视图（.cshtml 文件）</span><span class="sxs-lookup"><span data-stu-id="9d326-512">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="9d326-513">注意控制器将 `SchoolContext` 作为构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="9d326-513">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="9d326-514">ASP.NET Core 依赖关系注入负责将 `SchoolContext` 实例传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="9d326-514">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="9d326-515">在 Startup.cs 文件中对其进行了配置。</span><span class="sxs-lookup"><span data-stu-id="9d326-515">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="9d326-516">控制器包含 `Index` 操作方法，用于显示数据库中的所有学生。</span><span class="sxs-lookup"><span data-stu-id="9d326-516">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="9d326-517">该方法从学生实体集中获取学生列表，学生实体集则是通过读取数据库上下文实例中的 `Students` 属性获得：</span><span class="sxs-lookup"><span data-stu-id="9d326-517">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="9d326-518">本教程后面部分将介绍此代码中的异步编程元素。</span><span class="sxs-lookup"><span data-stu-id="9d326-518">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="9d326-519">*Views/Students/Index.cshtml* 视图使用table标签显示此列表：</span><span class="sxs-lookup"><span data-stu-id="9d326-519">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="9d326-520">按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。</span><span class="sxs-lookup"><span data-stu-id="9d326-520">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="9d326-521">单击学生选项卡以查看 `DbInitializer.Initialize` 插入的测试的数据。</span><span class="sxs-lookup"><span data-stu-id="9d326-521">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="9d326-522">你将看到 `Students` 选项卡链接在页的顶部或在单击右上角后的导航图标中，具体显示在哪里取决于浏览器窗口宽度。</span><span class="sxs-lookup"><span data-stu-id="9d326-522">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University 主页宽窄](intro/_static/home-page-narrow.png)

![“学生索引”页](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="9d326-525">查看数据库</span><span class="sxs-lookup"><span data-stu-id="9d326-525">View the database</span></span>

<span data-ttu-id="9d326-526">当你启动了应用程序，`DbInitializer.Initialize` 方法调用 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="9d326-526">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="9d326-527">EF 没有检测到相关数据库，因此自己创建了一个，接着 `Initialize` 方法的其余代码向数据库中填充数据。</span><span class="sxs-lookup"><span data-stu-id="9d326-527">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="9d326-528">你可以使用 Visual Studio 中的 **SQL Server 对象资源管理器** (SSOX) 查看数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-528">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="9d326-529">关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="9d326-529">Close the browser.</span></span>

<span data-ttu-id="9d326-530">如果 SSOX 窗口尚未打开，请从Visual Studio 中的 **视图** 菜单中选择。</span><span class="sxs-lookup"><span data-stu-id="9d326-530">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="9d326-531">在 SSOX 中，单击“(localdb)\MSSQLLocalDB”>“数据库”，然后单击 *:::no-loc(appsettings.json):::* 文件中的连接字符串中的数据库名称的实体。</span><span class="sxs-lookup"><span data-stu-id="9d326-531">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="9d326-532">展开“表”节点，查看数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="9d326-532">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX 中的表](intro/_static/ssox-tables.png)

<span data-ttu-id="9d326-534">右键单击 **Student** 表，然后单击 **查看数据** ，即可查看已创建的列和已插入到表的行。</span><span class="sxs-lookup"><span data-stu-id="9d326-534">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX 中的 Student 表](intro/_static/ssox-student-table.png)

<span data-ttu-id="9d326-536">*.mdf* 和 *.ldf* 数据库文件位于 *C:\Users\\\<username>* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="9d326-536">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="9d326-537">因为调用 `EnsureCreated` 的初始化方法在启动应用程序时才运行，所以在这之前你可以更改 `Student` 类、 删除数据库、 再运行一次应用程序，这时候数据库将自动重新创建，以匹配所做的更改。</span><span class="sxs-lookup"><span data-stu-id="9d326-537">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="9d326-538">例如，如果向 `Student` 类添加 `EmailAddress` 属性，重新的创建表中会有 `EmailAddress` 列。</span><span class="sxs-lookup"><span data-stu-id="9d326-538">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="9d326-539">约定</span><span class="sxs-lookup"><span data-stu-id="9d326-539">Conventions</span></span>

<span data-ttu-id="9d326-540">由于 Entity Framework 有一定的约束条件，你只需要按规则编写很少的代码就能够创建一个完整的数据库。</span><span class="sxs-lookup"><span data-stu-id="9d326-540">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="9d326-541">`DbSet` 类型的属性用作表名。</span><span class="sxs-lookup"><span data-stu-id="9d326-541">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="9d326-542">如果实体未被 `DbSet` 属性引用，实体类名称用作表名称。</span><span class="sxs-lookup"><span data-stu-id="9d326-542">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="9d326-543">使用实体属性名作为列名。</span><span class="sxs-lookup"><span data-stu-id="9d326-543">Entity property names are used for column names.</span></span>
* <span data-ttu-id="9d326-544">以 ID 或 classnameID 命名的实体属性被视为主键属性。</span><span class="sxs-lookup"><span data-stu-id="9d326-544">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="9d326-545">如果属性名为 *\<navigation property name>\<primary key property name>* 将被解释为外键属性 (例如，`StudentID` 对应 `Student` 导航属性，`Student` 实体的主键是`ID`，所以`StudentID`被解释为外键属性)。</span><span class="sxs-lookup"><span data-stu-id="9d326-545">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="9d326-546">此外也可以将外键属性命名为 *\<primary key property name>* (例如，`EnrollmentID`，由于 `Enrollment` 实体的主键是 `EnrollmentID`，因此被解释为外键)。</span><span class="sxs-lookup"><span data-stu-id="9d326-546">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="9d326-547">约定行为可以重写。</span><span class="sxs-lookup"><span data-stu-id="9d326-547">Conventional behavior can be overridden.</span></span> <span data-ttu-id="9d326-548">例如，本教程前半部分显式指定表名称。</span><span class="sxs-lookup"><span data-stu-id="9d326-548">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="9d326-549">本系列 [后面教程](complex-data-model.md) 则设置列名称并将任何属性设置为主键或外键。</span><span class="sxs-lookup"><span data-stu-id="9d326-549">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="9d326-550">异步代码</span><span class="sxs-lookup"><span data-stu-id="9d326-550">Asynchronous code</span></span>

<span data-ttu-id="9d326-551">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="9d326-551">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="9d326-552">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="9d326-552">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="9d326-553">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="9d326-553">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="9d326-554">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="9d326-554">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="9d326-555">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="9d326-555">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="9d326-556">因此，异步代码使得服务器更有效地使用资源，并且该服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="9d326-556">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="9d326-557">异步代码在运行时，会引入的少量开销，在低流量时对性能的影响可以忽略不计，但在针对高流量情况下潜在的性能提升是可观的。</span><span class="sxs-lookup"><span data-stu-id="9d326-557">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="9d326-558">在以下代码中，`async` 关键字、`Task<T>` 返回值、`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="9d326-558">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="9d326-559">`async` 关键字用于告知编译器该方法主体将生成回调并自动创建 `Task<IActionResult>` 返回对象。</span><span class="sxs-lookup"><span data-stu-id="9d326-559">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="9d326-560">返回类型 `Task<IActionResult>` 表示正在进行的工作返回的结果为 `IActionResult` 类型。</span><span class="sxs-lookup"><span data-stu-id="9d326-560">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="9d326-561">`await` 关键字会使得编译器将方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="9d326-561">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="9d326-562">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="9d326-562">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="9d326-563">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="9d326-563">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="9d326-564">`ToListAsync` 是 `ToList` 方法的的异步扩展版本。</span><span class="sxs-lookup"><span data-stu-id="9d326-564">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="9d326-565">使用 Entity Framework 编写异步代码时的一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="9d326-565">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="9d326-566">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="9d326-566">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="9d326-567">包括 `ToListAsync`， `SingleOrDefaultAsync`，和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="9d326-567">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="9d326-568">不包括只需更改 `IQueryable` 的语句，如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="9d326-568">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="9d326-569">EF 上下文是线程不安全的： 请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="9d326-569">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="9d326-570">当调用异步 EF 方法时，始终使用 `await` 关键字。</span><span class="sxs-lookup"><span data-stu-id="9d326-570">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="9d326-571">如果你想要利用异步代码的性能优势，请确保你所使用的任何库和包在它们调用导致 Entity Framework 数据库查询方法时也使用异步。</span><span class="sxs-lookup"><span data-stu-id="9d326-571">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="9d326-572">有关在 .NET 异步编程的详细信息，请参阅 [异步概述](/dotnet/articles/standard/async)。</span><span class="sxs-lookup"><span data-stu-id="9d326-572">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="9d326-573">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9d326-573">Next steps</span></span>

<span data-ttu-id="9d326-574">请继续阅读下一篇教程，了解如何执行基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="9d326-574">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9d326-575">实现基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="9d326-575">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
