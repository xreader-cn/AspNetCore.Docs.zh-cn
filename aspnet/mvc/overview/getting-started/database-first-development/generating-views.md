---
uid: mvc/overview/getting-started/database-first-development/generating-views
title: 教程：使用 ASP.NET MVC 应用程序的 EF Database First 生成的视图
description: 本文重点介绍使用 ASP.NET 基架生成控制器和视图。
author: Rick-Anderson
ms.author: riande
ms.date: 01/23/2019
ms.topic: tutorial
ms.assetid: 669367cf-8e30-4eb6-821d-10a7d9bb906c
msc.legacyurl: /mvc/overview/getting-started/database-first-development/generating-views
msc.type: authoredcontent
ms.openlocfilehash: e1f6646cdf10d293268b92f44b018709e70c0f86
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889777"
---
# <a name="tutorial-generate-views-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="53c7c-103">教程：使用 ASP.NET MVC 应用程序的 EF Database First 生成的视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-103">Tutorial: Generate views for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="53c7c-104">使用 MVC、 Entity Framework 和 ASP.NET 基架，可以创建提供接口的现有数据库的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="53c7c-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="53c7c-105">本系列教程演示了如何自动生成代码，使用户能够显示、 编辑、 创建和删除驻留在数据库表中的数据。</span><span class="sxs-lookup"><span data-stu-id="53c7c-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="53c7c-106">生成的代码对应于数据库表中的列。</span><span class="sxs-lookup"><span data-stu-id="53c7c-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="53c7c-107">本文重点介绍使用 ASP.NET 基架生成控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="53c7c-107">This article focuses on using ASP.NET Scaffolding to generate the controllers and views.</span></span>

<span data-ttu-id="53c7c-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="53c7c-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="53c7c-109">添加基架</span><span class="sxs-lookup"><span data-stu-id="53c7c-109">Add scaffold</span></span>
> * <span data-ttu-id="53c7c-110">将链接添加到新的视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-110">Add links to new views</span></span>
> * <span data-ttu-id="53c7c-111">显示学生视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-111">Display student views</span></span>
> * <span data-ttu-id="53c7c-112">显示注册视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-112">Display enrollment views</span></span>

## <a name="prerequisite"></a><span data-ttu-id="53c7c-113">必备组件</span><span class="sxs-lookup"><span data-stu-id="53c7c-113">Prerequisite</span></span>

* [<span data-ttu-id="53c7c-114">创建 web 应用程序和数据模型</span><span class="sxs-lookup"><span data-stu-id="53c7c-114">Create the web application and data models</span></span>](creating-the-web-application.md)

## <a name="add-scaffold"></a><span data-ttu-id="53c7c-115">添加基架</span><span class="sxs-lookup"><span data-stu-id="53c7c-115">Add scaffold</span></span>

<span data-ttu-id="53c7c-116">已准备好生成将提供标准数据操作，以便在 model 类的代码。</span><span class="sxs-lookup"><span data-stu-id="53c7c-116">You are ready to generate code that will provide standard data operations for the model classes.</span></span> <span data-ttu-id="53c7c-117">通过添加基架项添加代码。</span><span class="sxs-lookup"><span data-stu-id="53c7c-117">You add the code by adding a scaffold item.</span></span> <span data-ttu-id="53c7c-118">有许多选项的类型的基架可以添加;在本教程中，基架将包括控制器和对应于上一节中创建的 Student 和注册模型的视图。</span><span class="sxs-lookup"><span data-stu-id="53c7c-118">There are many options for the type of scaffolding you can add; in this tutorial, the scaffold will include a controller and views that correspond to the Student and Enrollment models you created in the previous section.</span></span>

<span data-ttu-id="53c7c-119">若要维护你的项目中的一致性，将添加新控制器到现有**控制器**文件夹。</span><span class="sxs-lookup"><span data-stu-id="53c7c-119">To maintain consistency in your project, you will add the new controller to the existing **Controllers** folder.</span></span> <span data-ttu-id="53c7c-120">右键单击**控制器**文件夹，然后选择**添加** > **新基架项**。</span><span class="sxs-lookup"><span data-stu-id="53c7c-120">Right-click the **Controllers** folder, and select **Add** > **New Scaffolded Item**.</span></span>

