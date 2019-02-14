---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: 教程：在 ASP.NET MVC 应用中使用 EF 更新相关的数据
description: 在本教程中将更新相关的数据。 对于大多数关系，这可以通过更新外键字段或导航属性。
author: tdykstra
ms.author: riande
ms.date: 01/19/2019
ms.topic: tutorial
ms.assetid: 7ba88418-5d0a-437d-b6dc-7c3816d4ec07
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 1ef4242ff3bd1dd86f4d58bd04ba08e8b90fdaa4
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248272"
---
<a name="updating-related-data-with-the-entity-framework-in-an-aspnet-mvc-application"></a><span data-ttu-id="cfd94-104">使用实体框架在 ASP.NET MVC 应用程序中更新相关的数据</span><span class="sxs-lookup"><span data-stu-id="cfd94-104">Updating Related Data with the Entity Framework in an ASP.NET MVC Application</span></span>
====================


# <a name="tutorial-update-related-data-with-ef-in-an-aspnet-mvc-app"></a><span data-ttu-id="cfd94-105">教程：在 ASP.NET MVC 应用中使用 EF 更新相关的数据</span><span class="sxs-lookup"><span data-stu-id="cfd94-105">Tutorial: Update related data with EF in an ASP.NET MVC app</span></span>

<span data-ttu-id="cfd94-106">上一教程中显示相关的数据。</span><span class="sxs-lookup"><span data-stu-id="cfd94-106">In the previous tutorial you displayed related data.</span></span> <span data-ttu-id="cfd94-107">在本教程中将更新相关的数据。</span><span class="sxs-lookup"><span data-stu-id="cfd94-107">In this tutorial you'll update related data.</span></span> <span data-ttu-id="cfd94-108">对于大多数关系，这可以通过更新外键字段或导航属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-108">For most relationships, this can be done by updating either foreign key fields or navigation properties.</span></span> <span data-ttu-id="cfd94-109">对于多对多关系，实体框架不会联接表直接公开，因此添加和删除实体与相应的导航属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-109">For many-to-many relationships, the Entity Framework doesn't expose the join table directly, so you add and remove entities to and from the appropriate navigation properties.</span></span>

<span data-ttu-id="cfd94-110">下图是一些将会用到的页面。</span><span class="sxs-lookup"><span data-stu-id="cfd94-110">The following illustrations show some of the pages that you'll work with.</span></span>

