---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
title: 教程：开始使用 Entity Framework 6 Code First 通过 MVC 5 |Microsoft Docs
description: 在本系列教程，您将学习如何构建用于数据访问使用 Entity Framework 6 的 ASP.NET MVC 5 应用程序。
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: 00bc8b51-32ed-4fd3-9745-be4c2a9c1eaf
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: a00a5a3aa295c2584b90abbf931c258c9e1b58ca
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889946"
---
# <a name="tutorial-get-started-with-entity-framework-6-code-first-using-mvc-5"></a><span data-ttu-id="a298a-103">教程：开始使用 Entity Framework 6 Code First 通过 MVC 5</span><span class="sxs-lookup"><span data-stu-id="a298a-103">Tutorial: Get Started with Entity Framework 6 Code First using MVC 5</span></span>

> [!NOTE]
> <span data-ttu-id="a298a-104">对于新开发，我们建议[ASP.NET Core Razor 页面](/aspnet/core/razor-pages)通过 ASP.NET MVC 控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="a298a-104">For new development, we recommend [ASP.NET Core Razor Pages](/aspnet/core/razor-pages) over ASP.NET MVC controllers and views.</span></span> <span data-ttu-id="a298a-105">有关类似于此系列教程使用 Razor 页面，请参阅[教程：开始使用 ASP.NET Core 中 Razor 页面](/aspnet/core/tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="a298a-105">For a tutorial series similar to this one using Razor Pages, see [Tutorial: Get started with Razor Pages in ASP.NET Core](/aspnet/core/tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="a298a-106">新教程：</span><span class="sxs-lookup"><span data-stu-id="a298a-106">The new tutorial:</span></span>
> * <span data-ttu-id="a298a-107">易于关注。</span><span class="sxs-lookup"><span data-stu-id="a298a-107">Is easier to follow.</span></span>
> * <span data-ttu-id="a298a-108">提供更多 EF Core 最佳做法。</span><span class="sxs-lookup"><span data-stu-id="a298a-108">Provides more EF Core best practices.</span></span>
> * <span data-ttu-id="a298a-109">使用更高效的查询。</span><span class="sxs-lookup"><span data-stu-id="a298a-109">Uses more efficient queries.</span></span>
> * <span data-ttu-id="a298a-110">通过最新 API 更新到更高版本。</span><span class="sxs-lookup"><span data-stu-id="a298a-110">Is more current with the latest API.</span></span>
> * <span data-ttu-id="a298a-111">涵盖更多功能。</span><span class="sxs-lookup"><span data-stu-id="a298a-111">Covers more features.</span></span>
> * <span data-ttu-id="a298a-112">是开发新应用程序的首选方法。</span><span class="sxs-lookup"><span data-stu-id="a298a-112">Is the preferred approach for new application development.</span></span>

<span data-ttu-id="a298a-113">在本系列教程，您将学习如何构建用于数据访问使用 Entity Framework 6 的 ASP.NET MVC 5 应用程序。</span><span class="sxs-lookup"><span data-stu-id="a298a-113">In this series of tutorials, you learn how to build an ASP.NET MVC 5 application that uses Entity Framework 6 for data access.</span></span> <span data-ttu-id="a298a-114">本教程使用 Code First 工作流。</span><span class="sxs-lookup"><span data-stu-id="a298a-114">This tutorial uses the Code First workflow.</span></span> <span data-ttu-id="a298a-115">有关如何选择第一个代码、 数据库第一个和第一个模型之间的信息，请参阅[创建模型](/ef/ef6/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="a298a-115">For information about how to choose between Code First, Database First, and Model First, see [Create a model](/ef/ef6/modeling/).</span></span>

<span data-ttu-id="a298a-116">此教程系列介绍了如何构建 Contoso University 示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="a298a-116">This tutorial series explains how to build the Contoso University sample application.</span></span> <span data-ttu-id="a298a-117">示例应用程序是一个简单的大学网站。</span><span class="sxs-lookup"><span data-stu-id="a298a-117">The sample application is a simple university website.</span></span> <span data-ttu-id="a298a-118">使用它，可以查看和更新学生、 课程和教师信息。</span><span class="sxs-lookup"><span data-stu-id="a298a-118">With it, you can view and update student, course, and instructor information.</span></span> <span data-ttu-id="a298a-119">下面是两个您创建的屏幕：</span><span class="sxs-lookup"><span data-stu-id="a298a-119">Here are two of the screens you create:</span></span>

![Students_Index_page](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image1.png)

![编辑学生](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="a298a-122">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="a298a-122">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a298a-123">创建 MVC web 应用</span><span class="sxs-lookup"><span data-stu-id="a298a-123">Create an MVC web app</span></span>
> * <span data-ttu-id="a298a-124">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="a298a-124">Set up the site style</span></span>
> * <span data-ttu-id="a298a-125">安装 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="a298a-125">Install Entity Framework 6</span></span>
> * <span data-ttu-id="a298a-126">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="a298a-126">Create the data model</span></span>
> * <span data-ttu-id="a298a-127">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="a298a-127">Create the database context</span></span>
> * <span data-ttu-id="a298a-128">使用测试数据初始化 DB</span><span class="sxs-lookup"><span data-stu-id="a298a-128">Initialize DB with test data</span></span>
> * <span data-ttu-id="a298a-129">设置为使用 LocalDB 的 EF 6</span><span class="sxs-lookup"><span data-stu-id="a298a-129">Set up EF 6 to use LocalDB</span></span>
> * <span data-ttu-id="a298a-130">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="a298a-130">Create controller and views</span></span>
> * <span data-ttu-id="a298a-131">查看数据库</span><span class="sxs-lookup"><span data-stu-id="a298a-131">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a298a-132">系统必备</span><span class="sxs-lookup"><span data-stu-id="a298a-132">Prerequisites</span></span>

* [<span data-ttu-id="a298a-133">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a298a-133">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)

## <a name="create-an-mvc-web-app"></a><span data-ttu-id="a298a-134">创建 MVC web 应用</span><span class="sxs-lookup"><span data-stu-id="a298a-134">Create an MVC web app</span></span>

1. <span data-ttu-id="a298a-135">打开 Visual Studio 并创建C#web 项目使用**ASP.NET Web 应用程序 (.NET Framework)** 模板。</span><span class="sxs-lookup"><span data-stu-id="a298a-135">Open Visual Studio and create a C# web project using the **ASP.NET Web Application (.NET Framework)** template.</span></span> <span data-ttu-id="a298a-136">将项目命名*ContosoUniversity* ，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="a298a-136">Name the project *ContosoUniversity* and select **OK**.</span></span>

   ![在 Visual Studio 中的新建项目对话框](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/new-project-dialog.png)

1. <span data-ttu-id="a298a-138">在中**新 ASP.NET Web 应用程序-ContosoUniversity**，选择**MVC**。</span><span class="sxs-lookup"><span data-stu-id="a298a-138">In **New ASP.NET Web Application - ContosoUniversity**, select **MVC**.</span></span>

   ![在 Visual Studio 中的新 web 应用程序对话框中](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/new-web-app-dialog.png)

    > [!NOTE]
    > <span data-ttu-id="a298a-140">默认情况下**身份验证**选项设置为**无身份验证**。</span><span class="sxs-lookup"><span data-stu-id="a298a-140">By default, the **Authentication** option is set to **No Authentication**.</span></span> <span data-ttu-id="a298a-141">对于本教程，web 应用不需要用户进行登录。</span><span class="sxs-lookup"><span data-stu-id="a298a-141">For this tutorial, the web app doesn't require users to sign in.</span></span> <span data-ttu-id="a298a-142">此外，它并不限制基于谁登录访问。</span><span class="sxs-lookup"><span data-stu-id="a298a-142">Also, it doesn't restrict access based on who's signed in.</span></span>

1. <span data-ttu-id="a298a-143">选择“确定”创建项目。</span><span class="sxs-lookup"><span data-stu-id="a298a-143">Select **OK** to create the project.</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="a298a-144">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="a298a-144">Set up the site style</span></span>

<span data-ttu-id="a298a-145">通过几个简单的更改设置站点菜单、 布局和主页。</span><span class="sxs-lookup"><span data-stu-id="a298a-145">A few simple changes will set up the site menu, layout, and home page.</span></span>

1. <span data-ttu-id="a298a-146">打开*views/shared\\_Layout.cshtml*，并进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="a298a-146">Open *Views\Shared\\_Layout.cshtml*, and make the following changes:</span></span>

   - <span data-ttu-id="a298a-147">将每个匹配项的"My ASP.NET Application"和"Application name"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="a298a-147">Change each occurrence of "My ASP.NET Application" and "Application name" to "Contoso University".</span></span>
   - <span data-ttu-id="a298a-148">面向学生、 课程、 讲师和部门，添加菜单项和删除联系人条目。</span><span class="sxs-lookup"><span data-stu-id="a298a-148">Add menu entries for Students, Courses, Instructors, and Departments, and delete the Contact entry.</span></span>

   <span data-ttu-id="a298a-149">以下代码片段中突出显示所做的更改：</span><span class="sxs-lookup"><span data-stu-id="a298a-149">The changes are highlighted in the following code snippet:</span></span>

   [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample1.cshtml?highlight=6,19,24-27,38)]

2. <span data-ttu-id="a298a-150">在中*Views\Home\Index.cshtml*，该文件的内容替换为以下代码以将有关 ASP.NET 和 MVC 文本有关此应用程序的文本替换为：</span><span class="sxs-lookup"><span data-stu-id="a298a-150">In *Views\Home\Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

   [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample2.cshtml)]

3. <span data-ttu-id="a298a-151">按 Ctrl + F5 以运行该 web 站点。</span><span class="sxs-lookup"><span data-stu-id="a298a-151">Press Ctrl+F5 to run the web site.</span></span> <span data-ttu-id="a298a-152">请参阅主页，其中主菜单。</span><span class="sxs-lookup"><span data-stu-id="a298a-152">You see the home page with the main menu.</span></span>

## <a name="install-entity-framework-6"></a><span data-ttu-id="a298a-153">安装 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="a298a-153">Install Entity Framework 6</span></span>

1. <span data-ttu-id="a298a-154">从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="a298a-154">From the **Tools** menu, choose **NuGet Package Manager**, and then choose **Package Manager Console**.</span></span>

2. <span data-ttu-id="a298a-155">在中**程序包管理器控制台**窗口中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="a298a-155">In the **Package Manager Console** window, enter the following command:</span></span>

   ```text
   Install-Package EntityFramework
   ```

<span data-ttu-id="a298a-156">此步骤是几个步骤，本教程要求您手动执行此，但是，无法进行了自动通过 ASP.NET MVC 基架功能之一。</span><span class="sxs-lookup"><span data-stu-id="a298a-156">This step is one of a few steps that this tutorial has you do manually, but that could have been done automatically by the ASP.NET MVC scaffolding feature.</span></span> <span data-ttu-id="a298a-157">要执行这些手动，以便您可以看到使用 Entity Framework (EF) 所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="a298a-157">You're doing them manually so that you can see the steps required to use Entity Framework (EF).</span></span> <span data-ttu-id="a298a-158">您将更高版本使用基架创建 MVC 控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="a298a-158">You'll use scaffolding later to create the MVC controller and views.</span></span> <span data-ttu-id="a298a-159">一种替代方法是让基架会自动安装 EF NuGet 包、 创建数据库上下文类，并创建连接字符串。</span><span class="sxs-lookup"><span data-stu-id="a298a-159">An alternative is to let scaffolding automatically install the EF NuGet package, create the database context class, and create the connection string.</span></span> <span data-ttu-id="a298a-160">如果你已准备好执行此操作通过这种方式，只需是跳过这些步骤，在创建实体类后，在 MVC 控制器搭建基架。</span><span class="sxs-lookup"><span data-stu-id="a298a-160">When you're ready to do it that way, all you have to do is skip those steps and scaffold your MVC controller after you create your entity classes.</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="a298a-161">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="a298a-161">Create the data model</span></span>

<span data-ttu-id="a298a-162">接下来你将创建 Contoso 大学应用程序的实体类。</span><span class="sxs-lookup"><span data-stu-id="a298a-162">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="a298a-163">首先将以下三个实体：</span><span class="sxs-lookup"><span data-stu-id="a298a-163">You'll start with the following three entities:</span></span>

<span data-ttu-id="a298a-164">**课程** <-> **注册** <-> **学生**</span><span class="sxs-lookup"><span data-stu-id="a298a-164">**Course** <-> **Enrollment** <-> **Student**</span></span>

| <span data-ttu-id="a298a-165">实体</span><span class="sxs-lookup"><span data-stu-id="a298a-165">Entities</span></span> | <span data-ttu-id="a298a-166">关系</span><span class="sxs-lookup"><span data-stu-id="a298a-166">Relationship</span></span> |
| -------- | ------------ |
| <span data-ttu-id="a298a-167">注册的课程</span><span class="sxs-lookup"><span data-stu-id="a298a-167">Course to Enrollment</span></span> | <span data-ttu-id="a298a-168">一个对多</span><span class="sxs-lookup"><span data-stu-id="a298a-168">One-to-many</span></span> |
| <span data-ttu-id="a298a-169">注册的学生</span><span class="sxs-lookup"><span data-stu-id="a298a-169">Student to Enrollment</span></span> | <span data-ttu-id="a298a-170">一个对多</span><span class="sxs-lookup"><span data-stu-id="a298a-170">One-to-many</span></span> |

<span data-ttu-id="a298a-171">`Student` 和 `Enrollment`实体之间是一对多的关系，`Course` 和`Enrollment` 实体之间也是一个对多的关系。</span><span class="sxs-lookup"><span data-stu-id="a298a-171">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="a298a-172">换而言之，一名学生可以修读任意数量的课程, 并且某一课程可以被任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="a298a-172">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="a298a-173">在以下部分中，你将创建这些实体的每个类。</span><span class="sxs-lookup"><span data-stu-id="a298a-173">In the following sections, you'll create a class for each one of these entities.</span></span>

> [!NOTE]
> <span data-ttu-id="a298a-174">如果您尝试编译项目，然后完成创建所有这些实体类，将收到编译器错误。</span><span class="sxs-lookup"><span data-stu-id="a298a-174">If you try to compile the project before you finish creating all of these entity classes, you'll get compiler errors.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="a298a-175">Student 实体</span><span class="sxs-lookup"><span data-stu-id="a298a-175">The Student entity</span></span>

- <span data-ttu-id="a298a-176">在中*模型*文件夹中，创建名为的类文件*Student.cs*上的文件夹中右键单击**解决方案资源管理器**，然后选择**添加**  > **类**。</span><span class="sxs-lookup"><span data-stu-id="a298a-176">In the *Models* folder, create a class file named *Student.cs* by right-clicking on the folder in **Solution Explorer** and choosing **Add** > **Class**.</span></span> <span data-ttu-id="a298a-177">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="a298a-177">Replace the template code with the following code:</span></span>

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample3.cs)]

<span data-ttu-id="a298a-178">`ID` 属性将成为对应于此类的数据库表中的主键。</span><span class="sxs-lookup"><span data-stu-id="a298a-178">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="a298a-179">名为的属性解释默认情况下，实体框架`ID`或*classname* `ID`为主键。</span><span class="sxs-lookup"><span data-stu-id="a298a-179">By default, Entity Framework interprets a property that's named `ID` or *classname* `ID` as the primary key.</span></span>

<span data-ttu-id="a298a-180">`Enrollments` 属性是*导航属性*。</span><span class="sxs-lookup"><span data-stu-id="a298a-180">The `Enrollments` property is a *navigation property*.</span></span> <span data-ttu-id="a298a-181">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="a298a-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="a298a-182">在这种情况下，`Enrollments`的属性`Student`实体将保留所有`Enrollment`实体相关的`Student`实体。</span><span class="sxs-lookup"><span data-stu-id="a298a-182">In this case, the `Enrollments` property of a `Student` entity will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="a298a-183">换而言之，如果给定`Student`数据库中的行具有两个相关`Enrollment`行 (包含该学生的主键的行中的值及其`StudentID`外键列)，则该`Student`实体的`Enrollments`导航属性将包含这两个`Enrollment`实体。</span><span class="sxs-lookup"><span data-stu-id="a298a-183">In other words, if a given `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their `StudentID` foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="a298a-184">导航属性通常定义为`virtual`，以便它们可以充分利用 Entity Framework 的特定功能，如*延迟加载*。</span><span class="sxs-lookup"><span data-stu-id="a298a-184">Navigation properties are typically defined as `virtual` so that they can take advantage of certain Entity Framework functionality such as *lazy loading*.</span></span> <span data-ttu-id="a298a-185">(将解释延迟加载在后面[读取相关数据](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)本系列后面的教程。)</span><span class="sxs-lookup"><span data-stu-id="a298a-185">(Lazy loading will be explained later, in the [Reading Related Data](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial later in this series.)</span></span>

<span data-ttu-id="a298a-186">如果导航属性可以具有多个实体 （如多对多或一对多关系），那么导航属性的类型必须是可以添加、 删除和更新条目的容器，如 `ICollection`。</span><span class="sxs-lookup"><span data-stu-id="a298a-186">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection`.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="a298a-187">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="a298a-187">The Enrollment entity</span></span>

- <span data-ttu-id="a298a-188">在 *Models* 文件夹中，创建 *Enrollment.cs* 并且用以下代码替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="a298a-188">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample4.cs)]

<span data-ttu-id="a298a-189">`EnrollmentID`属性将被设为主键; 此实体使用*classname* `ID`模式而不是`ID`如示`Student`实体。</span><span class="sxs-lookup"><span data-stu-id="a298a-189">The `EnrollmentID` property will be the primary key; this entity uses the *classname* `ID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="a298a-190">通常情况下，你选择一个主键模式，并在你的数据模型自始至终使用这种模式。</span><span class="sxs-lookup"><span data-stu-id="a298a-190">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="a298a-191">在这里，使用了两种不同的模式只是为了说明你可以使用任一模式来指定主键。</span><span class="sxs-lookup"><span data-stu-id="a298a-191">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="a298a-192">在后面的教程，您将看到如何使用`ID`而无需`classname`轻松地在数据模型中实现继承。</span><span class="sxs-lookup"><span data-stu-id="a298a-192">In a later tutorial, you'll see how using `ID` without `classname` makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="a298a-193">`Grade`属性是[枚举](/ef/ef6/modeling/code-first/data-types/enums)。</span><span class="sxs-lookup"><span data-stu-id="a298a-193">The `Grade` property is an [enum](/ef/ef6/modeling/code-first/data-types/enums).</span></span> <span data-ttu-id="a298a-194">问号后`Grade`类型声明指示`Grade`属性是[可以为 null](/dotnet/csharp/programming-guide/nullable-types/using-nullable-types)。</span><span class="sxs-lookup"><span data-stu-id="a298a-194">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/using-nullable-types).</span></span> <span data-ttu-id="a298a-195">评级为 null 是不同于评级为零，null 表示一个等级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="a298a-195">A grade that's null is different from a zero grade — null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="a298a-196">`StudentID` 属性是外键，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="a298a-196">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="a298a-197">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含单个 `Student` 实体 (与前面所看到的 `Student.Enrollments` 导航属性不同后，`Student`中可以容纳多个 `Enrollment` 实体)。</span><span class="sxs-lookup"><span data-stu-id="a298a-197">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="a298a-198">`CourseID` 属性是一个外键， `Course` 是与其对应的导航属性。</span><span class="sxs-lookup"><span data-stu-id="a298a-198">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="a298a-199">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="a298a-199">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="a298a-200">实体框架的属性解释为外键属性如果它名为 *&lt;导航属性名称&gt;&lt;主键属性名称&gt;* (例如， `StudentID`对于`Student`导航属性所以`Student`实体的主键是`ID`)。</span><span class="sxs-lookup"><span data-stu-id="a298a-200">Entity Framework interprets a property as a foreign key property if it's named *&lt;navigation property name&gt;&lt;primary key property name&gt;* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="a298a-201">外键属性还可以进行命名相同只需 *&lt;主键属性名称&gt;* (例如，`CourseID`由于`Course`实体的主键是`CourseID`)。</span><span class="sxs-lookup"><span data-stu-id="a298a-201">Foreign key properties can also be named the same simply *&lt;primary key property name&gt;* (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="a298a-202">Course 实体</span><span class="sxs-lookup"><span data-stu-id="a298a-202">The Course entity</span></span>

- <span data-ttu-id="a298a-203">在中*模型*文件夹中，创建*Course.cs*，模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="a298a-203">In the *Models* folder, create *Course.cs*, replacing the template code with the following code:</span></span>

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample5.cs)]