<span data-ttu-id="53c7c-121">选择**包含的 MVC 5 控制器视图，使用实体框架**选项。</span><span class="sxs-lookup"><span data-stu-id="53c7c-121">Select the **MVC 5 Controller with views, using Entity Framework** option.</span></span> <span data-ttu-id="53c7c-122">此选项将生成控制器和视图更新、 删除、 创建和显示模型中的数据。</span><span class="sxs-lookup"><span data-stu-id="53c7c-122">This option will generate the controller and views for updating, deleting, creating and displaying the data in your model.</span></span>

![添加 mvc 控制器](generating-views/_static/image2.png)

<span data-ttu-id="53c7c-124">选择**学生 (ContosoSite.Models)** model 类并选择**ContosoUniversityDataEntities (ContosoSite.Models)** 上下文类。</span><span class="sxs-lookup"><span data-stu-id="53c7c-124">Select **Student (ContosoSite.Models)** for the model class and select the **ContosoUniversityDataEntities (ContosoSite.Models)** for the context class.</span></span> <span data-ttu-id="53c7c-125">保留作为控制器名称**StudentsController**。</span><span class="sxs-lookup"><span data-stu-id="53c7c-125">Keep the controller name as **StudentsController**.</span></span>

<span data-ttu-id="53c7c-126">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="53c7c-126">Click **Add**.</span></span>

<span data-ttu-id="53c7c-127">如果收到错误，则可能是因为未生成上一节中的项目。</span><span class="sxs-lookup"><span data-stu-id="53c7c-127">If you receive an error, it may be because you did not build the project in the previous section.</span></span> <span data-ttu-id="53c7c-128">如果是这样，请尝试生成项目，，然后再次添加基架的项。</span><span class="sxs-lookup"><span data-stu-id="53c7c-128">If so, try building the project, and then add the scaffolded item again.</span></span>

<span data-ttu-id="53c7c-129">代码生成过程完成后，你将在项目中看到新的控制器和视图**控制器**并**视图** > **学生**文件夹.</span><span class="sxs-lookup"><span data-stu-id="53c7c-129">After the code generation process is complete, you will see a new controller and views in your project's **Controllers** and **Views** > **Students** folders.</span></span>


<span data-ttu-id="53c7c-130">同样，执行相同的步骤，但添加的基本框架**注册**类。</span><span class="sxs-lookup"><span data-stu-id="53c7c-130">Perform the same steps again, but add a scaffold for the **Enrollment** class.</span></span> <span data-ttu-id="53c7c-131">完成后，您拥有**EnrollmentsController.cs**文件和文件夹下的**视图**名为**注册**与创建、 删除、 详细信息、 编辑和索引视图。</span><span class="sxs-lookup"><span data-stu-id="53c7c-131">When finished, you have an **EnrollmentsController.cs** file, and a folder under **Views** named **Enrollments** with the Create, Delete, Details, Edit and Index views.</span></span>

## <a name="add-links-to-new-views"></a><span data-ttu-id="53c7c-132">将链接添加到新的视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-132">Add links to new views</span></span>

<span data-ttu-id="53c7c-133">若要使你更轻松地导航到新视图，可以索引视图中添加几个超链接，面向学生和注册。</span><span class="sxs-lookup"><span data-stu-id="53c7c-133">To make it easier for you to navigate to your new views, you can add a couple of hyperlinks to the Index views for students and enrollments.</span></span> <span data-ttu-id="53c7c-134">打开的文件**视图** > **家庭** > *Index.cshtml*，这是你的站点的主页。</span><span class="sxs-lookup"><span data-stu-id="53c7c-134">Open the file at **Views** > **Home** > *Index.cshtml*, which is the home page for your site.</span></span> <span data-ttu-id="53c7c-135">添加 jumbotron 下面的代码。</span><span class="sxs-lookup"><span data-stu-id="53c7c-135">Add the following code below the jumbotron.</span></span>

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