![Course_create_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

![讲师编辑课程](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

<span data-ttu-id="cfd94-114">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="cfd94-114">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cfd94-115">自定义课程页</span><span class="sxs-lookup"><span data-stu-id="cfd94-115">Customize courses pages</span></span>
> * <span data-ttu-id="cfd94-116">将 office 添加到讲师页</span><span class="sxs-lookup"><span data-stu-id="cfd94-116">Add office to instructors page</span></span>
> * <span data-ttu-id="cfd94-117">向讲师页添加课程</span><span class="sxs-lookup"><span data-stu-id="cfd94-117">Add courses to instructors page</span></span>
> * <span data-ttu-id="cfd94-118">更新 DeleteConfirmed</span><span class="sxs-lookup"><span data-stu-id="cfd94-118">Update DeleteConfirmed</span></span>
> * <span data-ttu-id="cfd94-119">向创建页添加办公室位置和课程</span><span class="sxs-lookup"><span data-stu-id="cfd94-119">Add office location and courses to the Create page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cfd94-120">系统必备</span><span class="sxs-lookup"><span data-stu-id="cfd94-120">Prerequisites</span></span>

* [<span data-ttu-id="cfd94-121">读取相关数据</span><span class="sxs-lookup"><span data-stu-id="cfd94-121">Reading Related Data</span></span>](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="customize-courses-pages"></a><span data-ttu-id="cfd94-122">自定义课程页</span><span class="sxs-lookup"><span data-stu-id="cfd94-122">Customize courses pages</span></span>

<span data-ttu-id="cfd94-123">创建新的课程实体时，新实体必须与现有院系有关系。</span><span class="sxs-lookup"><span data-stu-id="cfd94-123">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="cfd94-124">为此，基架代码需包括控制器方法、创建视图和编辑视图，且视图中应包括用于选择院系的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="cfd94-124">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="cfd94-125">下拉列表设置`Course.DepartmentID`外键属性，而这正是 Entity Framework 加载所需`Department`导航属性具有相应`Department`实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-125">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate `Department` entity.</span></span> <span data-ttu-id="cfd94-126">将用到基架代码，但需对其稍作更改，以便添加错误处理和对下拉列表进行排序。</span><span class="sxs-lookup"><span data-stu-id="cfd94-126">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="cfd94-127">在中*CourseController.cs*，删除四个`Create`和`Edit`方法，并用它们替换下面的代码：</span><span class="sxs-lookup"><span data-stu-id="cfd94-127">In *CourseController.cs*, delete the four `Create` and `Edit` methods and replace them with the following code:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,11-12,19-25,40,44,46,48-78)]

<span data-ttu-id="cfd94-128">以下代码添加到`using`文件的开头的语句：</span><span class="sxs-lookup"><span data-stu-id="cfd94-128">Add the following `using` statement at the beginning of the file:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

<span data-ttu-id="cfd94-129">`PopulateDepartmentsDropDownList`方法获取按名称排序的所有部门列表，创建`SelectList`集合有关的下拉列表，并将集合传递给视图`ViewBag`属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-129">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in a `ViewBag` property.</span></span> <span data-ttu-id="cfd94-130">该方法可以使用可选的 `selectedDepartment` 参数，而调用的代码可以通过该参数来指定呈现下拉列表时被选择的项。</span><span class="sxs-lookup"><span data-stu-id="cfd94-130">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="cfd94-131">该视图会将名称传递`DepartmentID`到[DropDownList](../../older-versions/working-with-the-dropdownlist-box-and-jquery/using-the-dropdownlist-helper-with-aspnet-mvc.md)帮助器，该帮助器就会知道要查找的`ViewBag`对象`SelectList`名为`DepartmentID`。</span><span class="sxs-lookup"><span data-stu-id="cfd94-131">The view will pass the name `DepartmentID` to the [DropDownList](../../older-versions/working-with-the-dropdownlist-box-and-jquery/using-the-dropdownlist-helper-with-aspnet-mvc.md) helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named `DepartmentID`.</span></span>

<span data-ttu-id="cfd94-132">`HttpGet` `Create`方法调用`PopulateDepartmentsDropDownList`方法而无需设置的所选的项，因为新课程的院系尚未建立：</span><span class="sxs-lookup"><span data-stu-id="cfd94-132">The `HttpGet` `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department is not established yet:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

<span data-ttu-id="cfd94-133">`HttpGet` `Edit`方法设置选定的项，基于已分配给正在编辑的课程的院系 ID:</span><span class="sxs-lookup"><span data-stu-id="cfd94-133">The `HttpGet` `Edit` method sets the selected item, based on the ID of the department that is already assigned to the course being edited:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=12)]

<span data-ttu-id="cfd94-134">`HttpPost`两个方法`Create`和`Edit`还包括设置选定的项时它们在错误之后重新显示页面的代码：</span><span class="sxs-lookup"><span data-stu-id="cfd94-134">The `HttpPost` methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=6)]

<span data-ttu-id="cfd94-135">此代码可确保当页面重新显示错误消息，选择任何院系保持所选。</span><span class="sxs-lookup"><span data-stu-id="cfd94-135">This code ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

<span data-ttu-id="cfd94-136">课程视图都已基架的 department 字段的下拉列表，但不想 DepartmentID 标题为此字段，以便进行以下突出显示将更改为*Views\Course\Create.cshtml*文件为更改标题。</span><span class="sxs-lookup"><span data-stu-id="cfd94-136">The Course views are already scaffolded with drop-down lists for the department field, but you don't want the DepartmentID caption for this field, so make the following highlighted change to the *Views\Course\Create.cshtml* file to change the caption.</span></span>

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml?highlight=43)]

<span data-ttu-id="cfd94-137">进行中相同的更改*Views\Course\Edit.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="cfd94-137">Make the same change in *Views\Course\Edit.cshtml*.</span></span>

<span data-ttu-id="cfd94-138">通常情况下基架不会创建基架主键因为键值由数据库生成和不能更改并不是有意义的值来向用户显示。</span><span class="sxs-lookup"><span data-stu-id="cfd94-138">Normally the scaffolder doesn't scaffold a primary key because the key value is generated by the database and can't be changed and isn't a meaningful value to be displayed to users.</span></span> <span data-ttu-id="cfd94-139">基架为 Course 实体包括文本框`CourseID`字段，因为它能够理解的`DatabaseGeneratedOption.None`属性意味着用户应该可以输入的主键值。</span><span class="sxs-lookup"><span data-stu-id="cfd94-139">For Course entities the scaffolder does include an text box for the `CourseID` field because it understands that the `DatabaseGeneratedOption.None` attribute means the user should be able enter the primary key value.</span></span> <span data-ttu-id="cfd94-140">但并不理解，因为的数量是有意义你想要在其他视图中看到它，因此需要手动添加它。</span><span class="sxs-lookup"><span data-stu-id="cfd94-140">But it doesn't understand that because the number is meaningful you want to see it in the other views, so you need to add it manually.</span></span>

<span data-ttu-id="cfd94-141">在中*Views\Course\Edit.cshtml*，添加一个课程编号字段之前**标题**字段。</span><span class="sxs-lookup"><span data-stu-id="cfd94-141">In *Views\Course\Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="cfd94-142">因为它是主键，它将显示，但不能更改。</span><span class="sxs-lookup"><span data-stu-id="cfd94-142">Because it's the primary key, it's displayed, but it can't be changed.</span></span>

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cshtml)]

<span data-ttu-id="cfd94-143">已存在一个隐藏的字段 (`Html.HiddenFor`帮助器) 编辑视图中的课程编号。</span><span class="sxs-lookup"><span data-stu-id="cfd94-143">There's already a hidden field (`Html.HiddenFor` helper) for the course number in the Edit view.</span></span> <span data-ttu-id="cfd94-144">添加*Html.LabelFor*帮助程序也同样需要隐藏字段，因为它不会导致当用户单击时，已发布数据中包括课程编号**保存**编辑页上。</span><span class="sxs-lookup"><span data-stu-id="cfd94-144">Adding an *Html.LabelFor* helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the Edit page.</span></span>

<span data-ttu-id="cfd94-145">在中*Views\Course\Delete.cshtml*并*Views\Course\Details.cshtml*，更改为"部门"院系名称标题从"名称"并添加一个课程编号字段之前**标题**字段。</span><span class="sxs-lookup"><span data-stu-id="cfd94-145">In *Views\Course\Delete.cshtml* and *Views\Course\Details.cshtml*, change the department name caption from "Name" to "Department" and add a course number field before the **Title** field.</span></span>

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cshtml?highlight=2,9-15)]

<span data-ttu-id="cfd94-146">运行**创建**页面 (显示课程索引页，然后单击**创建新**) 并输入新课程的数据：</span><span class="sxs-lookup"><span data-stu-id="cfd94-146">Run the **Create** page (display the Course Index page and click **Create New**) and enter data for a new course:</span></span>

| <span data-ttu-id="cfd94-147">值</span><span class="sxs-lookup"><span data-stu-id="cfd94-147">Value</span></span> | <span data-ttu-id="cfd94-148">设置</span><span class="sxs-lookup"><span data-stu-id="cfd94-148">Setting</span></span> |
| ----- | ------- |
| <span data-ttu-id="cfd94-149">数字</span><span class="sxs-lookup"><span data-stu-id="cfd94-149">Number</span></span> | <span data-ttu-id="cfd94-150">输入*1000年*。</span><span class="sxs-lookup"><span data-stu-id="cfd94-150">Enter *1000*.</span></span> |
| <span data-ttu-id="cfd94-151">标题</span><span class="sxs-lookup"><span data-stu-id="cfd94-151">Title</span></span> | <span data-ttu-id="cfd94-152">输入*代数*。</span><span class="sxs-lookup"><span data-stu-id="cfd94-152">Enter *Algebra*.</span></span> |
| <span data-ttu-id="cfd94-153">信用</span><span class="sxs-lookup"><span data-stu-id="cfd94-153">Credits</span></span> | <span data-ttu-id="cfd94-154">输入*4*。</span><span class="sxs-lookup"><span data-stu-id="cfd94-154">Enter *4*.</span></span> |
|<span data-ttu-id="cfd94-155">Department</span><span class="sxs-lookup"><span data-stu-id="cfd94-155">Department</span></span> | <span data-ttu-id="cfd94-156">选择**数学**。</span><span class="sxs-lookup"><span data-stu-id="cfd94-156">Select **Mathematics**.</span></span> |

<span data-ttu-id="cfd94-157">单击 **“创建”**。</span><span class="sxs-lookup"><span data-stu-id="cfd94-157">Click **Create**.</span></span> <span data-ttu-id="cfd94-158">课程索引页将显示新课程添加到列表中。</span><span class="sxs-lookup"><span data-stu-id="cfd94-158">The Course Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="cfd94-159">索引页列表中的院系名称来自导航属性，表明已正确建立关系。</span><span class="sxs-lookup"><span data-stu-id="cfd94-159">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="cfd94-160">运行**编辑**页面 (显示课程索引页，然后单击**编辑**课程上)。</span><span class="sxs-lookup"><span data-stu-id="cfd94-160">Run the **Edit** page (display the Course Index page and click **Edit** on a course).</span></span>

<span data-ttu-id="cfd94-161">更改页面上的数据，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="cfd94-161">Change data on the page and click **Save**.</span></span> <span data-ttu-id="cfd94-162">课程索引页将显示已更新的课程数据。</span><span class="sxs-lookup"><span data-stu-id="cfd94-162">The Course Index page is displayed with the updated course data.</span></span>

## <a name="add-office-to-instructors-page"></a><span data-ttu-id="cfd94-163">将 office 添加到讲师页</span><span class="sxs-lookup"><span data-stu-id="cfd94-163">Add office to instructors page</span></span>

<span data-ttu-id="cfd94-164">编辑讲师记录时，有时希望能更新讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="cfd94-164">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="cfd94-165">`Instructor`实体具有对零或一一的关系与`OfficeAssignment`实体，这意味着必须处理以下情况：</span><span class="sxs-lookup"><span data-stu-id="cfd94-165">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity, which means you must handle the following situations:</span></span>

- <span data-ttu-id="cfd94-166">如果用户清除办公室分配，并且它最初具有一个值，必须删除并删除`OfficeAssignment`实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-166">If the user clears the office assignment and it originally had a value, you must remove and delete the `OfficeAssignment` entity.</span></span>
- <span data-ttu-id="cfd94-167">如果用户输入了办公室分配值并且该值最初为空，则必须创建一个新`OfficeAssignment`实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-167">If the user enters an office assignment value and it originally was empty, you must create a new `OfficeAssignment` entity.</span></span>
- <span data-ttu-id="cfd94-168">如果用户更改了办公室分配的值，则必须更改中的现有值`OfficeAssignment`实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-168">If the user changes the value of an office assignment, you must change the value in an existing `OfficeAssignment` entity.</span></span>

<span data-ttu-id="cfd94-169">打开*InstructorController.cs*并查看`HttpGet``Edit`方法：</span><span class="sxs-lookup"><span data-stu-id="cfd94-169">Open *InstructorController.cs* and look at the `HttpGet` `Edit` method:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

<span data-ttu-id="cfd94-170">基架的代码不符合要求。</span><span class="sxs-lookup"><span data-stu-id="cfd94-170">The scaffolded code here isn't what you want.</span></span> <span data-ttu-id="cfd94-171">它设置数据的下拉列表中，但您所需要的是文本框。</span><span class="sxs-lookup"><span data-stu-id="cfd94-171">It's setting up data for a drop-down list, but you what you need is a text box.</span></span> <span data-ttu-id="cfd94-172">此方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="cfd94-172">Replace this method with the following code:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs?highlight=7-10)]

<span data-ttu-id="cfd94-173">此代码将删除`ViewBag`语句，并将关联的预先加载添加`OfficeAssignment`实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-173">This code drops the `ViewBag` statement and adds eager loading for the associated `OfficeAssignment` entity.</span></span> <span data-ttu-id="cfd94-174">无法执行与预先加载`Find`方法，因此`Where`和`Single`改为使用方法来选择讲师。</span><span class="sxs-lookup"><span data-stu-id="cfd94-174">You can't perform eager loading with the `Find` method, so the `Where` and `Single` methods are used instead to select the instructor.</span></span>

<span data-ttu-id="cfd94-175">替换`HttpPost``Edit`用下面的代码的方法。</span><span class="sxs-lookup"><span data-stu-id="cfd94-175">Replace the `HttpPost` `Edit` method with the following code.</span></span> <span data-ttu-id="cfd94-176">用于处理办公室分配更新：</span><span class="sxs-lookup"><span data-stu-id="cfd94-176">which handles office assignment updates:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

<span data-ttu-id="cfd94-177">对引用`RetryLimitExceededException`需要`using`语句; 若要将其添加-将鼠标悬停`RetryLimitExceededException`。</span><span class="sxs-lookup"><span data-stu-id="cfd94-177">The reference to `RetryLimitExceededException` requires a `using` statement; to add it - hover your mouse over `RetryLimitExceededException`.</span></span> <span data-ttu-id="cfd94-178">将显示以下消息：![ 重试异常消息](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image13.png)</span><span class="sxs-lookup"><span data-stu-id="cfd94-178">The following message appears: ![ Retry exception message](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image13.png)</span></span>


<span data-ttu-id="cfd94-179">选择**显示 potentital 修复**，然后**使用 System.Data.Entity.Infrastructure**</span><span class="sxs-lookup"><span data-stu-id="cfd94-179">Select **Show potentital fixes**, then **using System.Data.Entity.Infrastructure**</span></span>

![解析为重试异常](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image14.png)

<span data-ttu-id="cfd94-181">该代码执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cfd94-181">The code does the following:</span></span>

- <span data-ttu-id="cfd94-182">将方法名称更改为`EditPost`由于签名现与相同`HttpGet`方法 (`ActionName`特性指定仍然使用 /Edit/ URL)。</span><span class="sxs-lookup"><span data-stu-id="cfd94-182">Changes the method name to `EditPost` because the signature is now the same as the `HttpGet` method (the `ActionName` attribute specifies that the /Edit/ URL is still used).</span></span>
- <span data-ttu-id="cfd94-183">使用 `OfficeAssignment` 导航属性的预先加载从数据库获取当前的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-183">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="cfd94-184">这是与中的操作相同`HttpGet``Edit`方法。</span><span class="sxs-lookup"><span data-stu-id="cfd94-184">This is the same as what you did in the `HttpGet` `Edit` method.</span></span>
- <span data-ttu-id="cfd94-185">用模型绑定器中的值更新检索到的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-185">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="cfd94-186">[TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.108).aspx)使用重载使你能够*允许列表*你想要包括的属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-186">The [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.108).aspx) overload used enables you to *whitelist* the properties you want to include.</span></span> <span data-ttu-id="cfd94-187">这可以防止过度提交，如中所述[第二个教程](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)。</span><span class="sxs-lookup"><span data-stu-id="cfd94-187">This prevents over-posting, as explained in [the second tutorial](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md).</span></span>

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]
- <span data-ttu-id="cfd94-188">如果办公室位置为空，则设置`Instructor.OfficeAssignment`属性设置为 null，以便中的相关的行`OfficeAssignment`表将被删除。</span><span class="sxs-lookup"><span data-stu-id="cfd94-188">If the office location is blank, sets the `Instructor.OfficeAssignment` property to null so that the related row in the `OfficeAssignment` table will be deleted.</span></span>

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]
- <span data-ttu-id="cfd94-189">将更改保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="cfd94-189">Saves the changes to the database.</span></span>

<span data-ttu-id="cfd94-190">在*Views\Instructor\Edit.cshtml*后面`div`元素**雇佣日期**字段中，添加新的字段编辑办公室位置：</span><span class="sxs-lookup"><span data-stu-id="cfd94-190">In *Views\Instructor\Edit.cshtml*, after the `div` elements for the **Hire Date** field, add a new field for editing the office location:</span></span>

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml)]

<span data-ttu-id="cfd94-191">运行页 (选择**讲师**选项卡，然后单击**编辑**上)。</span><span class="sxs-lookup"><span data-stu-id="cfd94-191">Run the page (select the **Instructors** tab and then click **Edit** on an instructor).</span></span> <span data-ttu-id="cfd94-192">更改“办公室位置”，然后单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="cfd94-192">Change the **Office Location** and click **Save**.</span></span>

## <a name="add-courses-to-instructors-page"></a><span data-ttu-id="cfd94-193">向讲师页添加课程</span><span class="sxs-lookup"><span data-stu-id="cfd94-193">Add courses to instructors page</span></span>

<span data-ttu-id="cfd94-194">讲师可能教授任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="cfd94-194">Instructors may teach any number of courses.</span></span> <span data-ttu-id="cfd94-195">现在，您将通过添加的功能来更改课程分配使用一组复选框来增强讲师编辑页面。</span><span class="sxs-lookup"><span data-stu-id="cfd94-195">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes.</span></span>

<span data-ttu-id="cfd94-196">之间的关系`Course`和`Instructor`实体是多对多，这意味着您不具有直接访问该联接表中的外键属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-196">The relationship between the `Course` and `Instructor` entities is many-to-many, which means you do not have direct access to the foreign key properties which are in the join table.</span></span> <span data-ttu-id="cfd94-197">相反，添加和删除实体和`Instructor.Courses`导航属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-197">Instead, you add and remove entities to and from the `Instructor.Courses` navigation property.</span></span>

<span data-ttu-id="cfd94-198">用于更改讲师所对应的课程的 UI 是一组复选框。</span><span class="sxs-lookup"><span data-stu-id="cfd94-198">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="cfd94-199">该复选框中会显示数据库中的所有课程，选中讲师当前对应的课程即可。</span><span class="sxs-lookup"><span data-stu-id="cfd94-199">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="cfd94-200">用户可以通过选择或清除复选框来更改课程分配。</span><span class="sxs-lookup"><span data-stu-id="cfd94-200">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="cfd94-201">如果课程数量过大，可能想要使用不同的方法在视图中，显示数据的但会使用相同的操作才能创建或删除关系的导航属性的方法。</span><span class="sxs-lookup"><span data-stu-id="cfd94-201">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating navigation properties in order to create or delete relationships.</span></span>

<span data-ttu-id="cfd94-202">若要为复选框列表的视图提供数据，将使用视图模型类。</span><span class="sxs-lookup"><span data-stu-id="cfd94-202">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span> <span data-ttu-id="cfd94-203">创建*AssignedCourseData.cs*中*Viewmodel*文件夹并将替换为以下代码的现有代码：</span><span class="sxs-lookup"><span data-stu-id="cfd94-203">Create *AssignedCourseData.cs* in the *ViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

<span data-ttu-id="cfd94-204">在中*InstructorController.cs*，替换`HttpGet``Edit`用下面的代码的方法。</span><span class="sxs-lookup"><span data-stu-id="cfd94-204">In *InstructorController.cs*, replace the `HttpGet` `Edit` method with the following code.</span></span> <span data-ttu-id="cfd94-205">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="cfd94-205">The changes are highlighted.</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs?highlight=9,12,20-35)]

<span data-ttu-id="cfd94-206">该代码为 `Courses` 导航属性添加了预先加载，并调用新的 `PopulateAssignedCourseData` 方法使用 `AssignedCourseData` 视图模型类为复选框数组提供信息。</span><span class="sxs-lookup"><span data-stu-id="cfd94-206">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="cfd94-207">中的代码`PopulateAssignedCourseData`方法读取所有`Course`才能加载一系列课程使用视图的实体的模型。</span><span class="sxs-lookup"><span data-stu-id="cfd94-207">The code in the `PopulateAssignedCourseData` method reads through all `Course` entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="cfd94-208">对每门课程而言，该代码都会检查讲师的 `Courses` 导航属性中是否存在该课程。</span><span class="sxs-lookup"><span data-stu-id="cfd94-208">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="cfd94-209">若要创建高效的查找，检查是否分配给讲师的课程时，分配给讲师的课程将放入[HashSet](https://msdn.microsoft.com/library/bb359438.aspx)集合。</span><span class="sxs-lookup"><span data-stu-id="cfd94-209">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a [HashSet](https://msdn.microsoft.com/library/bb359438.aspx) collection.</span></span> <span data-ttu-id="cfd94-210">`Assigned`属性设置为`true`讲师的课程分配。</span><span class="sxs-lookup"><span data-stu-id="cfd94-210">The `Assigned` property is set to `true` for courses the instructor is assigned.</span></span> <span data-ttu-id="cfd94-211">视图将使用此属性来确定应将哪些复选框显示为选中状态。</span><span class="sxs-lookup"><span data-stu-id="cfd94-211">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="cfd94-212">最后，该列表传递给视图中`ViewBag`属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-212">Finally, the list is passed to the view in a `ViewBag` property.</span></span>

<span data-ttu-id="cfd94-213">接下来，添加用户单击“保存”时执行的代码。</span><span class="sxs-lookup"><span data-stu-id="cfd94-213">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="cfd94-214">替换`EditPost`方法调用更新的新方法的以下代码替换`Courses`导航属性`Instructor`实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-214">Replace the `EditPost` method with the following code, which calls a new method that updates the `Courses` navigation property of the `Instructor` entity.</span></span> <span data-ttu-id="cfd94-215">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="cfd94-215">The changes are highlighted.</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cs?highlight=3,11,25,37,40-68)]

<span data-ttu-id="cfd94-216">方法签名现在是不同于`HttpGet``Edit`方法，因此方法名称将从`EditPost`回`Edit`。</span><span class="sxs-lookup"><span data-stu-id="cfd94-216">The method signature is now different from the `HttpGet` `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="cfd94-217">由于视图不包含一系列`Course`实体，模型绑定器不能自动更新`Courses`导航属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-217">Since the view doesn't have a collection of `Course` entities, the model binder can't automatically update the `Courses` navigation property.</span></span> <span data-ttu-id="cfd94-218">而不是使用模型联编程序来更新`Courses`导航属性，则将执行该操作在新`UpdateInstructorCourses`方法。</span><span class="sxs-lookup"><span data-stu-id="cfd94-218">Instead of using the model binder to update the `Courses` navigation property, you'll do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="cfd94-219">为此，需要从模型绑定中排除 `Courses` 属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-219">Therefore you need to exclude the `Courses` property from model binding.</span></span> <span data-ttu-id="cfd94-220">这不需要对调用代码的任何更改[TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.98).aspx)因为您正在使用*允许列表*重载和`Courses`不在包括列表中。</span><span class="sxs-lookup"><span data-stu-id="cfd94-220">This doesn't require any change to the code that calls [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.98).aspx) because you're using the *whitelisting* overload and `Courses` isn't in the include list.</span></span>

<span data-ttu-id="cfd94-221">如果没有复选框已选中中的代码`UpdateInstructorCourses`初始化`Courses`具有一个空集合导航属性：</span><span class="sxs-lookup"><span data-stu-id="cfd94-221">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `Courses` navigation property with an empty collection:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

<span data-ttu-id="cfd94-222">之后，代码会循环访问数据库中的所有课程，并逐一检查当前分配给讲师的课程和视图中处于选中状态的课程。</span><span class="sxs-lookup"><span data-stu-id="cfd94-222">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="cfd94-223">为便于高效查找，后两个集合存储在 `HashSet` 对象中。</span><span class="sxs-lookup"><span data-stu-id="cfd94-223">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="cfd94-224">如果某课程的复选框处于选中状态，但该课程不在 `Instructor.Courses` 导航属性中，则会将该课程添加到导航属性中的集合中。</span><span class="sxs-lookup"><span data-stu-id="cfd94-224">If the check box for a course was selected but the course isn't in the `Instructor.Courses` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

<span data-ttu-id="cfd94-225">如果某课程的复选框未处于选中状态，但该课程存在 `Instructor.Courses` 导航属性中，则会从导航属性中删除该课程。</span><span class="sxs-lookup"><span data-stu-id="cfd94-225">If the check box for a course wasn't selected, but the course is in the `Instructor.Courses` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs)]

<span data-ttu-id="cfd94-226">在中*Views\Instructor\Edit.cshtml*，添加**课程**字段添加下面的复选框的数组的代码后立即`div`元素`OfficeAssignment`字段和之前`div`的元素**保存**按钮：</span><span class="sxs-lookup"><span data-stu-id="cfd94-226">In *Views\Instructor\Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the `OfficeAssignment` field and before the `div` element for the **Save** button:</span></span>

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cshtml)]

