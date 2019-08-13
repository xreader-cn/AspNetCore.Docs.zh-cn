---
title: ASP.NET Core 中的 Razor 页面和 EF Core - 更新相关数据 - 第 7 个教程（共 8 个）
author: rick-anderson
description: 本教程将通过更新外键字段和导航属性来更新相关数据。
ms.author: riande
ms.date: 11/15/2017
uid: data/ef-rp/update-related-data
ms.openlocfilehash: b996e7f9c8e422060f6378a6004a53906e2af7c7
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087087"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="a9e70-103">ASP.NET Core 中的 Razor 页面和 EF Core - 更新相关数据 - 第 7 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="a9e70-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="a9e70-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9e70-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="a9e70-105">本教程演示如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="a9e70-105">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="a9e70-106">如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="a9e70-106">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="a9e70-107">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="a9e70-107">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="a9e70-108">下图显示了部分已完成页面。</span><span class="sxs-lookup"><span data-stu-id="a9e70-108">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="a9e70-109">![课程“编辑”页](update-related-data/_static/course-edit.png)
![讲师”编辑”页](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="a9e70-109">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="a9e70-110">查看并测试“创建”和“编辑”课程页。</span><span class="sxs-lookup"><span data-stu-id="a9e70-110">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="a9e70-111">创建新课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-111">Create a new course.</span></span> <span data-ttu-id="a9e70-112">系按其主键（一个整数）进行选择，而不是按名称。</span><span class="sxs-lookup"><span data-stu-id="a9e70-112">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="a9e70-113">编辑新课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-113">Edit the new course.</span></span> <span data-ttu-id="a9e70-114">完成测试后，请删除新课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-114">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="a9e70-115">创建基类以共享通用代码</span><span class="sxs-lookup"><span data-stu-id="a9e70-115">Create a base class to share common code</span></span>

<span data-ttu-id="a9e70-116">“课程/创建”和“课程/编辑”页分别需要一个系名称列表。</span><span class="sxs-lookup"><span data-stu-id="a9e70-116">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="a9e70-117">针对“创建”和“编辑”页创建 Pages/Courses/DepartmentNamePageModel.cshtml.cs 基类  ：</span><span class="sxs-lookup"><span data-stu-id="a9e70-117">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="a9e70-118">上面的代码创建 [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) 以包含系名称列表。</span><span class="sxs-lookup"><span data-stu-id="a9e70-118">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="a9e70-119">如果指定了 `selectedDepartment`，可在 `SelectList` 中选择该系。</span><span class="sxs-lookup"><span data-stu-id="a9e70-119">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="a9e70-120">“创建”和“编辑”页模型类将派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="a9e70-120">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="a9e70-121">自定义课程页</span><span class="sxs-lookup"><span data-stu-id="a9e70-121">Customize the Courses Pages</span></span>

<span data-ttu-id="a9e70-122">创建新的课程实体时，新实体必须与现有院系有关系。</span><span class="sxs-lookup"><span data-stu-id="a9e70-122">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="a9e70-123">若要在创建课程的同时添加系，则“创建”和“编辑”的基类必须包含用于选择系的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="a9e70-123">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="a9e70-124">该下拉列表设置 `Course.DepartmentID` 外键 (FK) 属性。</span><span class="sxs-lookup"><span data-stu-id="a9e70-124">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="a9e70-125">EF Core 使用 `Course.DepartmentID` FK 加载 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="a9e70-125">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![创建课程](update-related-data/_static/ddl.png)

<span data-ttu-id="a9e70-127">使用以下代码更新“创建”页模型：</span><span class="sxs-lookup"><span data-stu-id="a9e70-127">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="a9e70-128">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="a9e70-128">The preceding code:</span></span>

* <span data-ttu-id="a9e70-129">派生自 `DepartmentNamePageModel`。</span><span class="sxs-lookup"><span data-stu-id="a9e70-129">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="a9e70-130">使用 `TryUpdateModelAsync` 防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="a9e70-130">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="a9e70-131">将 `ViewData["DepartmentID"]` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="a9e70-131">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="a9e70-132">`ViewData["DepartmentID"]` 将替换为强类型 `DepartmentNameSL`。</span><span class="sxs-lookup"><span data-stu-id="a9e70-132">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="a9e70-133">建议使用强类型而非弱类型。</span><span class="sxs-lookup"><span data-stu-id="a9e70-133">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="a9e70-134">有关详细信息，请参阅[弱类型数据（ViewData 和 ViewBag）](xref:mvc/views/overview#VD_VB)。</span><span class="sxs-lookup"><span data-stu-id="a9e70-134">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="a9e70-135">更新“课程创建”页</span><span class="sxs-lookup"><span data-stu-id="a9e70-135">Update the Courses Create page</span></span>

<span data-ttu-id="a9e70-136">使用以下标记更新 Pages/Courses/Create.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="a9e70-136">Update *Pages/Courses/Create.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="a9e70-137">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="a9e70-137">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="a9e70-138">将标题从“DepartmentID”更改为“Department”   。</span><span class="sxs-lookup"><span data-stu-id="a9e70-138">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="a9e70-139">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="a9e70-139">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="a9e70-140">添加“选择系”选项。</span><span class="sxs-lookup"><span data-stu-id="a9e70-140">Adds the "Select Department" option.</span></span> <span data-ttu-id="a9e70-141">此更改将导致呈现“选择系”而不是第一个系。</span><span class="sxs-lookup"><span data-stu-id="a9e70-141">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="a9e70-142">在未选择系时添加验证消息。</span><span class="sxs-lookup"><span data-stu-id="a9e70-142">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="a9e70-143">Razor 页面使用[选择标记帮助器](xref:mvc/views/working-with-forms#the-select-tag-helper)：</span><span class="sxs-lookup"><span data-stu-id="a9e70-143">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="a9e70-144">测试“创建”页。</span><span class="sxs-lookup"><span data-stu-id="a9e70-144">Test the Create page.</span></span> <span data-ttu-id="a9e70-145">“创建”页显示系名称，而不是系 ID。</span><span class="sxs-lookup"><span data-stu-id="a9e70-145">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="a9e70-146">更新“课程编辑”页。</span><span class="sxs-lookup"><span data-stu-id="a9e70-146">Update the Courses Edit page.</span></span>

<span data-ttu-id="a9e70-147">使用以下代码更新“编辑”页模型：</span><span class="sxs-lookup"><span data-stu-id="a9e70-147">Update the edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="a9e70-148">这些更改与在“创建”页模型中所做的更改相似。</span><span class="sxs-lookup"><span data-stu-id="a9e70-148">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="a9e70-149">在上面的代码中，`PopulateDepartmentsDropDownList` 在系 ID 中传递并将选择下拉列表中指定的系。</span><span class="sxs-lookup"><span data-stu-id="a9e70-149">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="a9e70-150">使用以下标记更新 Pages/Courses/Edit.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="a9e70-150">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="a9e70-151">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="a9e70-151">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="a9e70-152">显示课程 ID。</span><span class="sxs-lookup"><span data-stu-id="a9e70-152">Displays the course ID.</span></span> <span data-ttu-id="a9e70-153">通常不显示实体的主键 (PK)。</span><span class="sxs-lookup"><span data-stu-id="a9e70-153">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="a9e70-154">PK 对用户不具有任何意义。</span><span class="sxs-lookup"><span data-stu-id="a9e70-154">PKs are usually meaningless to users.</span></span> <span data-ttu-id="a9e70-155">在这种情况下，PK 就是课程编号。</span><span class="sxs-lookup"><span data-stu-id="a9e70-155">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="a9e70-156">将标题从“DepartmentID”更改为“Department”   。</span><span class="sxs-lookup"><span data-stu-id="a9e70-156">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="a9e70-157">将 `"ViewBag.DepartmentID"` 替换为 `DepartmentNameSL`（来自基类）。</span><span class="sxs-lookup"><span data-stu-id="a9e70-157">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="a9e70-158">该页面包含课程编号的隐藏域 (`<input type="hidden">`)。</span><span class="sxs-lookup"><span data-stu-id="a9e70-158">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="a9e70-159">添加具有 `asp-for="Course.CourseID"` 的 `<label>` 标记帮助器也同样需要隐藏域。</span><span class="sxs-lookup"><span data-stu-id="a9e70-159">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="a9e70-160">用户单击“保存”时，需要 `<input type="hidden">`  ，以便在已发布的数据中包括课程编号。</span><span class="sxs-lookup"><span data-stu-id="a9e70-160">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="a9e70-161">测试更新的代码。</span><span class="sxs-lookup"><span data-stu-id="a9e70-161">Test the updated code.</span></span> <span data-ttu-id="a9e70-162">创建、编辑和删除课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-162">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="a9e70-163">将 AsNoTracking 添加到“详细信息”和“删除”页模型</span><span class="sxs-lookup"><span data-stu-id="a9e70-163">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="a9e70-164">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 可以在不需要跟踪时提高性能。</span><span class="sxs-lookup"><span data-stu-id="a9e70-164">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="a9e70-165">将 `AsNoTracking` 添加到“删除”和“详细信息”页模型。</span><span class="sxs-lookup"><span data-stu-id="a9e70-165">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="a9e70-166">下面的代码显示更新的“删除”页模型：</span><span class="sxs-lookup"><span data-stu-id="a9e70-166">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="a9e70-167">在 Pages/Courses/Details.cshtml.cs 文件中更新 `OnGetAsync` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="a9e70-167">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="a9e70-168">修改“删除”和“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="a9e70-168">Modify the Delete and Details pages</span></span>

<span data-ttu-id="a9e70-169">使用以下标记更新“删除”Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="a9e70-169">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="a9e70-170">对“详细信息”页执行相同更改。</span><span class="sxs-lookup"><span data-stu-id="a9e70-170">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="a9e70-171">测试“课程”页</span><span class="sxs-lookup"><span data-stu-id="a9e70-171">Test the Course pages</span></span>

<span data-ttu-id="a9e70-172">测试“创建”、“编辑”、“详细信息”和“删除”。</span><span class="sxs-lookup"><span data-stu-id="a9e70-172">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="a9e70-173">更新“讲师”页</span><span class="sxs-lookup"><span data-stu-id="a9e70-173">Update the instructor pages</span></span>

<span data-ttu-id="a9e70-174">以下各部分介绍更新“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="a9e70-174">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="a9e70-175">添加办公室位置</span><span class="sxs-lookup"><span data-stu-id="a9e70-175">Add office location</span></span>

<span data-ttu-id="a9e70-176">编辑讲师记录时，可能希望更新讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="a9e70-176">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="a9e70-177">`Instructor` 实体与 `OfficeAssignment` 实体是一对零或一关系。</span><span class="sxs-lookup"><span data-stu-id="a9e70-177">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="a9e70-178">讲师代码必须处理：</span><span class="sxs-lookup"><span data-stu-id="a9e70-178">The instructor code must handle:</span></span>

* <span data-ttu-id="a9e70-179">如果用户清除办公室分配，则需删除 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="a9e70-179">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="a9e70-180">如果用户输入办公室分配，且办公室分配原本为空，则需创建一个新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="a9e70-180">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="a9e70-181">如果用户更改办公室分配，则需更新 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="a9e70-181">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="a9e70-182">使用以下代码更新讲师“编辑”页模型：</span><span class="sxs-lookup"><span data-stu-id="a9e70-182">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="a9e70-183">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="a9e70-183">The preceding code:</span></span>

* <span data-ttu-id="a9e70-184">使用 `OfficeAssignment` 导航属性的预先加载从数据库获取当前的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="a9e70-184">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="a9e70-185">用模型绑定器中的值更新检索到的 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="a9e70-185">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="a9e70-186">`TryUpdateModel` 可防止[过多发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="a9e70-186">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="a9e70-187">如果办公室位置为空，则将 `Instructor.OfficeAssignment` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="a9e70-187">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="a9e70-188">当 `Instructor.OfficeAssignment` 为 null 时，`OfficeAssignment` 表中的相关行将会删除。</span><span class="sxs-lookup"><span data-stu-id="a9e70-188">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="a9e70-189">更新讲师“编辑”页</span><span class="sxs-lookup"><span data-stu-id="a9e70-189">Update the instructor Edit page</span></span>

<span data-ttu-id="a9e70-190">使用办公室位置更新 Pages/Instructors/Edit.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="a9e70-190">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="a9e70-191">验证是否可以更改讲师办公室位置。</span><span class="sxs-lookup"><span data-stu-id="a9e70-191">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="a9e70-192">向“讲师编辑”页添加课程分配</span><span class="sxs-lookup"><span data-stu-id="a9e70-192">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="a9e70-193">讲师可能教授任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-193">Instructors may teach any number of courses.</span></span> <span data-ttu-id="a9e70-194">本部分将添加更改课程分配的功能。</span><span class="sxs-lookup"><span data-stu-id="a9e70-194">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="a9e70-195">下图显示更新的讲师“编辑”页：</span><span class="sxs-lookup"><span data-stu-id="a9e70-195">The following image shows the updated instructor Edit page:</span></span>

![带课程信息的讲师“编辑”页](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="a9e70-197">`Course` 和 `Instructor` 具有多对多关系。</span><span class="sxs-lookup"><span data-stu-id="a9e70-197">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="a9e70-198">若要添加和删除关系，可以从 `CourseAssignments` 联接实体集中添加和删除实体。</span><span class="sxs-lookup"><span data-stu-id="a9e70-198">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="a9e70-199">通过复选框可对分配给讲师的课程进行更改。</span><span class="sxs-lookup"><span data-stu-id="a9e70-199">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="a9e70-200">数据库中的每一门课程均有对应显示的复选框。</span><span class="sxs-lookup"><span data-stu-id="a9e70-200">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="a9e70-201">已分配给讲师的课程将会被勾选。</span><span class="sxs-lookup"><span data-stu-id="a9e70-201">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="a9e70-202">用户可以通过选择或清除复选框来更改课程分配。</span><span class="sxs-lookup"><span data-stu-id="a9e70-202">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="a9e70-203">如果课程数量过多：</span><span class="sxs-lookup"><span data-stu-id="a9e70-203">If the number of courses were much greater:</span></span>

* <span data-ttu-id="a9e70-204">可以使用其他用户界面显示课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-204">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="a9e70-205">使用联接实体创建或删除关系的方法不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="a9e70-205">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="a9e70-206">添加类以支持“创建”和“编辑”讲师页</span><span class="sxs-lookup"><span data-stu-id="a9e70-206">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="a9e70-207">使用以下代码创建 SchoolViewModels/AssignedCourseData.cs  ：</span><span class="sxs-lookup"><span data-stu-id="a9e70-207">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="a9e70-208">`AssignedCourseData` 类包含的数据可用于为讲师已分配的课程创建复选框。</span><span class="sxs-lookup"><span data-stu-id="a9e70-208">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="a9e70-209">创建 Pages/Instructors/InstructorCoursesPageModel.cshtml.cs 基类  ：</span><span class="sxs-lookup"><span data-stu-id="a9e70-209">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="a9e70-210">`InstructorCoursesPageModel` 是将用于“编辑”和“创建”页模型的基类。</span><span class="sxs-lookup"><span data-stu-id="a9e70-210">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="a9e70-211">`PopulateAssignedCourseData` 读取所有 `Course` 实体以填充 `AssignedCourseDataList`。</span><span class="sxs-lookup"><span data-stu-id="a9e70-211">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="a9e70-212">该代码将设置每门课程的 `CourseID` 和标题，并决定是否为讲师分配该课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-212">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="a9e70-213">[HashSet](/dotnet/api/system.collections.generic.hashset-1) 用于创建高效查找。</span><span class="sxs-lookup"><span data-stu-id="a9e70-213">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="a9e70-214">讲师“编辑”页模型</span><span class="sxs-lookup"><span data-stu-id="a9e70-214">Instructors Edit page model</span></span>

<span data-ttu-id="a9e70-215">使用以下代码更新讲师“编辑”页模型：</span><span class="sxs-lookup"><span data-stu-id="a9e70-215">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="a9e70-216">上面的代码处理办公室分配更改。</span><span class="sxs-lookup"><span data-stu-id="a9e70-216">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="a9e70-217">更新“讲师”Razor 视图：</span><span class="sxs-lookup"><span data-stu-id="a9e70-217">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="a9e70-218">将代码粘贴到 Visual Studio 中时，换行符会发生更改，其更改方式会导致代码中断。</span><span class="sxs-lookup"><span data-stu-id="a9e70-218">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="a9e70-219">按 Ctrl+Z 一次可撤消自动格式设置。</span><span class="sxs-lookup"><span data-stu-id="a9e70-219">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="a9e70-220">按 Ctrl+Z 可以修复换行符，使其看起来如此处所示。</span><span class="sxs-lookup"><span data-stu-id="a9e70-220">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="a9e70-221">缩进不一定要呈现完美形式，但 `@</tr><tr>`、`@:<td>`、`@:</td>` 和 `@:</tr>` 行必须各成一行（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="a9e70-221">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="a9e70-222">选中新的代码块后，按 Tab 三次，使新代码与现有代码对齐。</span><span class="sxs-lookup"><span data-stu-id="a9e70-222">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="a9e70-223">[通过此链接](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html)投票或查看此 bug 的状态。</span><span class="sxs-lookup"><span data-stu-id="a9e70-223">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="a9e70-224">上面的代码将创建一个具有三列的 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="a9e70-224">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="a9e70-225">每列均具有一个复选框和包含课程编号及标题的标题。</span><span class="sxs-lookup"><span data-stu-id="a9e70-225">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="a9e70-226">所有复选框均具有相同的名称（“selectedCourses”）。</span><span class="sxs-lookup"><span data-stu-id="a9e70-226">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="a9e70-227">使用相同名称可指示模型绑定器将它们视为一个组。</span><span class="sxs-lookup"><span data-stu-id="a9e70-227">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="a9e70-228">每个复选框的值特性都设置为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="a9e70-228">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="a9e70-229">发布页面时，模型绑定器会传递一个数组，该数组只包括所选复选框的 `CourseID` 值。</span><span class="sxs-lookup"><span data-stu-id="a9e70-229">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="a9e70-230">初次呈现复选框时，分配给讲师的课程具有已勾选的特性。</span><span class="sxs-lookup"><span data-stu-id="a9e70-230">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="a9e70-231">运行应用并测试更新的讲师“编辑”页。</span><span class="sxs-lookup"><span data-stu-id="a9e70-231">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="a9e70-232">更改某些课程分配。</span><span class="sxs-lookup"><span data-stu-id="a9e70-232">Change some course assignments.</span></span> <span data-ttu-id="a9e70-233">这些更改将反映在“索引”页上。</span><span class="sxs-lookup"><span data-stu-id="a9e70-233">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="a9e70-234">注意:此处所使用的编辑讲师课程数据的方法适用于数量有限的课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-234">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="a9e70-235">对于规模远大于此的集合，则使用不同的 UI 和不同的更新方法会更实用和更高效。</span><span class="sxs-lookup"><span data-stu-id="a9e70-235">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="a9e70-236">更新讲师“创建”页</span><span class="sxs-lookup"><span data-stu-id="a9e70-236">Update the instructors Create page</span></span>

<span data-ttu-id="a9e70-237">使用以下代码更新讲师“创建”页模型：</span><span class="sxs-lookup"><span data-stu-id="a9e70-237">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="a9e70-238">前面的代码与 Pages/Instructors/Edit.cshtml.cs 代码类似  。</span><span class="sxs-lookup"><span data-stu-id="a9e70-238">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="a9e70-239">使用以下标记更新讲师“创建”Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="a9e70-239">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="a9e70-240">测试讲师“创建”页。</span><span class="sxs-lookup"><span data-stu-id="a9e70-240">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="a9e70-241">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="a9e70-241">Update the Delete page</span></span>

<span data-ttu-id="a9e70-242">使用以下代码更新“删除”页模型：</span><span class="sxs-lookup"><span data-stu-id="a9e70-242">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="a9e70-243">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="a9e70-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="a9e70-244">对 `CourseAssignments` 导航属性使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="a9e70-244">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="a9e70-245">必须包含 `CourseAssignments`，否则删除讲师时将不会删除课程。</span><span class="sxs-lookup"><span data-stu-id="a9e70-245">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="a9e70-246">为避免阅读它们，可以在数据库中配置级联删除。</span><span class="sxs-lookup"><span data-stu-id="a9e70-246">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="a9e70-247">如果要删除的讲师被指派为任何系的管理员，则需从这些系中删除该讲师分配。</span><span class="sxs-lookup"><span data-stu-id="a9e70-247">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9e70-248">其他资源</span><span class="sxs-lookup"><span data-stu-id="a9e70-248">Additional resources</span></span>

* [<span data-ttu-id="a9e70-249">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="a9e70-249">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="a9e70-250">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="a9e70-250">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="a9e70-251">[上一页](xref:data/ef-rp/read-related-data)
> [下一页](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="a9e70-251">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>