<span data-ttu-id="a298a-204">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="a298a-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="a298a-205">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="a298a-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="a298a-206">我们说更多有关<xref:System.ComponentModel.DataAnnotations.Schema.DatabaseGeneratedAttribute>本系列后面的教程中的属性。</span><span class="sxs-lookup"><span data-stu-id="a298a-206">We'll say more about the <xref:System.ComponentModel.DataAnnotations.Schema.DatabaseGeneratedAttribute> attribute in a later tutorial in this series.</span></span> <span data-ttu-id="a298a-207">简单来说，此特性让你能自行指定主键，而不是让数据库自动指定主键。</span><span class="sxs-lookup"><span data-stu-id="a298a-207">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="a298a-208">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="a298a-208">Create the database context</span></span>

<span data-ttu-id="a298a-209">协调为给定的数据模型的实体框架功能的主类是*数据库上下文*类。</span><span class="sxs-lookup"><span data-stu-id="a298a-209">The main class that coordinates Entity Framework functionality for a given data model is the *database context* class.</span></span> <span data-ttu-id="a298a-210">通过从派生来创建此类[System.Data.Entity.DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx)类。</span><span class="sxs-lookup"><span data-stu-id="a298a-210">You create this class by deriving from the [System.Data.Entity.DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx) class.</span></span> <span data-ttu-id="a298a-211">在代码中，指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="a298a-211">In your code, you specify which entities are included in the data model.</span></span> <span data-ttu-id="a298a-212">你还可以定义某些 Entity Framework 行为。</span><span class="sxs-lookup"><span data-stu-id="a298a-212">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="a298a-213">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="a298a-213">In this project, the class is named `SchoolContext`.</span></span>