<span data-ttu-id="cfd94-227">粘贴后的代码中，如果其中的换行符和缩进看起来不像此处，手动修复所有问题，以便它如下所示，请参阅此处。</span><span class="sxs-lookup"><span data-stu-id="cfd94-227">After you paste the code, if line breaks and indentation don't look like they do here, manually fix everything so that it looks like what you see here.</span></span> <span data-ttu-id="cfd94-228">缩进不一定要完美，但 `@</tr><tr>`、`@:<td>`、`@:</td>` 和 `@</tr>` 行必须各成一行（如下所示），否则会出现运行时错误。</span><span class="sxs-lookup"><span data-stu-id="cfd94-228">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span>

<span data-ttu-id="cfd94-229">此代码将创建一个具有三列的 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="cfd94-229">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="cfd94-230">每个列中都有一个复选框，随后是一段由课程编号和标题组成的描述文字。</span><span class="sxs-lookup"><span data-stu-id="cfd94-230">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="cfd94-231">所有复选框均都具有相同的名称 ("selectedCourses")，通知它们是作为一个组来处理的模型联编程序。</span><span class="sxs-lookup"><span data-stu-id="cfd94-231">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they are to be treated as a group.</span></span> <span data-ttu-id="cfd94-232">`value`的每个复选框的属性设置为值`CourseID.`发布页面时，模型绑定器会将数组传递给控制器组成`CourseID`仅复选框进行选择的值。</span><span class="sxs-lookup"><span data-stu-id="cfd94-232">The `value` attribute of each check box is set to the value of `CourseID.` When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="cfd94-233">最初呈现复选框，用于分配给讲师的课程具有`checked`属性，后者选择它们 （显示处于选中状态）。</span><span class="sxs-lookup"><span data-stu-id="cfd94-233">When the check boxes are initially rendered, those that are for courses assigned to the instructor have `checked` attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="cfd94-234">更改课程分配之后, 你将想要能够验证所做的更改，当在站点恢复时`Index`页。</span><span class="sxs-lookup"><span data-stu-id="cfd94-234">After changing course assignments, you'll want to be able to verify the changes when the site returns to the `Index` page.</span></span> <span data-ttu-id="cfd94-235">因此，需要将列添加到该页面中的表。</span><span class="sxs-lookup"><span data-stu-id="cfd94-235">Therefore, you need to add a column to the table in that page.</span></span> <span data-ttu-id="cfd94-236">在这种情况下不需要使用`ViewBag`对象，因为已经在你想要显示的信息`Courses`导航属性`Instructor`您传递到页作为模型的实体。</span><span class="sxs-lookup"><span data-stu-id="cfd94-236">In this case you don't need to use the `ViewBag` object, because the information you want to display is already in the `Courses` navigation property of the `Instructor` entity that you're passing to the page as the model.</span></span>

