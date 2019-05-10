---
title: ASP.NET Core 中的 Razor 页面和 Entity Framework Core - 第 1 个教程（共 8 个）
author: rick-anderson
description: 介绍了如何使用 Entity Framework Core 创建 Razor 页面应用
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/22/2018
uid: data/ef-rp/intro
ms.openlocfilehash: b16d1b67b3b501811ab34330851e6cacc76f2ed0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886402"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="8b78b-103">ASP.NET Core 中的 Razor 页面和 Entity Framework Core - 第 1 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="8b78b-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8b78b-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8b78b-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8b78b-105">Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 创建 ASP.NET Core Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="8b78b-105">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="8b78b-106">该示例应用是一个虚构的 Contoso University 的网站。</span><span class="sxs-lookup"><span data-stu-id="8b78b-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="8b78b-107">其中包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="8b78b-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="8b78b-108">本页是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。</span><span class="sxs-lookup"><span data-stu-id="8b78b-108">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="8b78b-109">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="8b78b-109">Download or view the completed app.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="8b78b-110">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-110">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8b78b-111">系统必备</span><span class="sxs-lookup"><span data-stu-id="8b78b-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b78b-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b78b-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8b78b-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b78b-113">.NET Core CLI</span></span>](#tab/netcore-cli)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="8b78b-114">熟悉 [Razor 页面](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-114">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="8b78b-115">新程序员在开始学习本系列之前，应先完成 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-115">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8b78b-116">疑难解答</span><span class="sxs-lookup"><span data-stu-id="8b78b-116">Troubleshooting</span></span>

<span data-ttu-id="8b78b-117">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="8b78b-117">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="8b78b-118">获取帮助的一个好方法是将问题发布到适用于 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 的 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-118">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="8b78b-119">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="8b78b-119">The Contoso University web app</span></span>

<span data-ttu-id="8b78b-120">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="8b78b-120">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="8b78b-121">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="8b78b-121">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="8b78b-122">以下是在本教程中创建的几个屏幕。</span><span class="sxs-lookup"><span data-stu-id="8b78b-122">Here are a few of the screens created in the tutorial.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

