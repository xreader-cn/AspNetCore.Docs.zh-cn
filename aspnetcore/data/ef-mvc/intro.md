---
title: 教程：在 ASP.NET MVC Web 应用中使用 EF Core 入门
description: 这是一系列教程中的第一个，这一系列教程主要展示了如何从零开始构建 Contoso 大学示例应用程序。
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: 8cad650cacd0b467a45a13c7dde0410aa41fdb32
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212565"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="54318-103">教程：在 ASP.NET MVC Web 应用中使用 EF Core 入门</span><span class="sxs-lookup"><span data-stu-id="54318-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="54318-104">Contoso University 示例 Web 应用程序演示如何使用 Entity Framework (EF) Core 2.2 和 Visual Studio 2017 或 2019 创建 ASP.NET Core 2.2 MVC Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="54318-104">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="54318-105">示例应用程序供一个虚构的 Contoso 大学网站使用。</span><span class="sxs-lookup"><span data-stu-id="54318-105">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="54318-106">它包括诸如学生入学、 课程创建和导师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="54318-106">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="54318-107">这是一系列教程中的第一个，这一系列教程主要展示了如何从零开始构建 Contoso 大学示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="54318-107">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="54318-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="54318-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="54318-109">创建 ASP.NET Core MVC Web 应用</span><span class="sxs-lookup"><span data-stu-id="54318-109">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="54318-110">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="54318-110">Set up the site style</span></span>
> * <span data-ttu-id="54318-111">了解 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="54318-111">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="54318-112">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="54318-112">Create the data model</span></span>
> * <span data-ttu-id="54318-113">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="54318-113">Create the database context</span></span>
> * <span data-ttu-id="54318-114">为依赖关系注入注册上下文</span><span class="sxs-lookup"><span data-stu-id="54318-114">Register the context for dependency injection</span></span>
> * <span data-ttu-id="54318-115">使用测试数据初始化数据库</span><span class="sxs-lookup"><span data-stu-id="54318-115">Initialize the database with test data</span></span>
> * <span data-ttu-id="54318-116">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="54318-116">Create a controller and views</span></span>
> * <span data-ttu-id="54318-117">查看数据库</span><span class="sxs-lookup"><span data-stu-id="54318-117">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="54318-118">系统必备</span><span class="sxs-lookup"><span data-stu-id="54318-118">Prerequisites</span></span>