<span data-ttu-id="53c7c-136">对于 ActionLink 方法中，第一个参数是要显示的链接中的文本。</span><span class="sxs-lookup"><span data-stu-id="53c7c-136">For the ActionLink method, the first parameter is the text to display in the link.</span></span> <span data-ttu-id="53c7c-137">第二个参数是操作，第三个参数是控制器的名称。</span><span class="sxs-lookup"><span data-stu-id="53c7c-137">The second parameter is the action and the third parameter is the name of the controller.</span></span> <span data-ttu-id="53c7c-138">例如，第一个链接指向 StudentsController 中的索引操作。</span><span class="sxs-lookup"><span data-stu-id="53c7c-138">For example, the first link points to the Index action in StudentsController.</span></span> <span data-ttu-id="53c7c-139">实际的超链接，这些值从构造。</span><span class="sxs-lookup"><span data-stu-id="53c7c-139">The actual hyperlink is constructed from these values.</span></span> <span data-ttu-id="53c7c-140">第一个链接最终用户可转到**Index.cshtml**文件内**视图/学生**文件夹。</span><span class="sxs-lookup"><span data-stu-id="53c7c-140">The first link ultimately takes users to the **Index.cshtml** file within the **Views/Students** folder.</span></span>

## <a name="display-student-views"></a><span data-ttu-id="53c7c-141">显示学生视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-141">Display student views</span></span>

<span data-ttu-id="53c7c-142">将验证正确添加到你的项目的代码显示学生列表，并使用户能够编辑、 创建或删除数据库中的学生记录。</span><span class="sxs-lookup"><span data-stu-id="53c7c-142">You will verify that the code added to your project correctly displays a list of the students, and enables users to edit, create, or delete the student records in the database.</span></span>

<span data-ttu-id="53c7c-143">右键单击**视图** > **主页** > *Index.cshtml*文件，并选择**用浏览器查看**。</span><span class="sxs-lookup"><span data-stu-id="53c7c-143">Right-click the **Views** > **Home** > *Index.cshtml* file, and select **View in Browser**.</span></span> <span data-ttu-id="53c7c-144">在应用程序主页上，选择**学生列表**。</span><span class="sxs-lookup"><span data-stu-id="53c7c-144">On the application home page, select **List of students**.</span></span>

![](generating-views/_static/image6.png)

<span data-ttu-id="53c7c-145">上**索引**页上，请注意，若要修改此数据的学生和链接的列表。</span><span class="sxs-lookup"><span data-stu-id="53c7c-145">On the **Index** page, notice the list of the students and links to modify this data.</span></span> <span data-ttu-id="53c7c-146">选择**创建新**链接并为新的学生提供一些值。</span><span class="sxs-lookup"><span data-stu-id="53c7c-146">Select the **Create New** link and provide some values for a new student.</span></span> <span data-ttu-id="53c7c-147">单击**创建**，并请注意，新学生添加到列表。</span><span class="sxs-lookup"><span data-stu-id="53c7c-147">Click **Create**, and notice the new student is added to your list.</span></span>

<span data-ttu-id="53c7c-148">重新**索引**页上，选择**编辑**链接，并更改值的一些学生。</span><span class="sxs-lookup"><span data-stu-id="53c7c-148">Back on the **Index** page, select the **Edit** link, and change some of the values for a student.</span></span> <span data-ttu-id="53c7c-149">单击**保存**，您会发现已更改学生记录。</span><span class="sxs-lookup"><span data-stu-id="53c7c-149">Click **Save**, and notice the student record has been changed.</span></span>

<span data-ttu-id="53c7c-150">最后，选择**删除**链接，然后确认你想要通过单击删除的记录**删除**按钮。</span><span class="sxs-lookup"><span data-stu-id="53c7c-150">Finally, select the **Delete** link and confirm that you want to delete the record by clicking the **Delete** button.</span></span>

<span data-ttu-id="53c7c-151">无需编写任何代码，您已添加在 Student 表中执行常见操作的数据的视图。</span><span class="sxs-lookup"><span data-stu-id="53c7c-151">Without writing any code, you have added views that perform common operations on the data in the Student table.</span></span>

<span data-ttu-id="53c7c-152">您可能已经注意到一个字段的文本标签基于的数据库属性 (如**LastName**) 这不一定是你想要在网页上显示。</span><span class="sxs-lookup"><span data-stu-id="53c7c-152">You may have noticed that the text label for a field is based on the database property (such as **LastName**) which is not necessarily what you want to display on the web page.</span></span> <span data-ttu-id="53c7c-153">例如，可能想要进行的标签**姓氏**。</span><span class="sxs-lookup"><span data-stu-id="53c7c-153">For example, you may prefer the label to be **Last Name**.</span></span> <span data-ttu-id="53c7c-154">本教程的后面，则将修复此显示问题。</span><span class="sxs-lookup"><span data-stu-id="53c7c-154">You will fix this display issue later in the tutorial.</span></span>