- <span data-ttu-id="a298a-214">若要为 ContosoUniversity 项目中创建一个文件夹，右键单击项目中的**解决方案资源管理器**并单击**添加**，然后单击**新文件夹**。</span><span class="sxs-lookup"><span data-stu-id="a298a-214">To create a folder in the ContosoUniversity project, right-click the project in **Solution Explorer** and click **Add**, and then click **New Folder**.</span></span> <span data-ttu-id="a298a-215">将新文件夹命名*DAL* （适用于数据访问层）。</span><span class="sxs-lookup"><span data-stu-id="a298a-215">Name the new folder *DAL* (for Data Access Layer).</span></span> <span data-ttu-id="a298a-216">在该文件夹中创建名为的新类文件*SchoolContext.cs*，并使用以下代码替换模板代码：</span><span class="sxs-lookup"><span data-stu-id="a298a-216">In that folder, create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample6.cs)]

### <a name="specify-entity-sets"></a><span data-ttu-id="a298a-217">指定实体集</span><span class="sxs-lookup"><span data-stu-id="a298a-217">Specify entity sets</span></span>

<span data-ttu-id="a298a-218">此代码将创建[DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.113).aspx)每个实体集的属性。</span><span class="sxs-lookup"><span data-stu-id="a298a-218">This code creates a [DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.113).aspx) property for each entity set.</span></span> <span data-ttu-id="a298a-219">在实体框架术语中，*实体集*通常对应于数据库表和一个*实体*对应于表中的行。</span><span class="sxs-lookup"><span data-stu-id="a298a-219">In Entity Framework terminology, an *entity set* typically corresponds to a database table, and an *entity* corresponds to a row in the table.</span></span>