<span data-ttu-id="8b78b-125">此网站的 UI 样式与内置模板生成的 UI 样式类似。</span><span class="sxs-lookup"><span data-stu-id="8b78b-125">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="8b78b-126">教程的重点是 EF Core 和 Razor 页面，而非 UI。</span><span class="sxs-lookup"><span data-stu-id="8b78b-126">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="8b78b-127">创建 ContosoUniversity Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="8b78b-127">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b78b-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b78b-128">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b78b-129">从 Visual Studio“文件”菜单中，选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-129">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8b78b-130">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="8b78b-130">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="8b78b-131">将该项目命名为 ContosoUniversity 。</span><span class="sxs-lookup"><span data-stu-id="8b78b-131">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="8b78b-132">务必将该项目命名为 ContosoUniversity，以便复制/粘贴代码时命名空间相匹配。</span><span class="sxs-lookup"><span data-stu-id="8b78b-132">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="8b78b-133">在下拉列表中选择“ASP.NET Core 2.1”，然后选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-133">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="8b78b-134">有关上述步骤的图像，请参阅[创建 Razor Web 应用](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-134">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="8b78b-135">运行应用。</span><span class="sxs-lookup"><span data-stu-id="8b78b-135">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8b78b-136">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b78b-136">.NET Core CLI</span></span>](#tab/netcore-cli)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="8b78b-137">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="8b78b-137">Set up the site style</span></span>

<span data-ttu-id="8b78b-138">设置网站菜单、布局和主页时需作少量更改。</span><span class="sxs-lookup"><span data-stu-id="8b78b-138">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="8b78b-139">进行以下更改以更新 Pages/Shared/_Layout.cshtml：</span><span class="sxs-lookup"><span data-stu-id="8b78b-139">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="8b78b-140">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="8b78b-140">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="8b78b-141">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="8b78b-141">There are three occurrences.</span></span>

* <span data-ttu-id="8b78b-142">添加菜单项 **Students**，**Courses**，**Instructors**，和 **Department**，并删除 **Contact**菜单项。</span><span class="sxs-lookup"><span data-stu-id="8b78b-142">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="8b78b-143">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="8b78b-143">The changes are highlighted.</span></span> <span data-ttu-id="8b78b-144">（所有标记均不显示。）</span><span class="sxs-lookup"><span data-stu-id="8b78b-144">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="8b78b-145">在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET 和 MVC 的文本替换为有关本应用的文本：</span><span class="sxs-lookup"><span data-stu-id="8b78b-145">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="8b78b-146">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="8b78b-146">Create the data model</span></span>

<span data-ttu-id="8b78b-147">创建 Contoso University 应用的实体类。</span><span class="sxs-lookup"><span data-stu-id="8b78b-147">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="8b78b-148">从以下三个实体开始：</span><span class="sxs-lookup"><span data-stu-id="8b78b-148">Start with the following three entities:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="8b78b-150">`Student` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="8b78b-150">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="8b78b-151">`Course` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="8b78b-151">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="8b78b-152">一名学生可以报名参加任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="8b78b-152">A student can enroll in any number of courses.</span></span> <span data-ttu-id="8b78b-153">一门课程中可以包含任意数量的学生。</span><span class="sxs-lookup"><span data-stu-id="8b78b-153">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="8b78b-154">以下部分将为这几个实体中的每一个实体创建一个类。</span><span class="sxs-lookup"><span data-stu-id="8b78b-154">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="8b78b-155">Student 实体</span><span class="sxs-lookup"><span data-stu-id="8b78b-155">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="8b78b-157">创建 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="8b78b-157">Create a *Models* folder.</span></span> <span data-ttu-id="8b78b-158">在 Models 文件夹中，使用以下代码创建一个名为 Student.cs 的类文件：</span><span class="sxs-lookup"><span data-stu-id="8b78b-158">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="8b78b-159">`ID` 属性成为此类对应的数据库 (DB) 表的主键列。</span><span class="sxs-lookup"><span data-stu-id="8b78b-159">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="8b78b-160">默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。</span><span class="sxs-lookup"><span data-stu-id="8b78b-160">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="8b78b-161">在 `classnameID` 中，`classname` 为类名称。</span><span class="sxs-lookup"><span data-stu-id="8b78b-161">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="8b78b-162">另一种自动识别的主键是上例中的 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-162">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="8b78b-163">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-163">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="8b78b-164">导航属性链接到与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="8b78b-164">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="8b78b-165">在这种情况下，`Student entity` 的 `Enrollments` 属性包含与该 `Student` 相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="8b78b-165">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="8b78b-166">例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="8b78b-166">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="8b78b-167">相关的 `Enrollment` 行是 `StudentID` 列中包含该学生的主键值的行。</span><span class="sxs-lookup"><span data-stu-id="8b78b-167">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="8b78b-168">例如，假设 ID=1 的学生在 `Enrollment` 表中有两行。</span><span class="sxs-lookup"><span data-stu-id="8b78b-168">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="8b78b-169">`Enrollment` 表中有两行的 `StudentID` = 1。</span><span class="sxs-lookup"><span data-stu-id="8b78b-169">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="8b78b-170">`StudentID` 是 `Enrollment` 表中的外键，用于指定 `Student` 表中的学生。</span><span class="sxs-lookup"><span data-stu-id="8b78b-170">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="8b78b-171">如果导航属性包含多个实体，则导航属性必须是列表类型，例如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-171">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="8b78b-172">可以指定 `ICollection<T>` 或诸如 `List<T>` 或 `HashSet<T>` 的类型。</span><span class="sxs-lookup"><span data-stu-id="8b78b-172">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="8b78b-173">使用 `ICollection<T>` 时，EF Core 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="8b78b-173">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="8b78b-174">包含多个实体的导航属性来自于多对多和一对多关系。</span><span class="sxs-lookup"><span data-stu-id="8b78b-174">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="8b78b-175">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="8b78b-175">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="8b78b-177">在 Models 文件夹中，使用以下代码创建 Enrollment.cs：</span><span class="sxs-lookup"><span data-stu-id="8b78b-177">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="8b78b-178">`EnrollmentID` 属性为主键。</span><span class="sxs-lookup"><span data-stu-id="8b78b-178">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="8b78b-179">`Student` 实体使用的是 `ID` 模式，而本实体使用的是 `classnameID` 模式。</span><span class="sxs-lookup"><span data-stu-id="8b78b-179">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="8b78b-180">通常情况下，开发者会选择一种模式并在整个数据模型中都使用该模式。</span><span class="sxs-lookup"><span data-stu-id="8b78b-180">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="8b78b-181">下一个教程将介绍如何使用不带类名的 ID，以便更轻松地在数据模型中实现集成。</span><span class="sxs-lookup"><span data-stu-id="8b78b-181">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="8b78b-182">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="8b78b-183">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="8b78b-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="8b78b-184">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="8b78b-184">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="8b78b-185">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="8b78b-186">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="8b78b-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="8b78b-187">`Student` 实体与 `Student.Enrollments` 导航属性不同，后者包含多个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="8b78b-187">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="8b78b-188">`CourseID` 属性是外键，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="8b78b-189">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="8b78b-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="8b78b-190">如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。</span><span class="sxs-lookup"><span data-stu-id="8b78b-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="8b78b-191">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的主键为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-191">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="8b78b-192">还可以将外键属性命名为 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="8b78b-193">例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="8b78b-194">Course 实体</span><span class="sxs-lookup"><span data-stu-id="8b78b-194">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="8b78b-196">在 Models 文件夹中，使用以下代码创建 Course.cs：</span><span class="sxs-lookup"><span data-stu-id="8b78b-196">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="8b78b-197">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="8b78b-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="8b78b-198">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="8b78b-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="8b78b-199">应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。</span><span class="sxs-lookup"><span data-stu-id="8b78b-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="8b78b-200">为“学生”模型搭建基架</span><span class="sxs-lookup"><span data-stu-id="8b78b-200">Scaffold the student model</span></span>

<span data-ttu-id="8b78b-201">本部分将为“学生”模型搭建基架。</span><span class="sxs-lookup"><span data-stu-id="8b78b-201">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="8b78b-202">确切地说，基架工具将生成页面，用于对“学生”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="8b78b-202">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="8b78b-203">生成项目。</span><span class="sxs-lookup"><span data-stu-id="8b78b-203">Build the project.</span></span>
* <span data-ttu-id="8b78b-204">创建 Pages/Students 文件夹。</span><span class="sxs-lookup"><span data-stu-id="8b78b-204">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b78b-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b78b-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8b78b-206">在“解决方案资源管理器”中，右键单击“Pages/Students”文件夹>“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-206">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="8b78b-207">在“添加基架”对话框中，选择“使用实体框架生成 Razor Pages (CRUD)”>“添加”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-207">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="8b78b-208">完成“使用实体框架(CRUD)添加 Razor Pages”对话框：</span><span class="sxs-lookup"><span data-stu-id="8b78b-208">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8b78b-209">在“模型类”下拉列表中，选择“学生(ContosoUniversity.Models)”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-209">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="8b78b-210">在“数据上下文类”行中，选择加号 (+) 并将生成的名称更改为 ContosoUniversity.Models.SchoolContext。</span><span class="sxs-lookup"><span data-stu-id="8b78b-210">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="8b78b-211">在“数据上下文类”下拉列表中，选择“ContosoUniversity.Models.SchoolContext”</span><span class="sxs-lookup"><span data-stu-id="8b78b-211">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="8b78b-212">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-212">Select **Add**.</span></span>

![CRUD 对话框](intro/_static/s1.png)

<span data-ttu-id="8b78b-214">如果对前面的步骤有疑问，请参阅[搭建“电影”模型的基架](xref:tutorials/razor-pages/model#scaffold-the-movie-model)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-214">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8b78b-215">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b78b-215">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8b78b-216">运行以下命令，搭建“学生”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="8b78b-216">Run the following commands to scaffold the student model.</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="8b78b-217">搭建基架的过程会创建并更改以下文件：</span><span class="sxs-lookup"><span data-stu-id="8b78b-217">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="8b78b-218">创建的文件</span><span class="sxs-lookup"><span data-stu-id="8b78b-218">Files created</span></span>

* <span data-ttu-id="8b78b-219">Pages/Students：“创建”、“删除”、“详细信息”、“编辑”、“索引”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-219">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="8b78b-220">Data/SchoolContext.cs</span><span class="sxs-lookup"><span data-stu-id="8b78b-220">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="8b78b-221">文件更新</span><span class="sxs-lookup"><span data-stu-id="8b78b-221">File updates</span></span>

* <span data-ttu-id="8b78b-222">*Startup.cs*：下一部分详细介绍对此文件所作的更改。</span><span class="sxs-lookup"><span data-stu-id="8b78b-222">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="8b78b-223">*appsettings.json*：添加用于连接到本地数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8b78b-223">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8b78b-224">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="8b78b-224">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8b78b-225">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="8b78b-225">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8b78b-226">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="8b78b-226">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8b78b-227">需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="8b78b-227">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8b78b-228">本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="8b78b-228">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8b78b-229">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="8b78b-229">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8b78b-230">在 Startup.cs 中检查 `ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b78b-230">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="8b78b-231">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="8b78b-231">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="8b78b-232">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="8b78b-232">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8b78b-233">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8b78b-233">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="8b78b-234">更新 main</span><span class="sxs-lookup"><span data-stu-id="8b78b-234">Update main</span></span>

<span data-ttu-id="8b78b-235">在 Program.cs 中，修改 `Main` 方法以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8b78b-235">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="8b78b-236">从依赖关系注入容器获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="8b78b-236">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="8b78b-237">调用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-237">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="8b78b-238">`EnsureCreated` 方法完成时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="8b78b-238">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="8b78b-239">下面的代码显示更新后的 Program.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="8b78b-239">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="8b78b-240">`EnsureCreated` 确保存在上下文数据库。</span><span class="sxs-lookup"><span data-stu-id="8b78b-240">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="8b78b-241">如果存在，则不需要任何操作。</span><span class="sxs-lookup"><span data-stu-id="8b78b-241">If it exists, no action is taken.</span></span> <span data-ttu-id="8b78b-242">如果不存在，则会创建数据库及其所有架构。</span><span class="sxs-lookup"><span data-stu-id="8b78b-242">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="8b78b-243">`EnsureCreated` 不使用迁移创建数据库。</span><span class="sxs-lookup"><span data-stu-id="8b78b-243">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="8b78b-244">使用 `EnsureCreated` 创建的数据库稍后无法使用迁移更新。</span><span class="sxs-lookup"><span data-stu-id="8b78b-244">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="8b78b-245">启动应用时会调用 `EnsureCreated`，以进行以下工作流：</span><span class="sxs-lookup"><span data-stu-id="8b78b-245">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="8b78b-246">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="8b78b-246">Delete the DB.</span></span>
* <span data-ttu-id="8b78b-247">更改数据库架构（例如添加一个 `EmailAddress` 字段）。</span><span class="sxs-lookup"><span data-stu-id="8b78b-247">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="8b78b-248">运行应用。</span><span class="sxs-lookup"><span data-stu-id="8b78b-248">Run the app.</span></span>
* <span data-ttu-id="8b78b-249">`EnsureCreated` 创建一个带有 `EmailAddress` 列的数据库。</span><span class="sxs-lookup"><span data-stu-id="8b78b-249">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="8b78b-250">架构快速演变时，在开发初期使用 `EnsureCreated` 很方便。</span><span class="sxs-lookup"><span data-stu-id="8b78b-250">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="8b78b-251">本教程后面将删除 DB 并使用迁移。</span><span class="sxs-lookup"><span data-stu-id="8b78b-251">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="8b78b-252">测试应用</span><span class="sxs-lookup"><span data-stu-id="8b78b-252">Test the app</span></span>

<span data-ttu-id="8b78b-253">运行应用并接受 cookie 策略。</span><span class="sxs-lookup"><span data-stu-id="8b78b-253">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="8b78b-254">此应用不保留个人信息。</span><span class="sxs-lookup"><span data-stu-id="8b78b-254">This app doesn't keep personal information.</span></span> <span data-ttu-id="8b78b-255">有关 cookie 策略的信息，请参阅[欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-255">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="8b78b-256">依次选择“学生”链接、“新建”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-256">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="8b78b-257">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="8b78b-257">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="8b78b-258">检查 SchoolContext DB 上下文</span><span class="sxs-lookup"><span data-stu-id="8b78b-258">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="8b78b-259">数据库上下文类是为给定数据模型协调 EF Core 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="8b78b-259">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="8b78b-260">数据上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-260">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8b78b-261">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="8b78b-261">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="8b78b-262">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="8b78b-263">使用以下代码更新 SchoolContext.cs：</span><span class="sxs-lookup"><span data-stu-id="8b78b-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="8b78b-264">突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="8b78b-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="8b78b-265">在 EF Core 术语中：</span><span class="sxs-lookup"><span data-stu-id="8b78b-265">In EF Core terminology:</span></span>

* <span data-ttu-id="8b78b-266">实体集通常对应一个数据库表。</span><span class="sxs-lookup"><span data-stu-id="8b78b-266">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="8b78b-267">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="8b78b-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8b78b-268">可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-268">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="8b78b-269">EF Core 隐式包含了它们，因为 `Student` 实体引用 `Enrollment` 实体，而 `Enrollment` 实体引用 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="8b78b-269">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="8b78b-270">在本教程中，将 `DbSet<Enrollment>` 和 `DbSet<Course>` 保留在 `SchoolContext` 中。</span><span class="sxs-lookup"><span data-stu-id="8b78b-270">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="8b78b-271">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="8b78b-271">SQL Server Express LocalDB</span></span>

<span data-ttu-id="8b78b-272">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-272">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="8b78b-273">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="8b78b-273">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="8b78b-274">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="8b78b-274">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="8b78b-275">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="8b78b-275">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="8b78b-276">添加代码，以使用测试数据初始化该数据库</span><span class="sxs-lookup"><span data-stu-id="8b78b-276">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="8b78b-277">EF Core 会创建一个空的数据库。</span><span class="sxs-lookup"><span data-stu-id="8b78b-277">EF Core creates an empty DB.</span></span> <span data-ttu-id="8b78b-278">本部分中编写了 `Initialize` 方法来使用测试数据填充该数据库。</span><span class="sxs-lookup"><span data-stu-id="8b78b-278">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="8b78b-279">在 Data 文件夹中，新建一个名为 DbInitializer.cs 的类文件，并添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="8b78b-279">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="8b78b-280">注意:上面的代码对命名空间使用 `Models` (`namespace ContosoUniversity.Models`)，而不是 `Data`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-280">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="8b78b-281">`Models` 与基架生成的代码一致。</span><span class="sxs-lookup"><span data-stu-id="8b78b-281">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="8b78b-282">有关详细信息，请参阅[此 GitHub 基架问题](https://github.com/aspnet/Scaffolding/issues/822)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-282">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="8b78b-283">该代码会检查数据库中是否存在任何学生。</span><span class="sxs-lookup"><span data-stu-id="8b78b-283">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="8b78b-284">如果 DB 中没有任何学生，则会使用测试数据初始化该 DB。</span><span class="sxs-lookup"><span data-stu-id="8b78b-284">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="8b78b-285">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="8b78b-285">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="8b78b-286">`EnsureCreated` 方法自动为数据库上下文创建数据库。</span><span class="sxs-lookup"><span data-stu-id="8b78b-286">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="8b78b-287">如果数据库已存在，则返回 `EnsureCreated`，并且不修改数据库。</span><span class="sxs-lookup"><span data-stu-id="8b78b-287">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="8b78b-288">在 Program.cs 中，将 `Main` 方法修改为调用 `Initialize`：</span><span class="sxs-lookup"><span data-stu-id="8b78b-288">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

<span data-ttu-id="8b78b-289">删除任何学生记录并重启应用。</span><span class="sxs-lookup"><span data-stu-id="8b78b-289">Delete any student records and restart the app.</span></span> <span data-ttu-id="8b78b-290">如果未初始化 DB，则在 `Initialize` 中设置断点以诊断问题。</span><span class="sxs-lookup"><span data-stu-id="8b78b-290">If the DB is not initialized, set a break point in `Initialize` to diagnose the problem.</span></span>

## <a name="view-the-db"></a><span data-ttu-id="8b78b-291">查看数据库</span><span class="sxs-lookup"><span data-stu-id="8b78b-291">View the DB</span></span>

<span data-ttu-id="8b78b-292">数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。</span><span class="sxs-lookup"><span data-stu-id="8b78b-292">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="8b78b-293">因此，数据库名称为“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-293">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="8b78b-294">GUID 因用户而异。</span><span class="sxs-lookup"><span data-stu-id="8b78b-294">The GUID will be different for each user.</span></span>
<span data-ttu-id="8b78b-295">从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-295">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="8b78b-296">在 SSOX 中，依次单击“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”。</span><span class="sxs-lookup"><span data-stu-id="8b78b-296">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="8b78b-297">展开“表”节点。</span><span class="sxs-lookup"><span data-stu-id="8b78b-297">Expand the **Tables** node.</span></span>

<span data-ttu-id="8b78b-298">右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行。</span><span class="sxs-lookup"><span data-stu-id="8b78b-298">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="8b78b-299">异步代码</span><span class="sxs-lookup"><span data-stu-id="8b78b-299">Asynchronous code</span></span>

<span data-ttu-id="8b78b-300">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="8b78b-300">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="8b78b-301">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="8b78b-301">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="8b78b-302">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="8b78b-302">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="8b78b-303">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="8b78b-303">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="8b78b-304">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="8b78b-304">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="8b78b-305">因此，使用异步代码可以更有效地利用服务器资源，并且可以让服务器在没有延迟的情况下处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="8b78b-305">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="8b78b-306">异步代码会在运行时引入少量开销。</span><span class="sxs-lookup"><span data-stu-id="8b78b-306">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="8b78b-307">流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="8b78b-307">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="8b78b-308">在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="8b78b-308">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="8b78b-309">`async` 关键字让编译器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8b78b-309">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="8b78b-310">为方法主体的各部分生成回调。</span><span class="sxs-lookup"><span data-stu-id="8b78b-310">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="8b78b-311">自动创建返回的 [Task](/dotnet/api/system.threading.tasks.task) 对象。</span><span class="sxs-lookup"><span data-stu-id="8b78b-311">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="8b78b-312">有关详细信息，请参阅[任务返回类型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-312">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="8b78b-313">隐式返回类型 `Task` 表示正在进行的工作。</span><span class="sxs-lookup"><span data-stu-id="8b78b-313">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="8b78b-314">`await` 关键字让编译器将该方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="8b78b-314">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="8b78b-315">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="8b78b-315">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="8b78b-316">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="8b78b-316">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="8b78b-317">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="8b78b-317">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="8b78b-318">编写使用 EF Core 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="8b78b-318">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="8b78b-319">只会异步执行导致查询或命令被发送到数据库的语句。</span><span class="sxs-lookup"><span data-stu-id="8b78b-319">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="8b78b-320">这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-320">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="8b78b-321">不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="8b78b-321">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="8b78b-322">EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="8b78b-322">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="8b78b-323">若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。</span><span class="sxs-lookup"><span data-stu-id="8b78b-323">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="8b78b-324">有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="8b78b-324">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="8b78b-325">下一个教程将介绍基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="8b78b-325">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8b78b-326">其他资源</span><span class="sxs-lookup"><span data-stu-id="8b78b-326">Additional resources</span></span>

* [<span data-ttu-id="8b78b-327">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="8b78b-327">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="8b78b-328">下一页</span><span class="sxs-lookup"><span data-stu-id="8b78b-328">Next</span></span>](xref:data/ef-rp/crud)