## <a name="display-enrollment-views"></a><span data-ttu-id="53c7c-155">显示注册视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-155">Display enrollment views</span></span>

<span data-ttu-id="53c7c-156">你的数据库包括一个对多关系学生和注册表中与 Course 和 Enrollment 表之间的一个对多关系之间。</span><span class="sxs-lookup"><span data-stu-id="53c7c-156">Your database includes a one-to-many relationship between the Student and Enrollment tables, and a one-to-many relationship between the Course and Enrollment tables.</span></span> <span data-ttu-id="53c7c-157">为注册视图正确处理这些关系。</span><span class="sxs-lookup"><span data-stu-id="53c7c-157">The views for Enrollment correctly handle these relationships.</span></span> <span data-ttu-id="53c7c-158">导航到站点并选择主页**注册列表**链接，然后**创建新**链接。</span><span class="sxs-lookup"><span data-stu-id="53c7c-158">Navigate to the home page for your site and select the **List of enrollments** link and then the **Create New** link.</span></span>

<span data-ttu-id="53c7c-159">该视图显示用于创建新的注册记录的窗体。</span><span class="sxs-lookup"><span data-stu-id="53c7c-159">The view displays a form for creating a new enrollment record.</span></span> <span data-ttu-id="53c7c-160">具体而言，请注意，包含窗体**CourseID**下拉列表和一个**StudentID**下拉列表。</span><span class="sxs-lookup"><span data-stu-id="53c7c-160">In particular, notice that the form contains a **CourseID** drop-down list and a **StudentID** drop-down list.</span></span> <span data-ttu-id="53c7c-161">二者都被填充使用相关表中的值。</span><span class="sxs-lookup"><span data-stu-id="53c7c-161">Both are populated with values from the related tables.</span></span>

<span data-ttu-id="53c7c-162">此外，验证所提供的值将自动应用基于字段的数据类型。</span><span class="sxs-lookup"><span data-stu-id="53c7c-162">Furthermore, validation of the provided values is automatically applied based on the data type of the field.</span></span> <span data-ttu-id="53c7c-163">**等级**需要一个数字，因此如果你尝试提供不兼容的值显示一条错误消息：*字段级必须是数字。*</span><span class="sxs-lookup"><span data-stu-id="53c7c-163">**Grade** requires a number, so an error message is displayed if you try to provide an incompatible value: *The field Grade must be a number.*</span></span>

<span data-ttu-id="53c7c-164">您已验证的自动生成的视图使用户能够处理在数据库中的数据。</span><span class="sxs-lookup"><span data-stu-id="53c7c-164">You have verified that the automatically-generated views enable users to work with the data in the database.</span></span> <span data-ttu-id="53c7c-165">在本系列中下一个教程中，将更新数据库，并在 web 应用程序中进行相应更改。</span><span class="sxs-lookup"><span data-stu-id="53c7c-165">In the next tutorial in this series, you will update the database and make the corresponding changes in the web application.</span></span>

## <a name="next-steps"></a><span data-ttu-id="53c7c-166">后续步骤</span><span class="sxs-lookup"><span data-stu-id="53c7c-166">Next steps</span></span>

<span data-ttu-id="53c7c-167">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="53c7c-167">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="53c7c-168">添加基架</span><span class="sxs-lookup"><span data-stu-id="53c7c-168">Added scaffold</span></span>
> * <span data-ttu-id="53c7c-169">添加了指向新的视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-169">Added links to new views</span></span>
> * <span data-ttu-id="53c7c-170">显示的学生视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-170">Displayed student views</span></span>
> * <span data-ttu-id="53c7c-171">显示的注册视图</span><span class="sxs-lookup"><span data-stu-id="53c7c-171">Displayed enrollment views</span></span>

<span data-ttu-id="53c7c-172">转到下一步的文章，了解如何更改数据库。</span><span class="sxs-lookup"><span data-stu-id="53c7c-172">Advance to the next article to learn how to change the database.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="53c7c-173">更改数据库</span><span class="sxs-lookup"><span data-stu-id="53c7c-173">Change the database</span></span>](changing-the-database.md)