> [!NOTE]
>
> <span data-ttu-id="a298a-220">可以省略`DbSet<Enrollment>`和`DbSet<Course>`语句和它的工作方式相同。</span><span class="sxs-lookup"><span data-stu-id="a298a-220">You can omit the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="a298a-221">实体框架因为将隐式包括它们`Student`实体引用`Enrollment`实体以及`Enrollment`实体引用`Course`实体。</span><span class="sxs-lookup"><span data-stu-id="a298a-221">Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

### <a name="specify-the-connection-string"></a><span data-ttu-id="a298a-222">指定连接字符串</span><span class="sxs-lookup"><span data-stu-id="a298a-222">Specify the connection string</span></span>

<span data-ttu-id="a298a-223">连接字符串 （它稍后将添加到 Web.config 文件） 的名称被传递给构造函数。</span><span class="sxs-lookup"><span data-stu-id="a298a-223">The name of the connection string (which you'll add to the Web.config file later) is passed in to the constructor.</span></span>

[!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample7.cs?highlight=1)]

<span data-ttu-id="a298a-224">此外可以传递连接字符串本身而不是一个存储在 Web.config 文件的名称中。</span><span class="sxs-lookup"><span data-stu-id="a298a-224">You could also pass in the connection string itself instead of the name of one that is stored in the Web.config file.</span></span> <span data-ttu-id="a298a-225">有关使用用于指定数据库选项的详细信息，请参阅[连接字符串和模型](/ef/ef6/fundamentals/configuring/connection-strings)。</span><span class="sxs-lookup"><span data-stu-id="a298a-225">For more information about options for specifying the database to use, see [Connection strings and models](/ef/ef6/fundamentals/configuring/connection-strings).</span></span>

<span data-ttu-id="a298a-226">如果未显式指定连接字符串或其中一个的名称，实体框架将假定该连接字符串名称是与类名称相同。</span><span class="sxs-lookup"><span data-stu-id="a298a-226">If you don't specify a connection string or the name of one explicitly, Entity Framework assumes that the connection string name is the same as the class name.</span></span> <span data-ttu-id="a298a-227">在此示例中的默认连接字符串名称都将`SchoolContext`，与您要指定的显式。</span><span class="sxs-lookup"><span data-stu-id="a298a-227">The default connection string name in this example would then be `SchoolContext`, the same as what you're specifying explicitly.</span></span>

### <a name="specify-singular-table-names"></a><span data-ttu-id="a298a-228">指定单数形式的表名称</span><span class="sxs-lookup"><span data-stu-id="a298a-228">Specify singular table names</span></span>

<span data-ttu-id="a298a-229">`modelBuilder.Conventions.Remove`中的语句[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating(v=vs.103).aspx)方法会阻止表名正在变为复数形式。</span><span class="sxs-lookup"><span data-stu-id="a298a-229">The `modelBuilder.Conventions.Remove` statement in the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating(v=vs.103).aspx) method prevents table names from being pluralized.</span></span> <span data-ttu-id="a298a-230">如果不这样做，将命名为数据库中生成的表`Students`， `Courses`，和`Enrollments`。</span><span class="sxs-lookup"><span data-stu-id="a298a-230">If you didn't do this, the generated tables in the database would be named `Students`, `Courses`, and `Enrollments`.</span></span> <span data-ttu-id="a298a-231">相反，将表名`Student`， `Course`，和`Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="a298a-231">Instead, the table names will be `Student`, `Course`, and `Enrollment`.</span></span> <span data-ttu-id="a298a-232">开发者对表名称是否应为复数意见不一。</span><span class="sxs-lookup"><span data-stu-id="a298a-232">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="a298a-233">本教程使用单数形式，但重要的一点是，你可以选择任意窗体，您的首选，通过包括或省略这行代码。</span><span class="sxs-lookup"><span data-stu-id="a298a-233">This tutorial uses the singular form, but the important point is that you can select whichever form you prefer by including or omitting this line of code.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="a298a-234">使用测试数据初始化 DB</span><span class="sxs-lookup"><span data-stu-id="a298a-234">Initialize DB with test data</span></span>