<span data-ttu-id="cfd94-237">在中*Views\Instructor\Index.cshtml*，添加**课程**标题紧跟**Office**标题，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="cfd94-237">In *Views\Instructor\Index.cshtml*, add a **Courses** heading immediately following the **Office** heading, as shown in the following example:</span></span>

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cshtml?highlight=6)]

<span data-ttu-id="cfd94-238">然后，添加新的详细信息单元格紧跟 office 位置详细信息单元：</span><span class="sxs-lookup"><span data-stu-id="cfd94-238">Then add a new detail cell immediately following the office location detail cell:</span></span>

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml?highlight=7-14)]

<span data-ttu-id="cfd94-239">运行**讲师索引**页面，查看分配给每位讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="cfd94-239">Run the **Instructor Index** page to see the courses assigned to each instructor.</span></span>

<span data-ttu-id="cfd94-240">单击**编辑**上以查看编辑页。</span><span class="sxs-lookup"><span data-stu-id="cfd94-240">Click **Edit** on an instructor to see the Edit page.</span></span>

<span data-ttu-id="cfd94-241">更改某些课程分配，然后单击**保存**。</span><span class="sxs-lookup"><span data-stu-id="cfd94-241">Change some course assignments and click **Save**.</span></span> <span data-ttu-id="cfd94-242">所作更改将反映在索引页上。</span><span class="sxs-lookup"><span data-stu-id="cfd94-242">The changes you make are reflected on the Index page.</span></span>

 <span data-ttu-id="cfd94-243">注意:此处所使用的编辑讲师课程数据的方法适用于有限数量的课程。</span><span class="sxs-lookup"><span data-stu-id="cfd94-243">Note: The approach taken here to edit instructor course data works well when there is a limited number of courses.</span></span> <span data-ttu-id="cfd94-244">若是远大于此的集合，则需要使用不同的 UI 和不同的更新方法。</span><span class="sxs-lookup"><span data-stu-id="cfd94-244">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-deleteconfirmed"></a><span data-ttu-id="cfd94-245">更新 DeleteConfirmed</span><span class="sxs-lookup"><span data-stu-id="cfd94-245">Update DeleteConfirmed</span></span>

