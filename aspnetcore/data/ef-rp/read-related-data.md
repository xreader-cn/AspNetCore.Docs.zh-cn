---
title: 第 6 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 读取相关数据
author: rick-anderson
description: Razor 页面和实体框架教程系列第 6 部分。
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 5ffb57f21c89e21fcbb14b933cb0d0cb29d9c71b
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240943"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="6a169-103">第 6 部分，ASP.NET Core 中的 Razor 页面和 EF Core - 读取相关数据</span><span class="sxs-lookup"><span data-stu-id="6a169-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="6a169-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6a169-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6a169-105">本教程介绍如何读取和显示相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="6a169-106">相关数据为 EF Core 加载到导航属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="6a169-107">下图显示了本教程中已完成的页面：</span><span class="sxs-lookup"><span data-stu-id="6a169-107">The following illustrations show the completed pages for this tutorial:</span></span>

![“课程索引”页](read-related-data/_static/courses-index30.png)

![“讲师索引”页](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="6a169-110">预先加载、显式加载和延迟加载</span><span class="sxs-lookup"><span data-stu-id="6a169-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="6a169-111">EF Core 可采用多种方式将相关数据加载到实体的导航属性中：</span><span class="sxs-lookup"><span data-stu-id="6a169-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="6a169-112">[预先加载](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="6a169-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="6a169-113">预先加载是指对查询某类型的实体时一并加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="6a169-114">读取实体时，会检索其相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="6a169-115">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="6a169-116">EF Core 将针对预先加载的某些类型发出多个查询。</span><span class="sxs-lookup"><span data-stu-id="6a169-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="6a169-117">发布多个查询可能比发布大型的单个查询更为有效。</span><span class="sxs-lookup"><span data-stu-id="6a169-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="6a169-118">预先加载通过 `Include` 和 `ThenInclude` 方法进行指定。</span><span class="sxs-lookup"><span data-stu-id="6a169-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="6a169-120">当包含集合导航时，预先加载会发送多个查询：</span><span class="sxs-lookup"><span data-stu-id="6a169-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="6a169-121">一个查询用于主查询</span><span class="sxs-lookup"><span data-stu-id="6a169-121">One query for the main query</span></span> 
  * <span data-ttu-id="6a169-122">一个查询用于加载树中每个集合“边缘”。</span><span class="sxs-lookup"><span data-stu-id="6a169-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="6a169-123">使用 `Load` 的单独查询：可在单独的查询中检索数据，EF Core 会“修复”导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="6a169-124">“修复”是指 EF Core 自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="6a169-125">使用 `Load` 单独查询比预先加载更像是显式加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![单独查询示例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="6a169-127">**注意：** EF Core 会将导航属性自动“修复”为之前加载到上下文实例中的任何其他实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="6a169-128">即使导航属性的数据非显式包含在内，但如果先前加载了部分或所有相关实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="6a169-129">[显式加载](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="6a169-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="6a169-130">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="6a169-131">必须编写代码才能在需要时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="6a169-132">使用单独查询进行显式加载时，会向数据库发送多个查询。</span><span class="sxs-lookup"><span data-stu-id="6a169-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="6a169-133">该代码通过显式加载指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="6a169-134">使用 `Load` 方法进行显式加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="6a169-135">例如：</span><span class="sxs-lookup"><span data-stu-id="6a169-135">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="6a169-137">[延迟加载](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="6a169-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="6a169-138">[延迟加载已添加到版本 2.1 中的 EF Core](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="6a169-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="6a169-139">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="6a169-140">首次访问导航属性时，会自动检索该导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="6a169-141">首次访问导航属性时，都会向数据库发送一个查询。</span><span class="sxs-lookup"><span data-stu-id="6a169-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="6a169-142">创建“课程”页</span><span class="sxs-lookup"><span data-stu-id="6a169-142">Create Course pages</span></span>

<span data-ttu-id="6a169-143">`Course` 实体包括一个带相关 `Department` 实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="6a169-145">若要显示课程的已分配院系的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6a169-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="6a169-146">将相关的 `Department` 实体加载到 `Course.Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="6a169-147">获取 `Department` 实体的 `Name` 属性中的名称。</span><span class="sxs-lookup"><span data-stu-id="6a169-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="6a169-148">搭建“课程”页的基架</span><span class="sxs-lookup"><span data-stu-id="6a169-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a169-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a169-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a169-150">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="6a169-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="6a169-151">创建“Pages/Courses”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a169-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="6a169-152">将 `Course` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="6a169-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="6a169-153">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="6a169-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a169-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a169-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6a169-155">创建“Pages/Courses”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a169-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="6a169-156">运行以下命令，搭建“课程”页的基架。</span><span class="sxs-lookup"><span data-stu-id="6a169-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="6a169-157">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="6a169-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="6a169-158">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="6a169-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="6a169-159">打开 Pages/Courses/Index.cshtml.cs 并检查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="6a169-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="6a169-160">基架引擎为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="6a169-161">`Include` 方法指定预先加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="6a169-162">运行应用并选择“课程”链接。</span><span class="sxs-lookup"><span data-stu-id="6a169-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="6a169-163">院系列显示 `DepartmentID`（该项无用）。</span><span class="sxs-lookup"><span data-stu-id="6a169-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="6a169-164">显示院系名称</span><span class="sxs-lookup"><span data-stu-id="6a169-164">Display the department name</span></span>

<span data-ttu-id="6a169-165">使用以下代码更新 Pages/Courses/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="6a169-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="6a169-166">上述代码将 `Course` 属性更改为 `Courses`，然后添加 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="6a169-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="6a169-167">由于未跟踪返回的实体，因此 `AsNoTracking` 提升了性能。</span><span class="sxs-lookup"><span data-stu-id="6a169-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="6a169-168">无需跟踪实体，因为未在当前的上下文中更新这些实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="6a169-169">使用以下代码更新 Pages/Courses/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="6a169-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="6a169-170">对基架代码进行了以下更改：</span><span class="sxs-lookup"><span data-stu-id="6a169-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="6a169-171">将 `Course` 属性名称更改为了 `Courses`。</span><span class="sxs-lookup"><span data-stu-id="6a169-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="6a169-172">添加了显示 `CourseID` 属性值的“数字”列。</span><span class="sxs-lookup"><span data-stu-id="6a169-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="6a169-173">默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。</span><span class="sxs-lookup"><span data-stu-id="6a169-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="6a169-174">但在此情况下主键是有意义的。</span><span class="sxs-lookup"><span data-stu-id="6a169-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="6a169-175">更改“院系”列，显示院系名称。</span><span class="sxs-lookup"><span data-stu-id="6a169-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="6a169-176">该代码显示已加载到 `Department` 导航属性中的 `Department` 实体的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="6a169-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="6a169-177">运行应用并选择“课程”选项卡，查看包含系名称的列表。</span><span class="sxs-lookup"><span data-stu-id="6a169-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![“课程索引”页](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="6a169-179">使用 Select 加载相关数据</span><span class="sxs-lookup"><span data-stu-id="6a169-179">Loading related data with Select</span></span>

<span data-ttu-id="6a169-180">`OnGetAsync` 方法使用 `Include` 方法加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="6a169-181">`Select` 方法是只加载所需相关数据的替代方法。</span><span class="sxs-lookup"><span data-stu-id="6a169-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="6a169-182">对于单个项（如 `Department.Name`），它使用 SQL INNER JOIN。</span><span class="sxs-lookup"><span data-stu-id="6a169-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="6a169-183">对于集合，它使用另一个数据库访问，但集合上的 `Include` 运算符也是如此。</span><span class="sxs-lookup"><span data-stu-id="6a169-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="6a169-184">以下代码使用 `Select` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="6a169-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="6a169-185">上述代码不会返回任何实体类型，因此不进行任何跟踪。</span><span class="sxs-lookup"><span data-stu-id="6a169-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="6a169-186">有关 EF 跟踪的详细信息，请参阅 [跟踪查询与非跟踪查询](/ef/core/querying/tracking)。</span><span class="sxs-lookup"><span data-stu-id="6a169-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="6a169-187">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="6a169-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="6a169-188">有关完整示例的信息，请参阅 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)。</span><span class="sxs-lookup"><span data-stu-id="6a169-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="6a169-189">创建“讲师”页</span><span class="sxs-lookup"><span data-stu-id="6a169-189">Create Instructor pages</span></span>

<span data-ttu-id="6a169-190">本节搭建“讲师”页的基架，并向讲师“索引”页添加相关“课程”和“注册”。</span><span class="sxs-lookup"><span data-stu-id="6a169-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="6a169-191">![“讲师索引”页](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="6a169-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="6a169-192">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="6a169-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="6a169-193">讲师列表显示 `OfficeAssignment` 实体（上图中的办公室）的相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="6a169-194">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="6a169-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="6a169-195">预先加载适用于 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="6a169-196">需要显示相关数据时，预先加载通常更高效。</span><span class="sxs-lookup"><span data-stu-id="6a169-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="6a169-197">在此情况下，会显示讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="6a169-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="6a169-198">用户选择一名讲师时，显示相关 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="6a169-199">`Instructor` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="6a169-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="6a169-200">对 `Course` 实体及其相关的 `Department` 实体使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="6a169-201">这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="6a169-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="6a169-202">此示例演示如何在位于导航实体内的实体中预先加载这些导航实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="6a169-203">用户选择一门课程时，会显示 `Enrollments` 实体的相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="6a169-204">上图中显示了学生姓名和成绩。</span><span class="sxs-lookup"><span data-stu-id="6a169-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="6a169-205">`Course` 和 `Enrollment` 实体之间存在一对多的关系。</span><span class="sxs-lookup"><span data-stu-id="6a169-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="6a169-206">创建视图模型</span><span class="sxs-lookup"><span data-stu-id="6a169-206">Create a view model</span></span>

<span data-ttu-id="6a169-207">“讲师”页显示来自三个不同表格的数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="6a169-208">需要一个视图模型，该模型中包含表示三个表格的三个属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="6a169-209">使用以下代码创建 SchoolViewModels/InstructorIndexData.cs：</span><span class="sxs-lookup"><span data-stu-id="6a169-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="6a169-210">搭建“讲师”页的基架</span><span class="sxs-lookup"><span data-stu-id="6a169-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a169-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a169-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a169-212">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="6a169-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="6a169-213">创建“Pages/Instructors”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a169-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="6a169-214">将 `Instructor` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="6a169-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="6a169-215">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="6a169-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a169-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a169-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6a169-217">创建“Pages/Instructors”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6a169-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="6a169-218">运行以下命令，搭建“讲师”页的基架。</span><span class="sxs-lookup"><span data-stu-id="6a169-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="6a169-219">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="6a169-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="6a169-220">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="6a169-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="6a169-221">若要在更新之前查看已搭建基架的页面的外观，则运行应用并导航到“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="6a169-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="6a169-222">使用以下代码更新 Pages/Instructors/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="6a169-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="6a169-223">`OnGetAsync` 方法接受所选讲师 ID 的可选路由数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="6a169-224">检查 Pages/Instructors/Index.cshtml.cs 文件中的查询：</span><span class="sxs-lookup"><span data-stu-id="6a169-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="6a169-225">代码指定以下导航属性的预先加载：</span><span class="sxs-lookup"><span data-stu-id="6a169-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="6a169-226">注意 `CourseAssignments` 和 `Course` 对 `Include` 和 `ThenInclude` 方法的重复使用。</span><span class="sxs-lookup"><span data-stu-id="6a169-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="6a169-227">若要指定 `Course` 实体的两个导航属性的预先加载，则这种重复使用是必要的。</span><span class="sxs-lookup"><span data-stu-id="6a169-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="6a169-228">选择讲师时 (`id != null`)，将执行以下代码。</span><span class="sxs-lookup"><span data-stu-id="6a169-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="6a169-229">从视图模型中的讲师列表检索所选讲师。</span><span class="sxs-lookup"><span data-stu-id="6a169-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="6a169-230">向视图模型的 `Courses` 属性加载来自讲师 `CourseAssignments` 导航属性的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="6a169-231">`Where` 方法返回一个集合。</span><span class="sxs-lookup"><span data-stu-id="6a169-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="6a169-232">但在这种情况下，筛选器将选择单个实体，因此会调用 `Single` 方法将集合转换为单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="6a169-233">`Instructor` 实体提供对 `CourseAssignments` 属性的访问。</span><span class="sxs-lookup"><span data-stu-id="6a169-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="6a169-234">`CourseAssignments` 提供对相关 `Course` 实体的访问。</span><span class="sxs-lookup"><span data-stu-id="6a169-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="6a169-236">当集合仅包含一个项时，集合使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="6a169-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="6a169-237">如果集合为空或包含多个项，`Single` 方法会引发异常。</span><span class="sxs-lookup"><span data-stu-id="6a169-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="6a169-238">还可使用 `SingleOrDefault`，该方式在集合为空时返回默认值（本例中为 null）。</span><span class="sxs-lookup"><span data-stu-id="6a169-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="6a169-239">选中课程时，视图模型的 `Enrollments` 属性将填充以下代码：</span><span class="sxs-lookup"><span data-stu-id="6a169-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="6a169-240">更新“讲师索引”页</span><span class="sxs-lookup"><span data-stu-id="6a169-240">Update the instructors Index page</span></span>

<span data-ttu-id="6a169-241">使用以下代码更新 Pages/Instructors/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="6a169-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="6a169-242">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="6a169-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="6a169-243">将 `page` 指令从 `@page` 更新为 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="6a169-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="6a169-244">`"{id:int?}"` 是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="6a169-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="6a169-245">路由模板将 URL 中的整数查询字符串更改为路由数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="6a169-246">例如，单击仅具有 `@page` 指令的讲师的“选择”链接将生成如下 URL：</span><span class="sxs-lookup"><span data-stu-id="6a169-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="6a169-247">如果页面指令为 `@page "{id:int?}"` 时，则 URL 为：</span><span class="sxs-lookup"><span data-stu-id="6a169-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="6a169-248">添加仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列。</span><span class="sxs-lookup"><span data-stu-id="6a169-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="6a169-249">由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="6a169-250">添加显示每位讲师所授课程的“课程”列。</span><span class="sxs-lookup"><span data-stu-id="6a169-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="6a169-251">有关此 razor 语法的详细信息，请参阅[显式行转换](xref:mvc/views/razor#explicit-line-transition)。</span><span class="sxs-lookup"><span data-stu-id="6a169-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="6a169-252">添加向所选讲师和课程的 `tr` 元素中动态添加 `class="success"` 的代码。</span><span class="sxs-lookup"><span data-stu-id="6a169-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="6a169-253">此时会使用 Bootstrap 类为所选行设置背景色。</span><span class="sxs-lookup"><span data-stu-id="6a169-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="6a169-254">添加标记为“选择”的新的超链接。</span><span class="sxs-lookup"><span data-stu-id="6a169-254">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="6a169-255">该链接将所选讲师的 ID 发送给 `Index` 方法并设置背景色。</span><span class="sxs-lookup"><span data-stu-id="6a169-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="6a169-256">添加所选讲师的课程表。</span><span class="sxs-lookup"><span data-stu-id="6a169-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="6a169-257">添加所选课程的学生注册表。</span><span class="sxs-lookup"><span data-stu-id="6a169-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="6a169-258">运行应用并选择“讲师”选项卡。该页显示来自相关 `OfficeAssignment` 实体的 `Location`（办公室）。</span><span class="sxs-lookup"><span data-stu-id="6a169-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="6a169-259">如果 `OfficeAssignment` 为 NULL，则显示空白表格单元格。</span><span class="sxs-lookup"><span data-stu-id="6a169-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="6a169-260">单击“选择”链接，选择讲师。</span><span class="sxs-lookup"><span data-stu-id="6a169-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="6a169-261">显示行样式更改和分配给该讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="6a169-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="6a169-262">选择一门课程，查看已注册的学生及其成绩列表。</span><span class="sxs-lookup"><span data-stu-id="6a169-262">Select a course to see the list of enrolled students and their grades.</span></span>

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="6a169-264">使用 Single 方法</span><span class="sxs-lookup"><span data-stu-id="6a169-264">Using Single</span></span>

<span data-ttu-id="6a169-265">`Single` 方法可在 `Where` 条件中进行传递，无需分别调用 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="6a169-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="6a169-266">`Single` 与 Where 条件的配合使用与个人偏好相关。</span><span class="sxs-lookup"><span data-stu-id="6a169-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="6a169-267">相较于使用 `Where` 方法，它没有提供任何优势。</span><span class="sxs-lookup"><span data-stu-id="6a169-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="6a169-268">显式加载</span><span class="sxs-lookup"><span data-stu-id="6a169-268">Explicit loading</span></span>

<span data-ttu-id="6a169-269">当前代码为 `Enrollments` 和 `Students` 指定预先加载：</span><span class="sxs-lookup"><span data-stu-id="6a169-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="6a169-270">假设用户几乎不希望课程中显示注册情况。</span><span class="sxs-lookup"><span data-stu-id="6a169-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="6a169-271">在此情况下，可仅在请求时加载注册数据进行优化。</span><span class="sxs-lookup"><span data-stu-id="6a169-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="6a169-272">在本部分中，会更新 `OnGetAsync` 以使用 `Enrollments` 和 `Students` 的显式加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="6a169-273">使用以下代码更新 Pages/Instructors/Index.cshtml.cs。</span><span class="sxs-lookup"><span data-stu-id="6a169-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="6a169-274">上述代码取消针对注册和学生数据的 ThenInclude 方法调用。</span><span class="sxs-lookup"><span data-stu-id="6a169-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="6a169-275">如果已选中课程，则显式加载的代码会检索：</span><span class="sxs-lookup"><span data-stu-id="6a169-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="6a169-276">所选课程的 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="6a169-277">每个 `Enrollment` 的 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="6a169-278">注意，上述代码注释掉了 `.AsNoTracking()`。</span><span class="sxs-lookup"><span data-stu-id="6a169-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="6a169-279">对于跟踪的实体，仅可显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="6a169-280">测试应用。</span><span class="sxs-lookup"><span data-stu-id="6a169-280">Test the app.</span></span> <span data-ttu-id="6a169-281">对用户而言，该应用的行为与上一版本相同。</span><span class="sxs-lookup"><span data-stu-id="6a169-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6a169-282">后续步骤</span><span class="sxs-lookup"><span data-stu-id="6a169-282">Next steps</span></span>

<span data-ttu-id="6a169-283">下一个教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="6a169-284">[上一个教程](xref:data/ef-rp/complex-data-model)
>[下一个教程](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="6a169-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6a169-285">在本教程中，将读取和显示相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="6a169-286">相关数据为 EF Core 加载到导航属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="6a169-287">如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="6a169-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="6a169-288">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="6a169-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="6a169-289">下图显示了本教程中已完成的页面：</span><span class="sxs-lookup"><span data-stu-id="6a169-289">The following illustrations show the completed pages for this tutorial:</span></span>

![“课程索引”页](read-related-data/_static/courses-index.png)

![“讲师索引”页](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="6a169-292">相关数据的预先加载、显式加载和延迟加载</span><span class="sxs-lookup"><span data-stu-id="6a169-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="6a169-293">EF Core 可采用多种方式将相关数据加载到实体的导航属性中：</span><span class="sxs-lookup"><span data-stu-id="6a169-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="6a169-294">[预先加载](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="6a169-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="6a169-295">预先加载是指对查询某类型的实体时一并加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="6a169-296">读取实体时，会检索其相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="6a169-297">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="6a169-298">EF Core 将针对预先加载的某些类型发出多个查询。</span><span class="sxs-lookup"><span data-stu-id="6a169-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="6a169-299">与存在单一查询的 EF6 中的某些查询相比，发出多个查询可能更有效。</span><span class="sxs-lookup"><span data-stu-id="6a169-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="6a169-300">预先加载通过 `Include` 和 `ThenInclude` 方法进行指定。</span><span class="sxs-lookup"><span data-stu-id="6a169-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="6a169-302">当包含集合导航时，预先加载会发送多个查询：</span><span class="sxs-lookup"><span data-stu-id="6a169-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="6a169-303">一个查询用于主查询</span><span class="sxs-lookup"><span data-stu-id="6a169-303">One query for the main query</span></span> 
  * <span data-ttu-id="6a169-304">一个查询用于加载树中每个集合“边缘”。</span><span class="sxs-lookup"><span data-stu-id="6a169-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="6a169-305">使用 `Load` 的单独查询：可在单独的查询中检索数据，EF Core 会“修复”导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="6a169-306">“修复”是指 EF Core 自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="6a169-307">使用 `Load` 单独查询比预先加载更像是显式加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![单独查询示例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="6a169-309">注意：EF Core 会将导航属性自动“修复”为之前加载到上下文实例中的任何其他实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="6a169-310">即使导航属性的数据非显式包含在内，但如果先前加载了部分或所有相关实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="6a169-311">[显式加载](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="6a169-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="6a169-312">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="6a169-313">必须编写代码才能在需要时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="6a169-314">使用单独查询进行显式加载时，会向数据库发送多个查询。</span><span class="sxs-lookup"><span data-stu-id="6a169-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="6a169-315">该代码通过显式加载指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="6a169-316">使用 `Load` 方法进行显式加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="6a169-317">例如：</span><span class="sxs-lookup"><span data-stu-id="6a169-317">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="6a169-319">[延迟加载](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="6a169-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="6a169-320">[延迟加载已添加到版本 2.1 中的 EF Core](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="6a169-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="6a169-321">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="6a169-322">首次访问导航属性时，会自动检索该导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="6a169-323">首次访问导航属性时，都会向数据库发送一个查询。</span><span class="sxs-lookup"><span data-stu-id="6a169-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="6a169-324">`Select` 运算符仅加载所需的相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="6a169-325">创建显示院系名称的“课程”页</span><span class="sxs-lookup"><span data-stu-id="6a169-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="6a169-326">课程实体包括一个带 `Department` 实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="6a169-327">`Department` 实体包含要分配课程的院系。</span><span class="sxs-lookup"><span data-stu-id="6a169-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="6a169-328">要在课程列表中显示已分配院系的名称：</span><span class="sxs-lookup"><span data-stu-id="6a169-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="6a169-329">从 `Department` 实体中获取 `Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="6a169-330">`Department` 实体来自于 `Course.Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="6a169-332">为课程模型创建基架</span><span class="sxs-lookup"><span data-stu-id="6a169-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a169-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a169-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="6a169-334">按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-the-student-model)中的说明操作，并对模型类使用 `Course`。</span><span class="sxs-lookup"><span data-stu-id="6a169-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a169-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a169-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="6a169-336">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="6a169-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="6a169-337">上述命令为 `Course` 模型创建基架。</span><span class="sxs-lookup"><span data-stu-id="6a169-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="6a169-338">在 Visual Studio 中打开项目。</span><span class="sxs-lookup"><span data-stu-id="6a169-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="6a169-339">打开 Pages/Courses/Index.cshtml.cs 并检查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="6a169-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="6a169-340">基架引擎为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="6a169-341">`Include` 方法指定预先加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="6a169-342">运行应用并选择“课程”链接。</span><span class="sxs-lookup"><span data-stu-id="6a169-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="6a169-343">院系列显示 `DepartmentID`（该项无用）。</span><span class="sxs-lookup"><span data-stu-id="6a169-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="6a169-344">使用以下代码更新 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="6a169-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="6a169-345">上述代码添加了 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="6a169-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="6a169-346">由于未跟踪返回的实体，因此 `AsNoTracking` 提升了性能。</span><span class="sxs-lookup"><span data-stu-id="6a169-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="6a169-347">未跟踪实体，因为未在当前上下文中更新这些实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="6a169-348">使用以下突出显示的标记更新 Pages/Courses/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="6a169-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="6a169-349">对基架代码进行了以下更改：</span><span class="sxs-lookup"><span data-stu-id="6a169-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="6a169-350">将标题从“索引”更改为“课程”。</span><span class="sxs-lookup"><span data-stu-id="6a169-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="6a169-351">添加了显示 `CourseID` 属性值的“数字”列。</span><span class="sxs-lookup"><span data-stu-id="6a169-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="6a169-352">默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。</span><span class="sxs-lookup"><span data-stu-id="6a169-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="6a169-353">但在此情况下主键是有意义的。</span><span class="sxs-lookup"><span data-stu-id="6a169-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="6a169-354">更改“院系”列，显示院系名称。</span><span class="sxs-lookup"><span data-stu-id="6a169-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="6a169-355">该代码显示已加载到 `Department` 导航属性中的 `Department` 实体的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="6a169-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="6a169-356">运行应用并选择“课程”选项卡，查看包含系名称的列表。</span><span class="sxs-lookup"><span data-stu-id="6a169-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![“课程索引”页](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="6a169-358">使用 Select 加载相关数据</span><span class="sxs-lookup"><span data-stu-id="6a169-358">Loading related data with Select</span></span>

<span data-ttu-id="6a169-359">`OnGetAsync` 方法使用 `Include` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="6a169-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="6a169-360">`Select` 运算符仅加载所需的相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="6a169-361">对于单个项（如 `Department.Name`），它使用 SQL INNER JOIN。</span><span class="sxs-lookup"><span data-stu-id="6a169-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="6a169-362">对于集合，它使用另一个数据库访问，但集合上的 `Include` 运算符也是如此。</span><span class="sxs-lookup"><span data-stu-id="6a169-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="6a169-363">以下代码使用 `Select` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="6a169-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="6a169-364">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="6a169-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="6a169-365">有关完整示例的信息，请参阅 [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)。</span><span class="sxs-lookup"><span data-stu-id="6a169-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="6a169-366">创建显示“课程”和“注册”的“讲师”页</span><span class="sxs-lookup"><span data-stu-id="6a169-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="6a169-367">在本部分中，将创建“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="6a169-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="6a169-368">![“讲师索引”页](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="6a169-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="6a169-369">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="6a169-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="6a169-370">讲师列表显示 `OfficeAssignment` 实体（上图中的办公室）的相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="6a169-371">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="6a169-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="6a169-372">预先加载适用于 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="6a169-373">需要显示相关数据时，预先加载通常更高效。</span><span class="sxs-lookup"><span data-stu-id="6a169-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="6a169-374">在此情况下，会显示讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="6a169-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="6a169-375">当用户选择一名讲师（上图中的 Harui）时，显示相关的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="6a169-376">`Instructor` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="6a169-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="6a169-377">对 `Course` 实体及其相关的 `Department` 实体使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="6a169-378">这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="6a169-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="6a169-379">此示例演示如何在位于导航实体内的实体中预先加载这些导航实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="6a169-380">当用户选择一门课程（上图中的化学）时，显示 `Enrollments` 实体的相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="6a169-381">上图中显示了学生姓名和成绩。</span><span class="sxs-lookup"><span data-stu-id="6a169-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="6a169-382">`Course` 和 `Enrollment` 实体之间存在一对多的关系。</span><span class="sxs-lookup"><span data-stu-id="6a169-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="6a169-383">创建“讲师索引”视图的视图模型</span><span class="sxs-lookup"><span data-stu-id="6a169-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="6a169-384">“讲师”页显示来自三个不同表格的数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="6a169-385">创建一个视图模型，该模型中包含表示三个表格的三个实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="6a169-386">在 SchoolViewModels 文件夹中，使用以下代码创建 InstructorIndexData.cs：</span><span class="sxs-lookup"><span data-stu-id="6a169-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="6a169-387">为讲师模型创建基架</span><span class="sxs-lookup"><span data-stu-id="6a169-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a169-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a169-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="6a169-389">按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-the-student-model)中的说明操作，并对模型类使用 `Instructor`。</span><span class="sxs-lookup"><span data-stu-id="6a169-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a169-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a169-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="6a169-391">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="6a169-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="6a169-392">上述命令为 `Instructor` 模型创建基架。</span><span class="sxs-lookup"><span data-stu-id="6a169-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="6a169-393">运行应用并导航到“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="6a169-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="6a169-394">将 Pages/Instructors/Index.cshtml.cs 替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6a169-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="6a169-395">`OnGetAsync` 方法接受所选讲师 ID 的可选路由数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="6a169-396">检查 Pages/Instructors/Index.cshtml.cs 文件中的查询：</span><span class="sxs-lookup"><span data-stu-id="6a169-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="6a169-397">查询包括两项内容：</span><span class="sxs-lookup"><span data-stu-id="6a169-397">The query has two includes:</span></span>

* <span data-ttu-id="6a169-398">`OfficeAssignment`：在[讲师视图](#IP)中显示。</span><span class="sxs-lookup"><span data-stu-id="6a169-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="6a169-399">`CourseAssignments`：课程的教学内容。</span><span class="sxs-lookup"><span data-stu-id="6a169-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="6a169-400">更新“讲师索引”页</span><span class="sxs-lookup"><span data-stu-id="6a169-400">Update the instructors Index page</span></span>

<span data-ttu-id="6a169-401">使用以下标记更新 Pages/Instructors/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="6a169-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="6a169-402">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="6a169-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="6a169-403">将 `page` 指令从 `@page` 更新为 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="6a169-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="6a169-404">`"{id:int?}"` 是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="6a169-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="6a169-405">路由模板将 URL 中的整数查询字符串更改为路由数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="6a169-406">例如，单击仅具有 `@page` 指令的讲师的“选择”链接将生成如下 URL：</span><span class="sxs-lookup"><span data-stu-id="6a169-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="6a169-407">当页面指令是 `@page "{id:int?}"` 时，之前的 URL 为：</span><span class="sxs-lookup"><span data-stu-id="6a169-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="6a169-408">页标题为“讲师”。</span><span class="sxs-lookup"><span data-stu-id="6a169-408">Page title is **Instructors**.</span></span>
* <span data-ttu-id="6a169-409">添加了仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列。</span><span class="sxs-lookup"><span data-stu-id="6a169-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="6a169-410">由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="6a169-411">添加了显示每位讲师所授课程的“课程”列。</span><span class="sxs-lookup"><span data-stu-id="6a169-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="6a169-412">有关此 razor 语法的详细信息，请参阅[显式行转换](xref:mvc/views/razor#explicit-line-transition)。</span><span class="sxs-lookup"><span data-stu-id="6a169-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="6a169-413">添加了向所选讲师的 `tr` 元素中动态添加 `class="success"` 的代码。</span><span class="sxs-lookup"><span data-stu-id="6a169-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="6a169-414">此时会使用 Bootstrap 类为所选行设置背景色。</span><span class="sxs-lookup"><span data-stu-id="6a169-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="6a169-415">添加了标记为“选择”的新的超链接。</span><span class="sxs-lookup"><span data-stu-id="6a169-415">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="6a169-416">该链接将所选讲师的 ID 发送给 `Index` 方法并设置背景色。</span><span class="sxs-lookup"><span data-stu-id="6a169-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="6a169-417">运行应用并选择“讲师”选项卡。该页显示来自相关 `OfficeAssignment` 实体的 `Location`（办公室）。</span><span class="sxs-lookup"><span data-stu-id="6a169-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="6a169-418">如果 OfficeAssignment\` 为 NULL，则显示空白表格单元格。</span><span class="sxs-lookup"><span data-stu-id="6a169-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="6a169-419">单击“选择”链接。</span><span class="sxs-lookup"><span data-stu-id="6a169-419">Click on the **Select** link.</span></span> <span data-ttu-id="6a169-420">随即更改行样式。</span><span class="sxs-lookup"><span data-stu-id="6a169-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="6a169-421">添加由所选讲师教授的课程</span><span class="sxs-lookup"><span data-stu-id="6a169-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="6a169-422">将 Pages/Instructors/Index.cshtml.cs 中的 `OnGetAsync` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6a169-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="6a169-423">添加 `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="6a169-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="6a169-424">检查更新后的查询：</span><span class="sxs-lookup"><span data-stu-id="6a169-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="6a169-425">先前查询添加了 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="6a169-426">选择讲师时 (`id != null`)，将执行以下代码。</span><span class="sxs-lookup"><span data-stu-id="6a169-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="6a169-427">从视图模型中的讲师列表检索所选讲师。</span><span class="sxs-lookup"><span data-stu-id="6a169-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="6a169-428">向视图模型的 `Courses` 属性加载来自讲师 `CourseAssignments` 导航属性的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="6a169-429">`Where` 方法返回一个集合。</span><span class="sxs-lookup"><span data-stu-id="6a169-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="6a169-430">在前面的 `Where` 方法中，仅返回单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="6a169-431">`Single` 方法将集合转换为单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="6a169-432">`Instructor` 实体提供对 `CourseAssignments` 属性的访问。</span><span class="sxs-lookup"><span data-stu-id="6a169-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="6a169-433">`CourseAssignments` 提供对相关 `Course` 实体的访问。</span><span class="sxs-lookup"><span data-stu-id="6a169-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="6a169-435">当集合仅包含一个项时，集合使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="6a169-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="6a169-436">如果集合为空或包含多个项，`Single` 方法会引发异常。</span><span class="sxs-lookup"><span data-stu-id="6a169-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="6a169-437">还可使用 `SingleOrDefault`，该方式在集合为空时返回默认值（本例中为 null）。</span><span class="sxs-lookup"><span data-stu-id="6a169-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="6a169-438">在空集合上使用 `SingleOrDefault`：</span><span class="sxs-lookup"><span data-stu-id="6a169-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="6a169-439">引发异常（因为尝试在空引用上找到 `Courses` 属性）。</span><span class="sxs-lookup"><span data-stu-id="6a169-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="6a169-440">异常信息不太能清楚指出问题原因。</span><span class="sxs-lookup"><span data-stu-id="6a169-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="6a169-441">选中课程时，视图模型的 `Enrollments` 属性将填充以下代码：</span><span class="sxs-lookup"><span data-stu-id="6a169-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="6a169-442">在 Pages/Instructors/Index.cshtml Razor 页面末尾添加以下标记：</span><span class="sxs-lookup"><span data-stu-id="6a169-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="6a169-443">上述标记显示选中某讲师时与该讲师相关的课程列表。</span><span class="sxs-lookup"><span data-stu-id="6a169-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="6a169-444">测试应用。</span><span class="sxs-lookup"><span data-stu-id="6a169-444">Test the app.</span></span> <span data-ttu-id="6a169-445">单击讲师页面上的“选择”链接。</span><span class="sxs-lookup"><span data-stu-id="6a169-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="6a169-446">显示学生数据</span><span class="sxs-lookup"><span data-stu-id="6a169-446">Show student data</span></span>

<span data-ttu-id="6a169-447">在本部分中，更新应用以显示所选课程的学生数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="6a169-448">使用以下代码在 Pages/Instructors/Index.cshtml.cs 中更新 `OnGetAsync` 方法中的查询：</span><span class="sxs-lookup"><span data-stu-id="6a169-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="6a169-449">更新 Pages/Instructors/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="6a169-449">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="6a169-450">在文件末尾添加以下标记：</span><span class="sxs-lookup"><span data-stu-id="6a169-450">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="6a169-451">上述标记显示已注册所选课程的学生列表。</span><span class="sxs-lookup"><span data-stu-id="6a169-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="6a169-452">刷新页面并选择讲师。</span><span class="sxs-lookup"><span data-stu-id="6a169-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="6a169-453">选择一门课程，查看已注册的学生及其成绩列表。</span><span class="sxs-lookup"><span data-stu-id="6a169-453">Select a course to see the list of enrolled students and their grades.</span></span>

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="6a169-455">使用 Single 方法</span><span class="sxs-lookup"><span data-stu-id="6a169-455">Using Single</span></span>

<span data-ttu-id="6a169-456">`Single` 方法可在 `Where` 条件中进行传递，无需分别调用 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="6a169-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="6a169-457">使用 `Where` 时，前面的 `Single` 方法不适用。</span><span class="sxs-lookup"><span data-stu-id="6a169-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="6a169-458">一些开发人员更喜欢 `Single` 方法样式。</span><span class="sxs-lookup"><span data-stu-id="6a169-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="6a169-459">显式加载</span><span class="sxs-lookup"><span data-stu-id="6a169-459">Explicit loading</span></span>

<span data-ttu-id="6a169-460">当前代码为 `Enrollments` 和 `Students` 指定预先加载：</span><span class="sxs-lookup"><span data-stu-id="6a169-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="6a169-461">假设用户几乎不希望课程中显示注册情况。</span><span class="sxs-lookup"><span data-stu-id="6a169-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="6a169-462">在此情况下，可仅在请求时加载注册数据进行优化。</span><span class="sxs-lookup"><span data-stu-id="6a169-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="6a169-463">在本部分中，会更新 `OnGetAsync` 以使用 `Enrollments` 和 `Students` 的显式加载。</span><span class="sxs-lookup"><span data-stu-id="6a169-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="6a169-464">使用以下代码更新 `OnGetAsync`：</span><span class="sxs-lookup"><span data-stu-id="6a169-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="6a169-465">上述代码取消针对注册和学生数据的 ThenInclude 方法调用。</span><span class="sxs-lookup"><span data-stu-id="6a169-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="6a169-466">如果已选中课程，则突出显示的代码会检索：</span><span class="sxs-lookup"><span data-stu-id="6a169-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="6a169-467">所选课程的 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="6a169-468">每个 `Enrollment` 的 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="6a169-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="6a169-469">请注意，上述代码为 `.AsNoTracking()` 加上注释。</span><span class="sxs-lookup"><span data-stu-id="6a169-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="6a169-470">对于跟踪的实体，仅可显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="6a169-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="6a169-471">测试应用。</span><span class="sxs-lookup"><span data-stu-id="6a169-471">Test the app.</span></span> <span data-ttu-id="6a169-472">对用户而言，该应用的行为与上一版本相同。</span><span class="sxs-lookup"><span data-stu-id="6a169-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="6a169-473">下一个教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="6a169-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a169-474">其他资源</span><span class="sxs-lookup"><span data-stu-id="6a169-474">Additional resources</span></span>

* [<span data-ttu-id="6a169-475">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="6a169-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="6a169-476">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="6a169-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="6a169-477">[上一页](xref:data/ef-rp/complex-data-model)
>[下一页](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="6a169-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