<span data-ttu-id="a298a-235">实体框架可以自动创建 （或删除并重新创建） 为您的应用程序运行时的数据库。</span><span class="sxs-lookup"><span data-stu-id="a298a-235">Entity Framework can automatically create (or drop and re-create) a database for you when the application runs.</span></span> <span data-ttu-id="a298a-236">您可以指定应此操作每次你的应用程序运行时或仅在与现有的数据库不同步模型时。</span><span class="sxs-lookup"><span data-stu-id="a298a-236">You can specify that this should be done every time your application runs or only when the model is out of sync with the existing database.</span></span> <span data-ttu-id="a298a-237">此外可以编写`Seed`方法的实体框架会自动调用后才能填充测试数据创建数据库。</span><span class="sxs-lookup"><span data-stu-id="a298a-237">You can also write a `Seed` method that Entity Framework automatically calls after creating the database in order to populate it with test data.</span></span>

<span data-ttu-id="a298a-238">默认行为是创建数据库，仅当它不存在 （和引发异常，如果模型已更改，并且数据库已存在）。</span><span class="sxs-lookup"><span data-stu-id="a298a-238">The default behavior is to create a database only if it doesn't exist (and throw an exception if the model has changed and the database already exists).</span></span> <span data-ttu-id="a298a-239">在本部分中，将指定应删除并重新创建该模型发生更改时数据库。</span><span class="sxs-lookup"><span data-stu-id="a298a-239">In this section, you'll specify that the database should be dropped and re-created whenever the model changes.</span></span> <span data-ttu-id="a298a-240">删除数据库导致所有数据的丢失。</span><span class="sxs-lookup"><span data-stu-id="a298a-240">Dropping the database causes the loss of all your data.</span></span> <span data-ttu-id="a298a-241">这通常是好了在开发期间，因为`Seed`方法将运行时重新创建数据库，并将重新创建您的测试数据。</span><span class="sxs-lookup"><span data-stu-id="a298a-241">This is generally okay during development, because the `Seed` method will run when the database is re-created and will re-create your test data.</span></span> <span data-ttu-id="a298a-242">但在生产环境中通常不想丢失所有数据，每次需要更改数据库架构。</span><span class="sxs-lookup"><span data-stu-id="a298a-242">But in production you generally don't want to lose all your data every time you need to change the database schema.</span></span> <span data-ttu-id="a298a-243">稍后你将了解如何通过使用 Code First 迁移来更改数据库架构而不是删除并重新创建数据库来处理模型更改。</span><span class="sxs-lookup"><span data-stu-id="a298a-243">Later you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

1. <span data-ttu-id="a298a-244">在 DAL 文件夹中，创建名为的新类文件*SchoolInitializer.cs*和模板代码替换为以下代码，这会导致要在需要时创建的数据库和加载到新的数据库测试数据。</span><span class="sxs-lookup"><span data-stu-id="a298a-244">In the DAL folder, create a new class file named *SchoolInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample8.cs)]

   <span data-ttu-id="a298a-245">`Seed`方法采用数据库上下文对象作为输入参数，并在方法中的代码使用该对象来将新实体添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="a298a-245">The `Seed` method takes the database context object as an input parameter, and the code in the method uses that object to add new entities to the database.</span></span> <span data-ttu-id="a298a-246">对于每个实体类型，代码创建新实体的集合，将它们添加到相应`DbSet`属性，然后单击保存到数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="a298a-246">For each entity type, the code creates a collection of new  entities, adds them to the appropriate `DbSet` property, and then saves the changes to the database.</span></span> <span data-ttu-id="a298a-247">但并不需要调用`SaveChanges`后的实体的每个组的方法，正如在这里，但这样做可帮助您找到问题的根源，如果向数据库写入代码时出现异常。</span><span class="sxs-lookup"><span data-stu-id="a298a-247">It isn't necessary to call the `SaveChanges` method after each group of entities, as is done here, but doing that helps you locate the source of a problem if an exception occurs while the code is writing to the database.</span></span>