<span data-ttu-id="cfd94-246">在中*InstructorController.cs*，删除`DeleteConfirmed`方法并插入以下代码在其原位置。</span><span class="sxs-lookup"><span data-stu-id="cfd94-246">In *InstructorController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample24.cs?highlight=5-8,12-18)]

<span data-ttu-id="cfd94-247">此代码会进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="cfd94-247">This code makes the following change:</span></span>

- <span data-ttu-id="cfd94-248">如果以管理员身份的任何部门分配讲师，将从该部门中删除讲师分配。</span><span class="sxs-lookup"><span data-stu-id="cfd94-248">If the instructor is assigned as administrator of any department, removes the instructor assignment from that department.</span></span> <span data-ttu-id="cfd94-249">如果尝试删除作为某个部门的管理员分配一名讲师，而无需此代码中，则会得到引用完整性错误。</span><span class="sxs-lookup"><span data-stu-id="cfd94-249">Without this code, you would get a referential integrity error if you tried to delete an instructor who was assigned as administrator for a department.</span></span>

<span data-ttu-id="cfd94-250">此代码不会处理作为多个部门的管理员分配的一名讲师对应的方案。</span><span class="sxs-lookup"><span data-stu-id="cfd94-250">This code doesn't handle the scenario of one instructor assigned as administrator for multiple departments.</span></span> <span data-ttu-id="cfd94-251">在最后一个教程将添加代码，以防止发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="cfd94-251">In the last tutorial you'll add code that prevents that scenario from happening.</span></span>