* [<span data-ttu-id="54318-119">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="54318-119">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download)
* <span data-ttu-id="54318-120">具有以下工作负载的 [Visual Studio 2017 或 2019](https://visualstudio.microsoft.com/downloads/)：</span><span class="sxs-lookup"><span data-stu-id="54318-120">[Visual Studio 2017 or 2019](https://visualstudio.microsoft.com/downloads/) with the following workloads:</span></span>
  * <span data-ttu-id="54318-121">ASP.NET 和 Web 开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="54318-121">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="54318-122">.NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="54318-122">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="54318-123">疑难解答</span><span class="sxs-lookup"><span data-stu-id="54318-123">Troubleshooting</span></span>

<span data-ttu-id="54318-124">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="54318-124">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="54318-125">常见错误以及对应的解决方案，请参阅 [最新教程中的故障排除](advanced.md#common-errors)。</span><span class="sxs-lookup"><span data-stu-id="54318-125">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="54318-126">如果没有找到遇到的问题的解决方案，可以将问题发布到StackOverflow.com 的 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 版块。</span><span class="sxs-lookup"><span data-stu-id="54318-126">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="54318-127">这是一系列一共有十个教程，其中每个都是在前面教程已完成的基础上继续。</span><span class="sxs-lookup"><span data-stu-id="54318-127">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="54318-128">请考虑在完成每一个教程后保存项目的副本。</span><span class="sxs-lookup"><span data-stu-id="54318-128">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="54318-129">之后如果遇到问题，你可以从保存的副本中开始寻找问题，而不是从头开始。</span><span class="sxs-lookup"><span data-stu-id="54318-129">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="54318-130">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="54318-130">Contoso University web app</span></span>

<span data-ttu-id="54318-131">你将在这些教程中学习构建一个简单的大学网站的应用程序。</span><span class="sxs-lookup"><span data-stu-id="54318-131">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="54318-132">用户可以查看和更新学生、 课程和教师信息。</span><span class="sxs-lookup"><span data-stu-id="54318-132">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="54318-133">以下是一些你即将创建的页面。</span><span class="sxs-lookup"><span data-stu-id="54318-133">Here are a few of the screens you'll create.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="54318-136">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="54318-136">Create web app</span></span>

* <span data-ttu-id="54318-137">打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="54318-137">Open Visual Studio.</span></span>

* <span data-ttu-id="54318-138">从“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="54318-138">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="54318-139">从左窗格中依次选择“已安装”>“Visual C#”>“Web”。</span><span class="sxs-lookup"><span data-stu-id="54318-139">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="54318-140">选择“ASP.NET Core Web 应用程序”项目模板。</span><span class="sxs-lookup"><span data-stu-id="54318-140">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="54318-141">输入“ContosoUniversity”作为名称，然后单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="54318-141">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![“新建项目”对话框](intro/_static/new-project2.png)

* <span data-ttu-id="54318-143">等待“新建 ASP.NET Core Web 应用程序”对话框显示出来。</span><span class="sxs-lookup"><span data-stu-id="54318-143">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="54318-144">选择“.NET Core”、“ASP.NET Core 2.2”和“Web 应用程序(模型-视图-控制器)”模板。</span><span class="sxs-lookup"><span data-stu-id="54318-144">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="54318-145">请确保 **身份验证** 设置为  **不进行身份验证**。</span><span class="sxs-lookup"><span data-stu-id="54318-145">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="54318-146">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="54318-146">Select **OK**</span></span>

  ![新的 ASP.NET Core 项目对话框](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="54318-148">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="54318-148">Set up the site style</span></span>

<span data-ttu-id="54318-149">通过几个简单的更改设置站点菜单、 布局和主页。</span><span class="sxs-lookup"><span data-stu-id="54318-149">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="54318-150">打开 Views/Shared/_Layout.cshtml 并进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="54318-150">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="54318-151">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="54318-151">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="54318-152">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="54318-152">There are three occurrences.</span></span>

* <span data-ttu-id="54318-153">添加“关于”、“学生”、“课程”“讲师”和“院系”的菜单项，并删除“隐私”菜单项。</span><span class="sxs-lookup"><span data-stu-id="54318-153">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="54318-154">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="54318-154">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="54318-155">在 *Views/Home/Index.cshtml*，将文件的内容替换为以下代码以将有关 ASP.NET 和 MVC 的内容替换为有关此应用程序的内容：</span><span class="sxs-lookup"><span data-stu-id="54318-155">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="54318-156">按 CTRL + F5 来运行该项目或从菜单选择 **调试 > 开始执行(不调试)**。</span><span class="sxs-lookup"><span data-stu-id="54318-156">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="54318-157">你会看到首页，以及通过这个教程创建的页对应的选项卡。</span><span class="sxs-lookup"><span data-stu-id="54318-157">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University 主页](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="54318-159">关于 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="54318-159">About EF Core NuGet packages</span></span>

<span data-ttu-id="54318-160">若要为项目添加 EF Core 支持，需要安装相应的数据库驱动包。</span><span class="sxs-lookup"><span data-stu-id="54318-160">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="54318-161">本教程使用 SQL Server，相关驱动包[Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="54318-161">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="54318-162">此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，因此无需引用该包。</span><span class="sxs-lookup"><span data-stu-id="54318-162">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="54318-163">EF SQL Server 包和其依赖项（`Microsoft.EntityFrameworkCore` 和 `Microsoft.EntityFrameworkCore.Relational`）一起提供 EF 的运行时支持。</span><span class="sxs-lookup"><span data-stu-id="54318-163">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="54318-164">你将在之后的 [迁移](migrations.md) 教程中学习添加工具包。</span><span class="sxs-lookup"><span data-stu-id="54318-164">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="54318-165">有关其他可用于 EF Core 的数据库驱动的信息，请参阅 [数据库驱动](/ef/core/providers/)。</span><span class="sxs-lookup"><span data-stu-id="54318-165">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="54318-166">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="54318-166">Create the data model</span></span>

<span data-ttu-id="54318-167">接下来你将创建 Contoso 大学应用程序的实体类。</span><span class="sxs-lookup"><span data-stu-id="54318-167">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="54318-168">你将从以下三个实体类开始。</span><span class="sxs-lookup"><span data-stu-id="54318-168">You'll start with the following three entities.</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="54318-170">`Student` 和 `Enrollment`实体之间是一对多的关系，`Course` 和`Enrollment` 实体之间也是一个对多的关系。</span><span class="sxs-lookup"><span data-stu-id="54318-170">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="54318-171">换而言之，一名学生可以修读任意数量的课程, 并且某一课程可以被任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="54318-171">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="54318-172">接下来，你将创建与这些实体对应的类。</span><span class="sxs-lookup"><span data-stu-id="54318-172">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="54318-173">Student 实体</span><span class="sxs-lookup"><span data-stu-id="54318-173">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="54318-175">在 *Models* 文件夹中，创建一个名为 *Student.cs* 的类文件并且将模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="54318-175">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="54318-176">`ID` 属性将成为对应于此类的数据库表中的主键。</span><span class="sxs-lookup"><span data-stu-id="54318-176">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="54318-177">默认情况下，EF 将会将名为 `ID` 或 `classnameID` 的属性解析为主键。</span><span class="sxs-lookup"><span data-stu-id="54318-177">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="54318-178">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="54318-178">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="54318-179">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="54318-179">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="54318-180">在这个案例下，`Student entity` 中的 `Enrollments` 属性会保留所有与 `Student` 实体相关的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="54318-180">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="54318-181">换而言之，如果在数据库中有两行描述同一个学生的修读情况 （两行的 StudentID 值相同，而且 StudentID 作为外键和某位学生的主键值相同）， `Student` 实体的 `Enrollments` 导航属性将包含那两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="54318-181">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="54318-182">如果导航属性可以具有多个实体 （如多对多或一对多关系），那么导航属性的类型必须是可以添加、 删除和更新条目的容器，如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="54318-182">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="54318-183">你可以指定 `ICollection<T>` 或实现该接口类型，如 `List<T>` 或 `HashSet<T>`。</span><span class="sxs-lookup"><span data-stu-id="54318-183">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="54318-184">如果指定 `ICollection<T>`，EF在默认情况下创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="54318-184">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="54318-185">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="54318-185">The Enrollment entity</span></span>

![修读实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="54318-187">在 *Models* 文件夹中，创建 *Enrollment.cs* 并且用以下代码替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="54318-187">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="54318-188">`EnrollmentID` 属性将被设为主键; 此实体使用 `classnameID` 模式而不是如 `Student` 实体那样直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="54318-188">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="54318-189">通常情况下，你选择一个主键模式，并在你的数据模型自始至终使用这种模式。</span><span class="sxs-lookup"><span data-stu-id="54318-189">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="54318-190">在这里，使用了两种不同的模式只是为了说明你可以使用任一模式来指定主键。</span><span class="sxs-lookup"><span data-stu-id="54318-190">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="54318-191">在 [后面的教程](inheritance.md)，你将了解到使用`ID`这种模式可以更轻松地在数据模型之间实现继承。</span><span class="sxs-lookup"><span data-stu-id="54318-191">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="54318-192">`Grade` 属性是 `enum`。</span><span class="sxs-lookup"><span data-stu-id="54318-192">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="54318-193">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="54318-193">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="54318-194">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="54318-194">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="54318-195">`StudentID` 属性是一个外键，`Student` 是与其且对应的导航属性。</span><span class="sxs-lookup"><span data-stu-id="54318-195">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="54318-196">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含单个 `Student` 实体 (与前面所看到的 `Student.Enrollments` 导航属性不同后，`Student`中可以容纳多个 `Enrollment` 实体)。</span><span class="sxs-lookup"><span data-stu-id="54318-196">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="54318-197">`CourseID` 属性是一个外键， `Course` 是与其对应的导航属性。</span><span class="sxs-lookup"><span data-stu-id="54318-197">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="54318-198">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="54318-198">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="54318-199">如果一个属性名为 `<navigation property name><primary key property name>`，Entity Framework 就会将这个属性解析为外键属性(例如， `Student` 实体的主键是`ID`， `Student` 是`Enrollment`的导航属性所以`Enrollment`实体中 `StudentID` 会被解析为外键)。</span><span class="sxs-lookup"><span data-stu-id="54318-199">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="54318-200">此外还可以将需要解析为外键的属性命名为 `<primary key property name>` (例如，`CourseID` 由于 `Course` 实体的主键所以 `CourseID` 也被解析为外键)。</span><span class="sxs-lookup"><span data-stu-id="54318-200">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="54318-201">Course 实体</span><span class="sxs-lookup"><span data-stu-id="54318-201">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="54318-203">在 *Models* 文件夹中，创建 *Course.cs* 并且用以下代码替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="54318-203">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="54318-204">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="54318-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="54318-205">一个 `Course` 体可以与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="54318-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="54318-206">我们在本系列 [后面的教程](complex-data-model.md) 中会有更多有关 `DatabaseGenerated` 特性的例子。</span><span class="sxs-lookup"><span data-stu-id="54318-206">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="54318-207">简单来说，此特性让你能自行指定主键，而不是让数据库自动指定主键。</span><span class="sxs-lookup"><span data-stu-id="54318-207">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="54318-208">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="54318-208">Create the database context</span></span>

<span data-ttu-id="54318-209">使得给定的数据模型与 Entity Framework 功能相协调的主类是数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="54318-209">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="54318-210">可以通过继承 `Microsoft.EntityFrameworkCore.DbContext` 类的方式创建此类。</span><span class="sxs-lookup"><span data-stu-id="54318-210">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="54318-211">在该类中你可以指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="54318-211">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="54318-212">你还可以定义某些 Entity Framework 行为。</span><span class="sxs-lookup"><span data-stu-id="54318-212">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="54318-213">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="54318-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="54318-214">在项目文件夹中，创建名为的文件夹 *Data*。</span><span class="sxs-lookup"><span data-stu-id="54318-214">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="54318-215">在 *Data* 文件夹创建名为 *SchoolContext.cs* 的类文件，并将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="54318-215">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="54318-216">此代码将为每个实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="54318-216">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="54318-217">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="54318-217">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="54318-218">在这里可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>` 语句，实现的功能没有任何改变。</span><span class="sxs-lookup"><span data-stu-id="54318-218">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="54318-219">Entity Framework 会隐式包含这两个实体因为 `Student` 实体引用了 `Enrollment` 实体、`Enrollment` 实体引用了 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="54318-219">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="54318-220">当数据库创建完成后， EF 创建一系列数据表，表名默认和 `DbSet` 属性名相同。</span><span class="sxs-lookup"><span data-stu-id="54318-220">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="54318-221">集合属性的名称一般使用复数形式，但不同的开发人员的命名习惯可能不一样，开发人员根据自己的情况确定是否使用复数形式。</span><span class="sxs-lookup"><span data-stu-id="54318-221">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="54318-222">在定义 DbSet 属性的代码之后，添加下面高亮代码，对 DbContext 指定单数的表名来覆盖默认的表名。</span><span class="sxs-lookup"><span data-stu-id="54318-222">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="54318-223">此教程在最后一个 DbSet 属性之后，添加以下高亮显示的代码。</span><span class="sxs-lookup"><span data-stu-id="54318-223">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="54318-224">注册 SchoolContext</span><span class="sxs-lookup"><span data-stu-id="54318-224">Register the SchoolContext</span></span>

<span data-ttu-id="54318-225">ASP.NET Core 默认实现 [依赖注入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="54318-225">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="54318-226">在应用程序启动过程通过依赖注入注册相关服务 （例如 EF 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="54318-226">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="54318-227">需要这些服务的组件 （如 MVC 控制器） 可以通过向构造函数添加相关参数来获得对应服务。</span><span class="sxs-lookup"><span data-stu-id="54318-227">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="54318-228">在本教程后面你将看到控制器构造函数的代码，就是通过上述方式获得上下文实例。</span><span class="sxs-lookup"><span data-stu-id="54318-228">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="54318-229">若要将 `SchoolContext` 注册为一种服务，打开 *Startup.cs* ，并将高亮代码添加到 `ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="54318-229">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="54318-230">通过调用 `DbContextOptionsBuilder` 中的一个方法将数据库连接字符串在配置文件中的名称传递给上下文对象。</span><span class="sxs-lookup"><span data-stu-id="54318-230">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="54318-231">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="54318-231">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="54318-232">添加 `using` 语句引用 `ContosoUniversity.Data` 和 `Microsoft.EntityFrameworkCore` 命名空间，然后生成项目。</span><span class="sxs-lookup"><span data-stu-id="54318-232">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="54318-233">打开 appsettings.json 文件，并如以下示例所示添加连接字符串。</span><span class="sxs-lookup"><span data-stu-id="54318-233">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="54318-234">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="54318-234">SQL Server Express LocalDB</span></span>

<span data-ttu-id="54318-235">数据库连接字符串指定使用 SQL Server LocalDB 数据库。</span><span class="sxs-lookup"><span data-stu-id="54318-235">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="54318-236">LocalDB 是 SQL Server Express 数据库引擎的轻量级版本，用于应用程序开发，不在生产环境中使用。</span><span class="sxs-lookup"><span data-stu-id="54318-236">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="54318-237">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="54318-237">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="54318-238">默认情况下， LocalDB 在 `C:/Users/<user>` 目录下创建 *.mdf* 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="54318-238">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="54318-239">使用测试数据初始化数据库</span><span class="sxs-lookup"><span data-stu-id="54318-239">Initialize DB with test data</span></span>

<span data-ttu-id="54318-240">Entity Framework 已经为你创建了一个空数据库。</span><span class="sxs-lookup"><span data-stu-id="54318-240">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="54318-241">在本部分中，你将编写一个方法用于向数据库填充测试数据，该方法会在数据库创建完成之后执行。</span><span class="sxs-lookup"><span data-stu-id="54318-241">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="54318-242">此处将使用 `EnsureCreated` 方法来自动创建数据库。</span><span class="sxs-lookup"><span data-stu-id="54318-242">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="54318-243">在 [后面的教程](migrations.md) 你将了解如何通过使用 Code First Migration 来更改而不是删除并重新创建数据库来处理模型更改。</span><span class="sxs-lookup"><span data-stu-id="54318-243">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="54318-244">在 *Data* 文件夹中，创建名为的新类文件 *DbInitializer.cs* 并且将模板代码替换为以下代码，使得在需要时能创建数据库并向其填充测试数据。</span><span class="sxs-lookup"><span data-stu-id="54318-244">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="54318-245">这段代码首先检查是否有学生数据在数据库中，如果没有的话，就可以假定数据库是新建的，然后使用测试数据进行填充。</span><span class="sxs-lookup"><span data-stu-id="54318-245">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="54318-246">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="54318-246">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="54318-247">在 *Program.cs*，修改 `Main` 方法，使得在应用程序启动时能执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="54318-247">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="54318-248">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="54318-248">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="54318-249">调用 seed 方法，将上下文传递给它。</span><span class="sxs-lookup"><span data-stu-id="54318-249">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="54318-250">Seed 方法完成此操作时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="54318-250">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="54318-251">添加 `using` 语句：</span><span class="sxs-lookup"><span data-stu-id="54318-251">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="54318-252">在旧版教程中，你可能会在 *Startup.cs* 中的 `Configure` 方法看到类似的代码。</span><span class="sxs-lookup"><span data-stu-id="54318-252">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="54318-253">我们建议你只在为了设置请求管道时使用 `Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="54318-253">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="54318-254">将应用程序启动代码放入 `Main` 方法。</span><span class="sxs-lookup"><span data-stu-id="54318-254">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="54318-255">现在首次运行该应用程序，创建数据库并使用测试数据作为种子数据。</span><span class="sxs-lookup"><span data-stu-id="54318-255">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="54318-256">每当你更改数据模型时，可以删除数据库、 更新你的 Initialize 方法，然后使用上述方式更新新数据库。</span><span class="sxs-lookup"><span data-stu-id="54318-256">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="54318-257">在之后的教程中，你将了解如何在数据模型更改时，只需修改数据库而无需删除重建数据库。</span><span class="sxs-lookup"><span data-stu-id="54318-257">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="54318-258">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="54318-258">Create controller and views</span></span>

<span data-ttu-id="54318-259">接下来，将使用 Visual Studio 中的基架引擎添加一个 MVC 控制器，以及使用 EF 来查询和保存数据的视图。</span><span class="sxs-lookup"><span data-stu-id="54318-259">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="54318-260">CRUD 操作方法和视图的自动创建被称为基架。</span><span class="sxs-lookup"><span data-stu-id="54318-260">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="54318-261">基架与代码生成不同，基架的代码是一个起点，可以修改基架以满足自己需求，而你通常无需修改生成的代码。</span><span class="sxs-lookup"><span data-stu-id="54318-261">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="54318-262">当你需要自定义生成代码时，可使用一部分类或需求发生变化时重新生成代码。</span><span class="sxs-lookup"><span data-stu-id="54318-262">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="54318-263">右键单击 **解决方案资源管理器** 中的 **Controllers** 文件夹选择 **添加 > 新搭建基架的项目**。</span><span class="sxs-lookup"><span data-stu-id="54318-263">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="54318-264">在“添加基架”对话框中：</span><span class="sxs-lookup"><span data-stu-id="54318-264">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="54318-265">选择 **视图使用 Entity Framework 的 MVC 控制器**。</span><span class="sxs-lookup"><span data-stu-id="54318-265">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="54318-266">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="54318-266">Click **Add**.</span></span> <span data-ttu-id="54318-267">随即将显示“使用 Entity Framework 添加包含视图的 MVC 控制器”对话框。</span><span class="sxs-lookup"><span data-stu-id="54318-267">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![构架 Student](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="54318-269">在 **模型类** 选择 **Student**。</span><span class="sxs-lookup"><span data-stu-id="54318-269">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="54318-270">在“数据上下文类”中选择 SchoolContext。</span><span class="sxs-lookup"><span data-stu-id="54318-270">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="54318-271">使用 **StudentsController** 作为默认名称。</span><span class="sxs-lookup"><span data-stu-id="54318-271">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="54318-272">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="54318-272">Click **Add**.</span></span>

  <span data-ttu-id="54318-273">当你单击 **添加** 后，Visual Studio 基架引擎创建 *StudentsController.cs* 文件和一组对应于控制器的视图 (*.cshtml* 文件) 。</span><span class="sxs-lookup"><span data-stu-id="54318-273">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="54318-274">（如果你之前手动创建数据库上下文，基架引擎还可以自动创建。</span><span class="sxs-lookup"><span data-stu-id="54318-274">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="54318-275">你可以在 **添加控制器** 对话框中单击右侧的加号框 **数据上下文类** 来指定在一个新上下文类。</span><span class="sxs-lookup"><span data-stu-id="54318-275">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="54318-276">然后，Visual Studio 将创建你的 `DbContext`，控制器和视图类。）</span><span class="sxs-lookup"><span data-stu-id="54318-276">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="54318-277">注意控制器将 `SchoolContext` 作为构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="54318-277">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="54318-278">ASP.NET Core 依赖关系注入负责将 `SchoolContext` 实例传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="54318-278">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="54318-279">在前面的教程中已经通过修改 *Startup.cs* 文件来配置注入规则。</span><span class="sxs-lookup"><span data-stu-id="54318-279">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="54318-280">控制器包含 `Index` 操作方法，用于显示数据库中的所有学生。</span><span class="sxs-lookup"><span data-stu-id="54318-280">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="54318-281">该方法从学生实体集中获取学生列表，学生实体集则是通过读取数据库上下文实例中的 `Students` 属性获得：</span><span class="sxs-lookup"><span data-stu-id="54318-281">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="54318-282">本教程后面部分将介绍此代码中的异步编程元素。</span><span class="sxs-lookup"><span data-stu-id="54318-282">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="54318-283">*Views/Students/Index.cshtml* 视图使用table标签显示此列表：</span><span class="sxs-lookup"><span data-stu-id="54318-283">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="54318-284">按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)**。</span><span class="sxs-lookup"><span data-stu-id="54318-284">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="54318-285">单击学生选项卡以查看 `DbInitializer.Initialize` 插入的测试的数据。</span><span class="sxs-lookup"><span data-stu-id="54318-285">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="54318-286">你将看到 `Students` 选项卡链接在页的顶部或在单击右上角后的导航图标中，具体显示在哪里取决于浏览器窗口宽度。</span><span class="sxs-lookup"><span data-stu-id="54318-286">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University 主页宽窄](intro/_static/home-page-narrow.png)

![“学生索引”页](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="54318-289">查看数据库</span><span class="sxs-lookup"><span data-stu-id="54318-289">View the database</span></span>

<span data-ttu-id="54318-290">当你启动了应用程序，`DbInitializer.Initialize` 方法调用 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="54318-290">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="54318-291">EF 没有检测到相关数据库，因此自己创建了一个，接着 `Initialize` 方法的其余代码向数据库中填充数据。</span><span class="sxs-lookup"><span data-stu-id="54318-291">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="54318-292">你可以使用 Visual Studio 中的 **SQL Server 对象资源管理器** (SSOX) 查看数据库。</span><span class="sxs-lookup"><span data-stu-id="54318-292">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="54318-293">关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="54318-293">Close the browser.</span></span>

<span data-ttu-id="54318-294">如果 SSOX 窗口尚未打开，请从Visual Studio 中的**视图** 菜单中选择。</span><span class="sxs-lookup"><span data-stu-id="54318-294">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="54318-295">在 SSOX 中，单击 **(localdb) \MSSQLLocalDB > 数据库**，然后单击和 *appsettings.json* 文件中的连接字符串对应的数据库。</span><span class="sxs-lookup"><span data-stu-id="54318-295">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="54318-296">展开“表”节点，查看数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="54318-296">Expand the **Tables** node to see the tables in your database.</span></span>

![SSOX 中的表](intro/_static/ssox-tables.png)

<span data-ttu-id="54318-298">右键单击 **Student** 表，然后单击 **查看数据**，即可查看已创建的列和已插入到表的行。</span><span class="sxs-lookup"><span data-stu-id="54318-298">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX 中的 Student 表](intro/_static/ssox-student-table.png)

<span data-ttu-id="54318-300">.mdf 和 .ldf 数据库文件位于 C:\Users\\\<yourusername> 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="54318-300">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="54318-301">因为调用 `EnsureCreated` 的初始化方法在启动应用程序时才运行，所以在这之前你可以更改 `Student` 类、 删除数据库、 再运行一次应用程序，这时候数据库将自动重新创建，以匹配所做的更改。</span><span class="sxs-lookup"><span data-stu-id="54318-301">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="54318-302">例如，如果向 `Student` 类添加 `EmailAddress` 属性，重新的创建表中会有 `EmailAddress` 列。</span><span class="sxs-lookup"><span data-stu-id="54318-302">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="54318-303">约定</span><span class="sxs-lookup"><span data-stu-id="54318-303">Conventions</span></span>

<span data-ttu-id="54318-304">由于 Entity Framwork 有一定的约束条件，你只需要按规则编写很少的代码就能够创建一个完整的数据库。</span><span class="sxs-lookup"><span data-stu-id="54318-304">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="54318-305">`DbSet` 类型的属性用作表名。</span><span class="sxs-lookup"><span data-stu-id="54318-305">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="54318-306">如果实体未被 `DbSet` 属性引用，实体类名称用作表名称。</span><span class="sxs-lookup"><span data-stu-id="54318-306">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="54318-307">使用实体属性名作为列名。</span><span class="sxs-lookup"><span data-stu-id="54318-307">Entity property names are used for column names.</span></span>

* <span data-ttu-id="54318-308">以 ID 或 classnameID 命名的实体属性被视为主键属性。</span><span class="sxs-lookup"><span data-stu-id="54318-308">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="54318-309">如果属性名为 \<导航属性名>\<主键属性名>（例如，`StudentID` 对应 `Student` 导航属性，因为 `Student` 实体的主键是 `ID`），其将被解释为外键属性。</span><span class="sxs-lookup"><span data-stu-id="54318-309">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="54318-310">此外还可以将外键属性仅命名为 \<主键属性名>（例如 `EnrollmentID`，因为 `Enrollment` 实体的主键为 `EnrollmentID`）。</span><span class="sxs-lookup"><span data-stu-id="54318-310">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="54318-311">约定行为可以重写。</span><span class="sxs-lookup"><span data-stu-id="54318-311">Conventional behavior can be overridden.</span></span> <span data-ttu-id="54318-312">例如，本教程前半部分显式指定表名称。</span><span class="sxs-lookup"><span data-stu-id="54318-312">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="54318-313">本系列 [后面教程](complex-data-model.md) 则设置列名称并将任何属性设置为主键或外键。</span><span class="sxs-lookup"><span data-stu-id="54318-313">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="54318-314">异步代码</span><span class="sxs-lookup"><span data-stu-id="54318-314">Asynchronous code</span></span>

<span data-ttu-id="54318-315">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="54318-315">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="54318-316">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="54318-316">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="54318-317">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="54318-317">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="54318-318">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="54318-318">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="54318-319">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="54318-319">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="54318-320">因此，异步代码使得服务器更有效地使用资源，并且该服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="54318-320">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="54318-321">异步代码在运行时，会引入的少量开销，在低流量时对性能的影响可以忽略不计，但在针对高流量情况下潜在的性能提升是可观的。</span><span class="sxs-lookup"><span data-stu-id="54318-321">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="54318-322">在以下代码中，`async` 关键字、`Task<T>` 返回值、`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="54318-322">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="54318-323">`async` 关键字用于告知编译器该方法主体将生成回调并自动创建 `Task<IActionResult>` 返回对象。</span><span class="sxs-lookup"><span data-stu-id="54318-323">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="54318-324">返回类型 `Task<IActionResult>` 表示正在进行的工作返回的结果为 `IActionResult` 类型。</span><span class="sxs-lookup"><span data-stu-id="54318-324">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="54318-325">`await` 关键字会使得编译器将方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="54318-325">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="54318-326">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="54318-326">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="54318-327">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="54318-327">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="54318-328">`ToListAsync` 是 `ToList` 方法的的异步扩展版本。</span><span class="sxs-lookup"><span data-stu-id="54318-328">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="54318-329">使用 Entity Framework 编写异步代码时的一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="54318-329">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="54318-330">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="54318-330">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="54318-331">包括 `ToListAsync`， `SingleOrDefaultAsync`，和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="54318-331">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="54318-332">不包括只需更改 `IQueryable` 的语句，如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="54318-332">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="54318-333">EF 上下文是线程不安全的： 请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="54318-333">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="54318-334">当调用异步 EF 方法时，始终使用 `await` 关键字。</span><span class="sxs-lookup"><span data-stu-id="54318-334">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="54318-335">如果你想要利用异步代码的性能优势，请确保你所使用的任何库和包在它们调用导致 Entity Framework 数据库查询方法时也使用异步。</span><span class="sxs-lookup"><span data-stu-id="54318-335">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="54318-336">有关在 .NET 异步编程的详细信息，请参阅 [异步概述](/dotnet/articles/standard/async)。</span><span class="sxs-lookup"><span data-stu-id="54318-336">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="54318-337">获取代码</span><span class="sxs-lookup"><span data-stu-id="54318-337">Get the code</span></span>

[<span data-ttu-id="54318-338">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="54318-338">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="54318-339">后续步骤</span><span class="sxs-lookup"><span data-stu-id="54318-339">Next steps</span></span>

<span data-ttu-id="54318-340">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="54318-340">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="54318-341">已创建 ASP.NET Core MVC Web 应用</span><span class="sxs-lookup"><span data-stu-id="54318-341">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="54318-342">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="54318-342">Set up the site style</span></span>
> * <span data-ttu-id="54318-343">已了解 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="54318-343">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="54318-344">已创建数据模型</span><span class="sxs-lookup"><span data-stu-id="54318-344">Created the data model</span></span>
> * <span data-ttu-id="54318-345">已创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="54318-345">Created the database context</span></span>
> * <span data-ttu-id="54318-346">已注册 SchoolContext</span><span class="sxs-lookup"><span data-stu-id="54318-346">Registered the SchoolContext</span></span>
> * <span data-ttu-id="54318-347">已使用测试数据初始化数据库</span><span class="sxs-lookup"><span data-stu-id="54318-347">Initialized DB with test data</span></span>
> * <span data-ttu-id="54318-348">已创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="54318-348">Created controller and views</span></span>
> * <span data-ttu-id="54318-349">已查看数据库</span><span class="sxs-lookup"><span data-stu-id="54318-349">Viewed the database</span></span>

<span data-ttu-id="54318-350">在下一个教程中，你将学习如何执行基本的 CRUD （创建、 读取、 更新、 删除） 操作。</span><span class="sxs-lookup"><span data-stu-id="54318-350">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="54318-351">请继续阅读下一篇教程，了解如何执行基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="54318-351">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="54318-352">实现基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="54318-352">Implement basic CRUD functionality</span></span>](crud.md)