2. <span data-ttu-id="a298a-248">若要指示 Entity Framework 使用初始值设定项类，将元素添加到`entityFramework`应用程序中的元素*Web.config*文件 （一个根项目文件夹中），如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="a298a-248">To tell Entity Framework to use your initializer class, add an element to the `entityFramework` element in the application *Web.config* file (the one in the root project folder), as shown in the following example:</span></span>

   [!code-xml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample9.xml?highlight=2-6)]

   <span data-ttu-id="a298a-249">`context type`指定完全限定的上下文类名称和它所在的程序集和`databaseinitializer type`指定初始值设定项类和它所在的程序集的完全限定的名称。</span><span class="sxs-lookup"><span data-stu-id="a298a-249">The `context type` specifies the fully qualified context class name and the assembly it's in, and the `databaseinitializer type` specifies the fully qualified name of the initializer class and the assembly it's in.</span></span> <span data-ttu-id="a298a-250">(如果不希望 EF 使用初始值设定项，您可以设置属性`context`元素： `disableDatabaseInitialization="true"`。)有关详细信息，请参阅[配置文件设置](/ef/ef6/fundamentals/configuring/config-file)。</span><span class="sxs-lookup"><span data-stu-id="a298a-250">(When you don't want EF to use the initializer, you can set an attribute on the `context` element: `disableDatabaseInitialization="true"`.) For more information, see [Configuration File Settings](/ef/ef6/fundamentals/configuring/config-file).</span></span>

   <span data-ttu-id="a298a-251">设置初始值设定项的一种替代方法*Web.config*文件是在代码中执行该操作通过添加`Database.SetInitializer`语句`Application_Start`中的方法*Global.asax.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="a298a-251">An alternative to setting the initializer in the *Web.config* file is to do it in code by adding a `Database.SetInitializer` statement to the `Application_Start` method in the *Global.asax.cs* file.</span></span> <span data-ttu-id="a298a-252">有关详细信息，请参阅[了解数据库初始值设定项中 Entity Framework Code First](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm)。</span><span class="sxs-lookup"><span data-stu-id="a298a-252">For more information, see [Understanding Database Initializers in Entity Framework Code First](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm).</span></span>

<span data-ttu-id="a298a-253">应用程序现在已设置，以便在首次在给定运行的应用程序中访问数据库时，实体框架进行比较对模型数据库 (您`SchoolContext`和实体类)。</span><span class="sxs-lookup"><span data-stu-id="a298a-253">The application is now set up so that when you access the database for the first time in a given run of the application, Entity Framework compares the database to the model (your `SchoolContext` and entity classes).</span></span> <span data-ttu-id="a298a-254">如果差异，应用程序删除并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="a298a-254">If there's a difference, the application drops and re-creates the database.</span></span>

> [!NOTE]
> <span data-ttu-id="a298a-255">在部署到生产 web 服务器的应用程序时，必须删除或禁用删除并重新创建数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="a298a-255">When you deploy an application to a production web server, you must remove or disable code that drops and re-creates the database.</span></span> <span data-ttu-id="a298a-256">在本系列中下一个教程，将执行该操作。</span><span class="sxs-lookup"><span data-stu-id="a298a-256">You'll do that in a later tutorial in this series.</span></span>

## <a name="set-up-ef-6-to-use-localdb"></a><span data-ttu-id="a298a-257">设置为使用 LocalDB 的 EF 6</span><span class="sxs-lookup"><span data-stu-id="a298a-257">Set up EF 6 to use LocalDB</span></span>

<span data-ttu-id="a298a-258">[LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb?view=sql-server-2017)是 SQL Server Express 数据库引擎的轻型版本。</span><span class="sxs-lookup"><span data-stu-id="a298a-258">[LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb?view=sql-server-2017) is a lightweight version of the SQL Server Express database engine.</span></span> <span data-ttu-id="a298a-259">它易于安装和配置、 按需、 启动并在用户模式下运行。</span><span class="sxs-lookup"><span data-stu-id="a298a-259">It's easy to install and configure, starts on demand, and runs in user mode.</span></span> <span data-ttu-id="a298a-260">中的 SQL Server Express，使您可以使用数据库作为特殊执行模式运行 LocalDB *.mdf*文件。</span><span class="sxs-lookup"><span data-stu-id="a298a-260">LocalDB runs in a special execution mode of SQL Server Express that enables you to work with databases as *.mdf* files.</span></span> <span data-ttu-id="a298a-261">可以将 LocalDB 数据库文件放入*应用程序\_数据*web 项目，如果你想要能够复制数据库与项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a298a-261">You can put LocalDB database files in the *App\_Data* folder of a web project if you want to be able to copy the database with the project.</span></span> <span data-ttu-id="a298a-262">在 SQL Server Express 用户实例功能还使您可以使用 *.mdf*文件，但用户实例功能已弃用; 因此，用于处理建议 LocalDB *.mdf*文件。</span><span class="sxs-lookup"><span data-stu-id="a298a-262">The user instance feature in SQL Server Express also enables you to work with *.mdf* files, but the user instance feature is deprecated; therefore, LocalDB is recommended for working with *.mdf* files.</span></span> <span data-ttu-id="a298a-263">默认情况下使用 Visual Studio 安装 LocalDB。</span><span class="sxs-lookup"><span data-stu-id="a298a-263">LocalDB is installed by default with Visual Studio.</span></span>

<span data-ttu-id="a298a-264">通常情况下，SQL Server Express 不用于生产 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="a298a-264">Typically, SQL Server Express is not used for production web applications.</span></span> <span data-ttu-id="a298a-265">LocalDB 具体而言不是建议用于生产 web 应用程序因为它不旨在与 IIS 配合使用。</span><span class="sxs-lookup"><span data-stu-id="a298a-265">LocalDB in particular is not recommended for production use with a web application because it's not designed to work with IIS.</span></span>

- <span data-ttu-id="a298a-266">在本教程中，您将使用 LocalDB。</span><span class="sxs-lookup"><span data-stu-id="a298a-266">In this tutorial, you'll work with LocalDB.</span></span> <span data-ttu-id="a298a-267">打开应用程序*Web.config*文件，并添加`connectionStrings`元素前面`appSettings`元素，如下面的示例中所示。</span><span class="sxs-lookup"><span data-stu-id="a298a-267">Open the application *Web.config* file and add a `connectionStrings` element preceding the `appSettings` element, as shown in the following example.</span></span> <span data-ttu-id="a298a-268">(请确保更新*Web.config*根项目文件夹中的文件。</span><span class="sxs-lookup"><span data-stu-id="a298a-268">(Make sure you update the *Web.config* file in the root project folder.</span></span> <span data-ttu-id="a298a-269">此外，还有*Web.config*中的文件*视图*无需更新的子文件夹。)</span><span class="sxs-lookup"><span data-stu-id="a298a-269">There's also a *Web.config* file in the *Views* subfolder that you don't need to update.)</span></span>

   [!code-xml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample10.xml?highlight=1-3)]

<span data-ttu-id="a298a-270">已添加的连接字符串指定实体框架将使用名为的 LocalDB 数据库*ContosoUniversity1.mdf*。</span><span class="sxs-lookup"><span data-stu-id="a298a-270">The connection string you've added specifies that Entity Framework will use a LocalDB database named *ContosoUniversity1.mdf*.</span></span> <span data-ttu-id="a298a-271">（该数据库尚不存在，但 EF 将创建它。）如果你想要在其中创建数据库你*应用程序\_数据*文件夹中，您可以添加`AttachDBFilename=|DataDirectory|\ContosoUniversity1.mdf`到连接字符串。</span><span class="sxs-lookup"><span data-stu-id="a298a-271">(The database doesn't exist yet but EF will create it.) If you want to create the database in your *App\_Data* folder, you could add `AttachDBFilename=|DataDirectory|\ContosoUniversity1.mdf` to the connection string.</span></span> <span data-ttu-id="a298a-272">有关连接字符串的详细信息，请参阅[ASP.NET Web 应用程序的 SQL Server 连接字符串](/previous-versions/aspnet/jj653752(v=vs.110))。</span><span class="sxs-lookup"><span data-stu-id="a298a-272">For more information about connection strings, see [SQL Server Connection Strings for ASP.NET Web Applications](/previous-versions/aspnet/jj653752(v=vs.110)).</span></span>

<span data-ttu-id="a298a-273">实际上并不需要的连接字符串*Web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="a298a-273">You don't actually need a connection string in the *Web.config* file.</span></span> <span data-ttu-id="a298a-274">如果不提供连接字符串，实体框架使用基于上下文类的默认连接字符串。</span><span class="sxs-lookup"><span data-stu-id="a298a-274">If you don't supply a connection string, Entity Framework uses a default connection string based on your context class.</span></span> <span data-ttu-id="a298a-275">有关详细信息，请参阅[到新数据库 Code First](/ef/ef6/modeling/code-first/workflows/new-database)。</span><span class="sxs-lookup"><span data-stu-id="a298a-275">For more information, see [Code First to a New Database](/ef/ef6/modeling/code-first/workflows/new-database).</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="a298a-276">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="a298a-276">Create controller and views</span></span>

<span data-ttu-id="a298a-277">现在将创建一个用于显示数据 web 页。</span><span class="sxs-lookup"><span data-stu-id="a298a-277">Now you'll create a web page to display data.</span></span> <span data-ttu-id="a298a-278">自动请求数据的过程将触发创建数据库。</span><span class="sxs-lookup"><span data-stu-id="a298a-278">The process of requesting the data automatically triggers the creation of the database.</span></span> <span data-ttu-id="a298a-279">您将首先创建一个新的控制器。</span><span class="sxs-lookup"><span data-stu-id="a298a-279">You'll begin by creating a new controller.</span></span> <span data-ttu-id="a298a-280">但这样做之前，请生成项目以使模型和上下文类可用于 MVC 控制器基架。</span><span class="sxs-lookup"><span data-stu-id="a298a-280">But before you do that, build the project to make the model and context classes available to MVC controller scaffolding.</span></span>

1. <span data-ttu-id="a298a-281">右键单击**控制器**中的文件夹**解决方案资源管理器**，选择**添加**，然后单击**新基架项**。</span><span class="sxs-lookup"><span data-stu-id="a298a-281">Right-click the **Controllers** folder in **Solution Explorer**, select **Add**, and then click **New Scaffolded Item**.</span></span>
2. <span data-ttu-id="a298a-282">在中**添加基架**对话框中，选择**包含的 MVC 5 控制器视图，使用实体框架**，然后选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="a298a-282">In the **Add Scaffold** dialog box, select **MVC 5 Controller with views, using Entity Framework**, and then choose **Add**.</span></span>

     ![在 Visual Studio 中添加基架对话框](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/add-scaffold.png)