## <a name="add-office-location-and-courses-to-the-create-page"></a><span data-ttu-id="cfd94-252">向创建页添加办公室位置和课程</span><span class="sxs-lookup"><span data-stu-id="cfd94-252">Add office location and courses to the Create page</span></span>

<span data-ttu-id="cfd94-253">在中*InstructorController.cs*，删除`HttpGet`并`HttpPost``Create`方法，然后在其位置添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="cfd94-253">In *InstructorController.cs*, delete the `HttpGet` and `HttpPost` `Create` methods, and then add the following code in their place:</span></span>


[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample25.cs)]

<span data-ttu-id="cfd94-254">此代码是类似于你看到的针对 Edit 方法只不过最初选择任何课程。</span><span class="sxs-lookup"><span data-stu-id="cfd94-254">This code is similar to what you saw for the Edit methods except that initially no courses are selected.</span></span> <span data-ttu-id="cfd94-255">`HttpGet` `Create`方法调用`PopulateAssignedCourseData`方法不是因为可能有课程处于选中状态但在进行排序以提供有关一个空集合`foreach`（否则查看代码将引发空引用异常在视图中的循环).</span><span class="sxs-lookup"><span data-stu-id="cfd94-255">The `HttpGet` `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="cfd94-256">创建 HttpPost 方法将每个选定的课程添加到之前的模板代码，检查的验证错误并向数据库添加新讲师的课程导航属性。</span><span class="sxs-lookup"><span data-stu-id="cfd94-256">The HttpPost Create method adds each selected course to the Courses navigation property before the template code that checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="cfd94-257">即使存在模型错误也会添加课程，以便当存在模型错误 （例如，用户键入了无效的日期），以便当页面重新显示并显示错误消息，会自动还原所做的任何课程选择。</span><span class="sxs-lookup"><span data-stu-id="cfd94-257">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date) so that when the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="cfd94-258">请注意，为了能够向 `Courses` 导航属性添加课程，必须将该属性初始化为空集合：</span><span class="sxs-lookup"><span data-stu-id="cfd94-258">Notice that in order to be able to add courses to the `Courses` navigation property you have to initialize the property as an empty collection:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample26.cs)]

<span data-ttu-id="cfd94-259">除了在控制器代码中进行此操作之外，还可以在“导师”模型中进行此操作，方法是将该属性更改为不存在集合时自动创建集合，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="cfd94-259">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample27.cs)]

<span data-ttu-id="cfd94-260">如果通过这种方式修改 `Courses` 属性，则可以删除控制器中的显式属性初始化代码。</span><span class="sxs-lookup"><span data-stu-id="cfd94-260">If you modify the `Courses` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="cfd94-261">在中*Views\Instructor\Create.cshtml*、 添加办公室位置文本框和课程的复选框，雇佣日期字段之后，并在**提交**按钮。</span><span class="sxs-lookup"><span data-stu-id="cfd94-261">In *Views\Instructor\Create.cshtml*, add an office location text box and course check boxes after the hire date field and before the **Submit** button.</span></span>

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample28.cshtml)]

<span data-ttu-id="cfd94-262">粘贴代码后，可以修复换行符和缩进，像之前的编辑页。</span><span class="sxs-lookup"><span data-stu-id="cfd94-262">After you paste the code, fix line breaks and indentation as you did earlier for the Edit page.</span></span>

<span data-ttu-id="cfd94-263">运行创建页，并添加一名讲师。</span><span class="sxs-lookup"><span data-stu-id="cfd94-263">Run the Create page and add an instructor.</span></span>

<a id="transactions"></a>

## <a name="handling-transactions"></a><span data-ttu-id="cfd94-264">处理事务</span><span class="sxs-lookup"><span data-stu-id="cfd94-264">Handling transactions</span></span>

<span data-ttu-id="cfd94-265">中所述[基本的 CRUD 功能教程](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)，默认情况下 Entity Framework 隐式实现事务。</span><span class="sxs-lookup"><span data-stu-id="cfd94-265">As explained in the [Basic CRUD Functionality tutorial](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md), by default the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="cfd94-266">有关在需要更多的控制-例如，如果你想要包括在事务中-Entity Framework 外部完成的操作的方案，请参阅[使用事务](https://msdn.microsoft.com/data/dn456843)MSDN 上。</span><span class="sxs-lookup"><span data-stu-id="cfd94-266">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Working with Transactions](https://msdn.microsoft.com/data/dn456843) on MSDN.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="cfd94-267">获取代码</span><span class="sxs-lookup"><span data-stu-id="cfd94-267">Get the code</span></span>

[<span data-ttu-id="cfd94-268">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="cfd94-268">Download the Completed Project</span></span>](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a><span data-ttu-id="cfd94-269">其他资源</span><span class="sxs-lookup"><span data-stu-id="cfd94-269">Additional resources</span></span>

<span data-ttu-id="cfd94-270">其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。</span><span class="sxs-lookup"><span data-stu-id="cfd94-270">Links to other Entity Framework resources can be found in [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

## <a name="next-step"></a><span data-ttu-id="cfd94-271">下一步</span><span class="sxs-lookup"><span data-stu-id="cfd94-271">Next step</span></span>

<span data-ttu-id="cfd94-272">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="cfd94-272">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cfd94-273">自定义的课程页</span><span class="sxs-lookup"><span data-stu-id="cfd94-273">Customized courses pages</span></span>
> * <span data-ttu-id="cfd94-274">添加了的 office 到讲师页</span><span class="sxs-lookup"><span data-stu-id="cfd94-274">Added office to instructors page</span></span>
> * <span data-ttu-id="cfd94-275">向讲师页添加的课程</span><span class="sxs-lookup"><span data-stu-id="cfd94-275">Added courses to instructors page</span></span>
> * <span data-ttu-id="cfd94-276">更新的 DeleteConfirmed</span><span class="sxs-lookup"><span data-stu-id="cfd94-276">Updated DeleteConfirmed</span></span>
> * <span data-ttu-id="cfd94-277">添加的办公室位置和课程向创建页</span><span class="sxs-lookup"><span data-stu-id="cfd94-277">Added office location and courses to the Create page</span></span>

<span data-ttu-id="cfd94-278">转到下一步的文章，了解如何实现异步编程模型。</span><span class="sxs-lookup"><span data-stu-id="cfd94-278">Advance to the next article to learn how to implement an asynchronous programming model.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="cfd94-279">异步编程模型</span><span class="sxs-lookup"><span data-stu-id="cfd94-279">Asynchronous programming model</span></span>](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)