3. <span data-ttu-id="a298a-284">在中**添加控制器**对话框中，进行以下选择，然后选择**添加**:</span><span class="sxs-lookup"><span data-stu-id="a298a-284">In the **Add Controller** dialog box, make the following selections, and then choose **Add**:</span></span>

   - <span data-ttu-id="a298a-285">模型类：**学生 (ContosoUniversity.Models)**。</span><span class="sxs-lookup"><span data-stu-id="a298a-285">Model class: **Student (ContosoUniversity.Models)**.</span></span> <span data-ttu-id="a298a-286">（如果看不到下拉列表中的此选项，生成项目，然后重试。）</span><span class="sxs-lookup"><span data-stu-id="a298a-286">(If you don't see this option in the drop-down list, build the project and try again.)</span></span>
   - <span data-ttu-id="a298a-287">数据上下文类：**SchoolContext (ContosoUniversity.DAL)**。</span><span class="sxs-lookup"><span data-stu-id="a298a-287">Data context class: **SchoolContext (ContosoUniversity.DAL)**.</span></span>
   - <span data-ttu-id="a298a-288">控制器名称：**StudentController** (不 StudentsController)。</span><span class="sxs-lookup"><span data-stu-id="a298a-288">Controller name: **StudentController** (not StudentsController).</span></span>
   - <span data-ttu-id="a298a-289">保留其他字段的默认值。</span><span class="sxs-lookup"><span data-stu-id="a298a-289">Leave the default values for the other fields.</span></span>

     <span data-ttu-id="a298a-290">当您单击**添加**，创建基架*StudentController.cs*文件和一组视图 (*.cshtml*文件) 与控制器的工作。</span><span class="sxs-lookup"><span data-stu-id="a298a-290">When you click **Add**, the scaffolder creates a *StudentController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span> <span data-ttu-id="a298a-291">在将来创建使用实体框架的项目时，还可使用的基架一些附加功能： 创建第一个模型类，不要创建连接字符串，然后在**添加控制器**框中指定**新建数据上下文** 通过选择 **+** 旁边 **数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="a298a-291">In the future when you create projects that use Entity Framework, you can also take advantage of some additional functionality of the scaffolder: create your first model class, don't create a connection string, and then in the **Add Controller** box specify **New data context** by selecting the **+** button next to **Data context class**.</span></span> <span data-ttu-id="a298a-292">基架将创建在`DbContext`类和你的连接字符串以及控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="a298a-292">The scaffolder will create your `DbContext` class and your connection string as well as the controller and views.</span></span>
4. <span data-ttu-id="a298a-293">Visual Studio 将打开*Controllers\StudentController.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="a298a-293">Visual Studio opens the *Controllers\StudentController.cs* file.</span></span> <span data-ttu-id="a298a-294">您看到类变量已创建了实例化数据库上下文对象：</span><span class="sxs-lookup"><span data-stu-id="a298a-294">You see that a class variable has been created that instantiates a database context object:</span></span>

     [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample11.cs)]

     <span data-ttu-id="a298a-295">`Index`操作方法获取的学生列表*学生*实体集通过阅读`Students`数据库上下文实例的属性：</span><span class="sxs-lookup"><span data-stu-id="a298a-295">The `Index` action method gets a list of students from the *Students* entity set by reading the `Students` property of the database context instance:</span></span>

     [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample12.cs)]

     <span data-ttu-id="a298a-296">*Student\Index.cshtml*视图的表中显示此列表：</span><span class="sxs-lookup"><span data-stu-id="a298a-296">The *Student\Index.cshtml* view displays this list in a table:</span></span>

     [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample13.cshtml)]
5. <span data-ttu-id="a298a-297">按 Ctrl + F5 以运行该项目。</span><span class="sxs-lookup"><span data-stu-id="a298a-297">Press Ctrl+F5 to run the project.</span></span> <span data-ttu-id="a298a-298">（如果收到"无法创建卷影副本"错误，关闭浏览器和重试。）</span><span class="sxs-lookup"><span data-stu-id="a298a-298">(If you get a "Cannot create Shadow Copy" error, close the browser and try again.)</span></span>

     <span data-ttu-id="a298a-299">单击**学生**选项卡以查看测试数据的`Seed`插入的方法。</span><span class="sxs-lookup"><span data-stu-id="a298a-299">Click the **Students** tab to see the test data that the `Seed` method inserted.</span></span> <span data-ttu-id="a298a-300">具体取决于如何窄浏览器窗口，您将看到顶部的地址栏中的学生选项卡链接或您必须单击右上角才能看到该链接。</span><span class="sxs-lookup"><span data-stu-id="a298a-300">Depending on how narrow your browser window is, you'll see the Student tab link in the top address bar or you'll have to click the upper right corner to see the link.</span></span>

     ![菜单按钮](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image14.png)

## <a name="view-the-database"></a><span data-ttu-id="a298a-302">查看数据库</span><span class="sxs-lookup"><span data-stu-id="a298a-302">View the database</span></span>

<span data-ttu-id="a298a-303">当运行学生页面和应用程序尝试访问数据库时，EF 发现存在已没有数据库并创建了一个。</span><span class="sxs-lookup"><span data-stu-id="a298a-303">When you ran the Students page and the application tried to access the database, EF discovered that there was no database and created one.</span></span> <span data-ttu-id="a298a-304">然后，EF 运行 seed 方法来使用数据填充数据库。</span><span class="sxs-lookup"><span data-stu-id="a298a-304">EF then ran the seed method to populate the database with data.</span></span>

<span data-ttu-id="a298a-305">你可以使用**服务器资源管理器**或**SQL Server 对象资源管理器**(SSOX) 在 Visual Studio 中查看数据库。</span><span class="sxs-lookup"><span data-stu-id="a298a-305">You can use either **Server Explorer** or **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span> <span data-ttu-id="a298a-306">对于本教程中，你将使用**服务器资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="a298a-306">For this tutorial, you'll use **Server Explorer**.</span></span>

1. <span data-ttu-id="a298a-307">关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="a298a-307">Close the browser.</span></span>
2. <span data-ttu-id="a298a-308">在中**服务器资源管理器**，展开**数据连接**（可能需要首先选择刷新按钮），展开**学校上下文 (ContosoUniversity)**，然后展开**表**以查看新数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="a298a-308">In **Server Explorer**, expand **Data Connections** (you may need to select the refresh button first), expand **School Context (ContosoUniversity)**, and then expand **Tables** to see the tables in your new database.</span></span>

3. <span data-ttu-id="a298a-309">右键单击**学生**表，然后单击**显示表数据**若要查看已创建的列和插入到表的行。</span><span class="sxs-lookup"><span data-stu-id="a298a-309">Right-click the **Student** table and click **Show Table Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

4. <span data-ttu-id="a298a-310">关闭**服务器资源管理器**连接。</span><span class="sxs-lookup"><span data-stu-id="a298a-310">Close the **Server Explorer** connection.</span></span>

<span data-ttu-id="a298a-311">*ContosoUniversity1.mdf*并 *.ldf*数据库文件位于 *%USERPROFILE%* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="a298a-311">The *ContosoUniversity1.mdf* and *.ldf* database files are in the *%USERPROFILE%* folder.</span></span>

<span data-ttu-id="a298a-312">正在使用，因此`DropCreateDatabaseIfModelChanges`初始值设定项，可以现在更改到`Student`类、 运行一次，应用程序和数据库会自动将重新创建，以匹配所做的更改。</span><span class="sxs-lookup"><span data-stu-id="a298a-312">Because you're using the `DropCreateDatabaseIfModelChanges` initializer, you could now make a change to the `Student` class, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="a298a-313">例如，如果您将添加`EmailAddress`属性设置为`Student`类，学生页上再次运行，并可以看到一个新的然后查看表再次，`EmailAddress`列。</span><span class="sxs-lookup"><span data-stu-id="a298a-313">For example, if you add an `EmailAddress` property to the `Student` class, run the Students page again, and then look at the table again, you'll see a new `EmailAddress` column.</span></span>

## <a name="conventions"></a><span data-ttu-id="a298a-314">约定</span><span class="sxs-lookup"><span data-stu-id="a298a-314">Conventions</span></span>

<span data-ttu-id="a298a-315">您必须在实体框架能够为您创建完整的数据库中编写的代码量是由于最小*约定*，或使实体框架的假设。</span><span class="sxs-lookup"><span data-stu-id="a298a-315">The amount of code you had to write in order for Entity Framework to be able to create a complete database for you is minimal because of *conventions*, or assumptions that Entity Framework makes.</span></span> <span data-ttu-id="a298a-316">其中一些已记下或使用而无需你不知道的：</span><span class="sxs-lookup"><span data-stu-id="a298a-316">Some of them have already been noted or were used without your being aware of them:</span></span>

- <span data-ttu-id="a298a-317">复数的形式的实体类名称用作表名。</span><span class="sxs-lookup"><span data-stu-id="a298a-317">The pluralized forms of entity class names are used as table names.</span></span>
- <span data-ttu-id="a298a-318">使用实体属性名作为列名。</span><span class="sxs-lookup"><span data-stu-id="a298a-318">Entity property names are used for column names.</span></span>
- <span data-ttu-id="a298a-319">命名的实体属性`ID`或*classname* `ID`被视为主键属性。</span><span class="sxs-lookup"><span data-stu-id="a298a-319">Entity properties that are named `ID` or *classname* `ID` are recognized as primary key properties.</span></span>
- <span data-ttu-id="a298a-320">如果它名为一个属性将被解释为外键属性 *&lt;导航属性名称&gt;&lt;主键属性名称&gt;* (例如， `StudentID` 为`Student`导航属性所以`Student`实体的主键是`ID`)。</span><span class="sxs-lookup"><span data-stu-id="a298a-320">A property is interpreted as a foreign key property if it's named *&lt;navigation property name&gt;&lt;primary key property name&gt;* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="a298a-321">外键属性还可以进行命名相同只需&lt;主键属性名称&gt;(例如，`EnrollmentID`由于`Enrollment`实体的主键是`EnrollmentID`)。</span><span class="sxs-lookup"><span data-stu-id="a298a-321">Foreign key properties can also be named the same simply &lt;primary key property name&gt; (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="a298a-322">您已了解约定可以重写。</span><span class="sxs-lookup"><span data-stu-id="a298a-322">You've seen that conventions can be overridden.</span></span> <span data-ttu-id="a298a-323">例如，指定表名不应变为复数形式，并稍后您将看到如何显式标记为外键属性的属性。</span><span class="sxs-lookup"><span data-stu-id="a298a-323">For example, you specified that table names shouldn't be pluralized, and you'll see later how to explicitly mark a property as a foreign key property.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="a298a-324">获取代码</span><span class="sxs-lookup"><span data-stu-id="a298a-324">Get the code</span></span>

[<span data-ttu-id="a298a-325">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="a298a-325">Download Completed Project</span></span>](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a><span data-ttu-id="a298a-326">其他资源</span><span class="sxs-lookup"><span data-stu-id="a298a-326">Additional resources</span></span>

<span data-ttu-id="a298a-327">有关 EF 6 的详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="a298a-327">For more about EF 6, see these articles:</span></span>

* [<span data-ttu-id="a298a-328">ASP.NET 数据访问 - 推荐的资源</span><span class="sxs-lookup"><span data-stu-id="a298a-328">ASP.NET Data Access - Recommended Resources</span></span>](../../../../whitepapers/aspnet-data-access-content-map.md)

* [<span data-ttu-id="a298a-329">代码优先约定</span><span class="sxs-lookup"><span data-stu-id="a298a-329">Code First Conventions</span></span>](/ef/ef6/modeling/code-first/conventions/built-in)

* [<span data-ttu-id="a298a-330">创建更复杂的数据模型</span><span class="sxs-lookup"><span data-stu-id="a298a-330">Creating a More Complex Data Model</span></span>](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)

## <a name="next-steps"></a><span data-ttu-id="a298a-331">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a298a-331">Next steps</span></span>

<span data-ttu-id="a298a-332">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="a298a-332">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a298a-333">创建 MVC web 应用</span><span class="sxs-lookup"><span data-stu-id="a298a-333">Created an MVC web app</span></span>
> * <span data-ttu-id="a298a-334">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="a298a-334">Set up the site style</span></span>
> * <span data-ttu-id="a298a-335">已安装的实体框架 6</span><span class="sxs-lookup"><span data-stu-id="a298a-335">Installed Entity Framework 6</span></span>
> * <span data-ttu-id="a298a-336">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="a298a-336">Created the data model</span></span>
> * <span data-ttu-id="a298a-337">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="a298a-337">Created the database context</span></span>
> * <span data-ttu-id="a298a-338">使用测试数据初始化的 DB</span><span class="sxs-lookup"><span data-stu-id="a298a-338">Initialized DB with test data</span></span>
> * <span data-ttu-id="a298a-339">设置为使用 LocalDB 的 EF 6</span><span class="sxs-lookup"><span data-stu-id="a298a-339">Set up EF 6 to use LocalDB</span></span>
> * <span data-ttu-id="a298a-340">创建的控制器和视图</span><span class="sxs-lookup"><span data-stu-id="a298a-340">Created controller and views</span></span>
> * <span data-ttu-id="a298a-341">查看数据库</span><span class="sxs-lookup"><span data-stu-id="a298a-341">Viewed the database</span></span>

<span data-ttu-id="a298a-342">转到下一步的文章，了解如何查看和自定义创建、 读取、 更新、 删除 (CRUD) 在控制器和视图中的代码。</span><span class="sxs-lookup"><span data-stu-id="a298a-342">Advance to the next article to learn how to review and customize the create, read, update, delete (CRUD) code in your controllers and views.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="a298a-343">实现基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="a298a-343">Implement basic CRUD functionality</span></span>](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)