---
title: ASP.NET Core 中的 Razor 页面和 EF Core - 读取相关数据 - 第 6 个教程（共 8 个）
author: tdykstra
description: 在本教程中，将读取并显示相关数据 - 即 Entity Framework 加载到导航属性中的数据。
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: c9da404b1bbd072d3e033f18a7366169082dac06
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583547"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="72a4c-103">ASP.NET Core 中的 Razor 页面和 EF Core - 读取相关数据 - 第 6 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="72a4c-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="72a4c-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="72a4c-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="72a4c-105">本教程介绍如何读取和显示相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="72a4c-106">相关数据为 EF Core 加载到导航属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="72a4c-107">下图显示了本教程中已完成的页面：</span><span class="sxs-lookup"><span data-stu-id="72a4c-107">The following illustrations show the completed pages for this tutorial:</span></span>

![“课程索引”页](read-related-data/_static/courses-index30.png)

![“讲师索引”页](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="72a4c-110">预先加载、显式加载和延迟加载</span><span class="sxs-lookup"><span data-stu-id="72a4c-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="72a4c-111">EF Core 可采用多种方式将相关数据加载到实体的导航属性中：</span><span class="sxs-lookup"><span data-stu-id="72a4c-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="72a4c-112">[预先加载](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="72a4c-113">预先加载是指对查询某类型的实体时一并加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="72a4c-114">读取实体时，会检索其相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="72a4c-115">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="72a4c-116">EF Core 将针对预先加载的某些类型发出多个查询。</span><span class="sxs-lookup"><span data-stu-id="72a4c-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="72a4c-117">发布多个查询可能比发布大型的单个查询更为有效。</span><span class="sxs-lookup"><span data-stu-id="72a4c-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="72a4c-118">预先加载通过 `Include` 和 `ThenInclude` 方法进行指定。</span><span class="sxs-lookup"><span data-stu-id="72a4c-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="72a4c-120">当包含集合导航时，预先加载会发送多个查询：</span><span class="sxs-lookup"><span data-stu-id="72a4c-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="72a4c-121">一个查询用于主查询</span><span class="sxs-lookup"><span data-stu-id="72a4c-121">One query for the main query</span></span> 
  * <span data-ttu-id="72a4c-122">一个查询用于加载树中每个集合“边缘”。</span><span class="sxs-lookup"><span data-stu-id="72a4c-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="72a4c-123">使用 `Load` 的单独查询：可在单独的查询中检索数据，EF Core 会“修复”导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="72a4c-124">“修复”是指 EF Core 自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="72a4c-125">使用 `Load` 单独查询比预先加载更像是显式加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![单独查询示例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="72a4c-127">注意：EF Core 会将导航属性自动“修复”为之前加载到上下文实例中的任何其他实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="72a4c-128">即使导航属性的数据非显式包含在内  ，但如果先前加载了部分或所有相关实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="72a4c-129">[显式加载](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="72a4c-130">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="72a4c-131">必须编写代码才能在需要时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="72a4c-132">使用单独查询进行显式加载时，会向数据库发送多个查询。</span><span class="sxs-lookup"><span data-stu-id="72a4c-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="72a4c-133">该代码通过显式加载指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="72a4c-134">使用 `Load` 方法进行显式加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="72a4c-135">例如:</span><span class="sxs-lookup"><span data-stu-id="72a4c-135">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="72a4c-137">[延迟加载](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="72a4c-138">[延迟加载已添加到版本 2.1 中的 EF Core](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="72a4c-139">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="72a4c-140">首次访问导航属性时，会自动检索该导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="72a4c-141">首次访问导航属性时，都会向数据库发送一个查询。</span><span class="sxs-lookup"><span data-stu-id="72a4c-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="72a4c-142">创建“课程”页</span><span class="sxs-lookup"><span data-stu-id="72a4c-142">Create Course pages</span></span>

<span data-ttu-id="72a4c-143">`Course` 实体包括一个带相关 `Department` 实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="72a4c-145">若要显示课程的已分配院系的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="72a4c-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="72a4c-146">将相关的 `Department` 实体加载到 `Course.Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="72a4c-147">获取 `Department` 实体的 `Name` 属性中的名称。</span><span class="sxs-lookup"><span data-stu-id="72a4c-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="72a4c-148">搭建“课程”页的基架</span><span class="sxs-lookup"><span data-stu-id="72a4c-148">Scaffold Course pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72a4c-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72a4c-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="72a4c-150">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="72a4c-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="72a4c-151">创建“Pages/Courses”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="72a4c-152">将 `Course` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="72a4c-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="72a4c-153">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="72a4c-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72a4c-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72a4c-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="72a4c-155">创建“Pages/Courses”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="72a4c-156">运行以下命令，搭建“课程”页的基架。</span><span class="sxs-lookup"><span data-stu-id="72a4c-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="72a4c-157">在 Windows 上： </span><span class="sxs-lookup"><span data-stu-id="72a4c-157">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="72a4c-158">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="72a4c-158">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="72a4c-159">打开 Pages/Courses/Index.cshtml.cs  并检查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="72a4c-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="72a4c-160">基架引擎为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="72a4c-161">`Include` 方法指定预先加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="72a4c-162">运行应用并选择“课程”链接  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="72a4c-163">院系列显示 `DepartmentID`（该项无用）。</span><span class="sxs-lookup"><span data-stu-id="72a4c-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="72a4c-164">显示院系名称</span><span class="sxs-lookup"><span data-stu-id="72a4c-164">Display the department name</span></span>

<span data-ttu-id="72a4c-165">使用以下代码更新 Pages/Courses/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="72a4c-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="72a4c-166">上述代码将 `Course` 属性更改为 `Courses`，然后添加 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="72a4c-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="72a4c-167">由于未跟踪返回的实体，因此 `AsNoTracking` 提升了性能。</span><span class="sxs-lookup"><span data-stu-id="72a4c-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="72a4c-168">无需跟踪实体，因为未在当前的上下文中更新这些实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="72a4c-169">使用以下代码更新 Pages/Courses/Index.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="72a4c-170">对基架代码进行了以下更改：</span><span class="sxs-lookup"><span data-stu-id="72a4c-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="72a4c-171">将 `Course` 属性名称更改为了 `Courses`。</span><span class="sxs-lookup"><span data-stu-id="72a4c-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="72a4c-172">添加了显示 `CourseID` 属性值的“数字”列  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="72a4c-173">默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。</span><span class="sxs-lookup"><span data-stu-id="72a4c-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="72a4c-174">但在此情况下主键是有意义的。</span><span class="sxs-lookup"><span data-stu-id="72a4c-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="72a4c-175">更改“院系”列，显示院系名称  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="72a4c-176">该代码显示已加载到 `Department` 导航属性中的 `Department` 实体的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="72a4c-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="72a4c-177">运行应用并选择“课程”选项卡，查看包含系名称的列表  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![“课程索引”页](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="72a4c-179">使用 Select 加载相关数据</span><span class="sxs-lookup"><span data-stu-id="72a4c-179">Loading related data with Select</span></span>

<span data-ttu-id="72a4c-180">`OnGetAsync` 方法使用 `Include` 方法加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="72a4c-181">`Select` 方法是只加载所需相关数据的替代方法。</span><span class="sxs-lookup"><span data-stu-id="72a4c-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="72a4c-182">对于单个项（如 `Department.Name`），它使用 SQL INNER JOIN。</span><span class="sxs-lookup"><span data-stu-id="72a4c-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="72a4c-183">对于集合，它使用另一个数据库访问，但集合上的 `Include` 运算符也是如此。</span><span class="sxs-lookup"><span data-stu-id="72a4c-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="72a4c-184">以下代码使用 `Select` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="72a4c-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="72a4c-185">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="72a4c-185">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="72a4c-186">有关完整示例的信息，请参阅 [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-186">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="72a4c-187">创建“讲师”页</span><span class="sxs-lookup"><span data-stu-id="72a4c-187">Create Instructor pages</span></span>

<span data-ttu-id="72a4c-188">本节搭建“讲师”页的基架，并向讲师“索引”页添加相关“课程”和“注册”。</span><span class="sxs-lookup"><span data-stu-id="72a4c-188">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="72a4c-189">![“讲师索引”页](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="72a4c-189">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="72a4c-190">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="72a4c-190">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="72a4c-191">讲师列表显示 `OfficeAssignment` 实体（上图中的办公室）的相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-191">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="72a4c-192">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="72a4c-192">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="72a4c-193">预先加载适用于 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-193">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="72a4c-194">需要显示相关数据时，预先加载通常更高效。</span><span class="sxs-lookup"><span data-stu-id="72a4c-194">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="72a4c-195">在此情况下，会显示讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="72a4c-195">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="72a4c-196">用户选择一名讲师时，显示相关 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-196">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="72a4c-197">`Instructor` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="72a4c-197">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="72a4c-198">对 `Course` 实体及其相关的 `Department` 实体使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-198">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="72a4c-199">这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="72a4c-199">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="72a4c-200">此示例演示如何在位于导航实体内的实体中预先加载这些导航实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-200">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="72a4c-201">用户选择一门课程时，会显示 `Enrollments` 实体的相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-201">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="72a4c-202">上图中显示了学生姓名和成绩。</span><span class="sxs-lookup"><span data-stu-id="72a4c-202">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="72a4c-203">`Course` 和 `Enrollment` 实体之间存在一对多的关系。</span><span class="sxs-lookup"><span data-stu-id="72a4c-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="72a4c-204">创建视图模型</span><span class="sxs-lookup"><span data-stu-id="72a4c-204">Create a view model</span></span>

<span data-ttu-id="72a4c-205">“讲师”页显示来自三个不同表格的数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-205">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="72a4c-206">需要一个视图模型，该模型中包含表示三个表格的三个属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-206">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="72a4c-207">使用以下代码创建 SchoolViewModels/InstructorIndexData.cs  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-207">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="72a4c-208">搭建“讲师”页的基架</span><span class="sxs-lookup"><span data-stu-id="72a4c-208">Scaffold Instructor pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72a4c-209">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72a4c-209">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="72a4c-210">遵循[搭建“学生”页的基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="72a4c-210">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="72a4c-211">创建“Pages/Instructors”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-211">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="72a4c-212">将 `Instructor` 用于模型类。</span><span class="sxs-lookup"><span data-stu-id="72a4c-212">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="72a4c-213">使用现有的上下文类，而不是新建上下文类。</span><span class="sxs-lookup"><span data-stu-id="72a4c-213">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72a4c-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72a4c-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="72a4c-215">创建“Pages/Instructors”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-215">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="72a4c-216">运行以下命令，搭建“讲师”页的基架。</span><span class="sxs-lookup"><span data-stu-id="72a4c-216">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="72a4c-217">在 Windows 上： </span><span class="sxs-lookup"><span data-stu-id="72a4c-217">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="72a4c-218">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="72a4c-218">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="72a4c-219">若要在更新之前查看已搭建基架的页面的外观，则运行应用并导航到“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="72a4c-219">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="72a4c-220">使用以下代码更新 Pages/Instructors/Index.cshtml.cs  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-220">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="72a4c-221">`OnGetAsync` 方法接受所选讲师 ID 的可选路由数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-221">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="72a4c-222">检查 Pages/Instructors/Index.cshtml.cs 文件中的查询  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-222">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="72a4c-223">代码指定以下导航属性的预先加载：</span><span class="sxs-lookup"><span data-stu-id="72a4c-223">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="72a4c-224">注意 `CourseAssignments` 和 `Course` 对 `Include` 和 `ThenInclude` 方法的重复使用。</span><span class="sxs-lookup"><span data-stu-id="72a4c-224">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="72a4c-225">若要指定 `Course` 实体的两个导航属性的预先加载，则这种重复使用是必要的。</span><span class="sxs-lookup"><span data-stu-id="72a4c-225">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="72a4c-226">选择讲师时 (`id != null`)，将执行以下代码。</span><span class="sxs-lookup"><span data-stu-id="72a4c-226">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="72a4c-227">从视图模型中的讲师列表检索所选讲师。</span><span class="sxs-lookup"><span data-stu-id="72a4c-227">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="72a4c-228">向视图模型的 `Courses` 属性加载来自讲师 `CourseAssignments` 导航属性的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-228">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="72a4c-229">`Where` 方法返回一个集合。</span><span class="sxs-lookup"><span data-stu-id="72a4c-229">The `Where` method returns a collection.</span></span> <span data-ttu-id="72a4c-230">但在本例中，筛选器将选择单个实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-230">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="72a4c-231">因此，调用 `Single` 方法将集合转换为单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-231">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="72a4c-232">`Instructor` 实体提供对 `CourseAssignments` 属性的访问。</span><span class="sxs-lookup"><span data-stu-id="72a4c-232">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="72a4c-233">`CourseAssignments` 提供对相关 `Course` 实体的访问。</span><span class="sxs-lookup"><span data-stu-id="72a4c-233">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="72a4c-235">当集合仅包含一个项时，集合使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="72a4c-235">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="72a4c-236">如果集合为空或包含多个项，`Single` 方法会引发异常。</span><span class="sxs-lookup"><span data-stu-id="72a4c-236">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="72a4c-237">还可使用 `SingleOrDefault`，该方式在集合为空时返回默认值（本例中为 null）。</span><span class="sxs-lookup"><span data-stu-id="72a4c-237">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="72a4c-238">选中课程时，视图模型的 `Enrollments` 属性将填充以下代码：</span><span class="sxs-lookup"><span data-stu-id="72a4c-238">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="72a4c-239">更新“讲师索引”页</span><span class="sxs-lookup"><span data-stu-id="72a4c-239">Update the instructors Index page</span></span>

<span data-ttu-id="72a4c-240">使用以下代码更新 Pages/Instructors/Index.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-240">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="72a4c-241">上面的代码执行以下更改：</span><span class="sxs-lookup"><span data-stu-id="72a4c-241">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="72a4c-242">将 `page` 指令从 `@page` 更新为 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="72a4c-242">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="72a4c-243">`"{id:int?}"` 是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="72a4c-243">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="72a4c-244">路由模板将 URL 中的整数查询字符串更改为路由数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-244">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="72a4c-245">例如，单击仅具有 `@page` 指令的讲师的“选择”链接将生成如下 URL  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-245">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="72a4c-246">如果页面指令为 `@page "{id:int?}"` 时，则 URL 为：</span><span class="sxs-lookup"><span data-stu-id="72a4c-246">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="72a4c-247">添加仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-247">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="72a4c-248">由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-248">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="72a4c-249">添加显示每位讲师所授课程的“课程”列  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-249">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="72a4c-250">有关此 Razor 语法的详细信息，请参阅[使用 `@:` 进行显式行转换](xref:mvc/views/razor#explicit-line-transition-with-)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-250">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="72a4c-251">添加向所选讲师和课程的 `tr` 元素中动态添加 `class="success"` 的代码。</span><span class="sxs-lookup"><span data-stu-id="72a4c-251">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="72a4c-252">此时会使用 Bootstrap 类为所选行设置背景色。</span><span class="sxs-lookup"><span data-stu-id="72a4c-252">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="72a4c-253">添加标记为“选择”的新的超链接  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-253">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="72a4c-254">该链接将所选讲师的 ID 发送给 `Index` 方法并设置背景色。</span><span class="sxs-lookup"><span data-stu-id="72a4c-254">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="72a4c-255">添加所选讲师的课程表。</span><span class="sxs-lookup"><span data-stu-id="72a4c-255">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="72a4c-256">添加所选课程的学生注册表。</span><span class="sxs-lookup"><span data-stu-id="72a4c-256">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="72a4c-257">运行应用并选择“讲师”选项卡  。该页显示来自相关 `OfficeAssignment` 实体的 `Location`（办公室）。</span><span class="sxs-lookup"><span data-stu-id="72a4c-257">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="72a4c-258">如果 `OfficeAssignment` 为 NULL，则显示空白表格单元格。</span><span class="sxs-lookup"><span data-stu-id="72a4c-258">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="72a4c-259">单击“选择”链接，选择讲师  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-259">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="72a4c-260">显示行样式更改和分配给该讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="72a4c-260">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="72a4c-261">选择一门课程，查看已注册的学生及其成绩列表。</span><span class="sxs-lookup"><span data-stu-id="72a4c-261">Select a course to see the list of enrolled students and their grades.</span></span>

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="72a4c-263">使用 Single 方法</span><span class="sxs-lookup"><span data-stu-id="72a4c-263">Using Single</span></span>

<span data-ttu-id="72a4c-264">`Single` 方法可在 `Where` 条件中进行传递，无需分别调用 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="72a4c-264">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="72a4c-265">`Single` 与 Where 条件的配合使用与个人偏好相关。</span><span class="sxs-lookup"><span data-stu-id="72a4c-265">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="72a4c-266">相较于使用 `Where` 方法，它没有提供任何优势。</span><span class="sxs-lookup"><span data-stu-id="72a4c-266">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="72a4c-267">显式加载</span><span class="sxs-lookup"><span data-stu-id="72a4c-267">Explicit loading</span></span>

<span data-ttu-id="72a4c-268">当前代码为 `Enrollments` 和 `Students` 指定预先加载：</span><span class="sxs-lookup"><span data-stu-id="72a4c-268">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="72a4c-269">假设用户几乎不希望课程中显示注册情况。</span><span class="sxs-lookup"><span data-stu-id="72a4c-269">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="72a4c-270">在此情况下，可仅在请求时加载注册数据进行优化。</span><span class="sxs-lookup"><span data-stu-id="72a4c-270">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="72a4c-271">在本部分中，会更新 `OnGetAsync` 以使用 `Enrollments` 和 `Students` 的显式加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-271">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="72a4c-272">使用以下代码更新 Pages/Instructors/Index.cshtml.cs  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-272">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="72a4c-273">上述代码取消针对注册和学生数据的 ThenInclude 方法调用  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-273">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="72a4c-274">如果已选中课程，则显式加载的代码会检索：</span><span class="sxs-lookup"><span data-stu-id="72a4c-274">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="72a4c-275">所选课程的 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-275">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="72a4c-276">每个 `Enrollment` 的 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-276">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="72a4c-277">注意，上述代码注释掉了 `.AsNoTracking()`。</span><span class="sxs-lookup"><span data-stu-id="72a4c-277">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="72a4c-278">对于跟踪的实体，仅可显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-278">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="72a4c-279">测试应用。</span><span class="sxs-lookup"><span data-stu-id="72a4c-279">Test the app.</span></span> <span data-ttu-id="72a4c-280">对用户而言，该应用的行为与上一版本相同。</span><span class="sxs-lookup"><span data-stu-id="72a4c-280">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="72a4c-281">后续步骤</span><span class="sxs-lookup"><span data-stu-id="72a4c-281">Next steps</span></span>

<span data-ttu-id="72a4c-282">下一个教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-282">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="72a4c-283">[上一个教程](xref:data/ef-rp/complex-data-model)
>[下一个教程](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="72a4c-283">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="72a4c-284">在本教程中，将读取和显示相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-284">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="72a4c-285">相关数据为 EF Core 加载到导航属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-285">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="72a4c-286">如果遇到无法解决的问题，请[下载或查看已完成的应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-286">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="72a4c-287">[下载说明](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-287">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="72a4c-288">下图显示了本教程中已完成的页面：</span><span class="sxs-lookup"><span data-stu-id="72a4c-288">The following illustrations show the completed pages for this tutorial:</span></span>

![“课程索引”页](read-related-data/_static/courses-index.png)

![“讲师索引”页](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="72a4c-291">相关数据的预先加载、显式加载和延迟加载</span><span class="sxs-lookup"><span data-stu-id="72a4c-291">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="72a4c-292">EF Core 可采用多种方式将相关数据加载到实体的导航属性中：</span><span class="sxs-lookup"><span data-stu-id="72a4c-292">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="72a4c-293">[预先加载](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-293">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="72a4c-294">预先加载是指对查询某类型的实体时一并加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-294">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="72a4c-295">读取实体时，会检索其相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-295">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="72a4c-296">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-296">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="72a4c-297">EF Core 将针对预先加载的某些类型发出多个查询。</span><span class="sxs-lookup"><span data-stu-id="72a4c-297">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="72a4c-298">与存在单一查询的 EF6 中的某些查询相比，发出多个查询可能更有效。</span><span class="sxs-lookup"><span data-stu-id="72a4c-298">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="72a4c-299">预先加载通过 `Include` 和 `ThenInclude` 方法进行指定。</span><span class="sxs-lookup"><span data-stu-id="72a4c-299">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="72a4c-301">当包含集合导航时，预先加载会发送多个查询：</span><span class="sxs-lookup"><span data-stu-id="72a4c-301">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="72a4c-302">一个查询用于主查询</span><span class="sxs-lookup"><span data-stu-id="72a4c-302">One query for the main query</span></span> 
  * <span data-ttu-id="72a4c-303">一个查询用于加载树中每个集合“边缘”。</span><span class="sxs-lookup"><span data-stu-id="72a4c-303">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="72a4c-304">使用 `Load` 的单独查询：可在单独的查询中检索数据，EF Core 会“修复”导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-304">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="72a4c-305">“修复”是指 EF Core 自动填充导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-305">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="72a4c-306">使用 `Load` 单独查询比预先加载更像是显式加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-306">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![单独查询示例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="72a4c-308">注意：EF Core 会将导航属性自动“修复”为之前加载到上下文实例中的任何其他实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-308">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="72a4c-309">即使导航属性的数据非显式包含在内  ，但如果先前加载了部分或所有相关实体，则仍可能填充该属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-309">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="72a4c-310">[显式加载](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-310">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="72a4c-311">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-311">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="72a4c-312">必须编写代码才能在需要时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-312">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="72a4c-313">使用单独查询进行显式加载时，会向数据库发送多个查询。</span><span class="sxs-lookup"><span data-stu-id="72a4c-313">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="72a4c-314">该代码通过显式加载指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-314">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="72a4c-315">使用 `Load` 方法进行显式加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-315">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="72a4c-316">例如:</span><span class="sxs-lookup"><span data-stu-id="72a4c-316">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="72a4c-318">[延迟加载](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-318">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="72a4c-319">[延迟加载已添加到版本 2.1 中的 EF Core](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-319">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="72a4c-320">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-320">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="72a4c-321">首次访问导航属性时，会自动检索该导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-321">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="72a4c-322">首次访问导航属性时，都会向数据库发送一个查询。</span><span class="sxs-lookup"><span data-stu-id="72a4c-322">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="72a4c-323">`Select` 运算符仅加载所需的相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-323">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="72a4c-324">创建显示院系名称的“课程”页</span><span class="sxs-lookup"><span data-stu-id="72a4c-324">Create a Course page that displays department name</span></span>

<span data-ttu-id="72a4c-325">课程实体包括一个带 `Department` 实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-325">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="72a4c-326">`Department` 实体包含要分配课程的院系。</span><span class="sxs-lookup"><span data-stu-id="72a4c-326">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="72a4c-327">要在课程列表中显示已分配院系的名称：</span><span class="sxs-lookup"><span data-stu-id="72a4c-327">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="72a4c-328">从 `Department` 实体中获取 `Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-328">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="72a4c-329">`Department` 实体来自于 `Course.Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-329">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="72a4c-331">为课程模型创建基架</span><span class="sxs-lookup"><span data-stu-id="72a4c-331">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72a4c-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72a4c-332">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="72a4c-333">按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明操作，并对模型类使用 `Course`。</span><span class="sxs-lookup"><span data-stu-id="72a4c-333">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Course` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72a4c-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72a4c-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="72a4c-335">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="72a4c-335">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="72a4c-336">上述命令为 `Course` 模型创建基架。</span><span class="sxs-lookup"><span data-stu-id="72a4c-336">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="72a4c-337">在 Visual Studio 中打开项目。</span><span class="sxs-lookup"><span data-stu-id="72a4c-337">Open the project in Visual Studio.</span></span>

<span data-ttu-id="72a4c-338">打开 Pages/Courses/Index.cshtml.cs  并检查 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="72a4c-338">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="72a4c-339">基架引擎为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-339">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="72a4c-340">`Include` 方法指定预先加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-340">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="72a4c-341">运行应用并选择“课程”链接  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-341">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="72a4c-342">院系列显示 `DepartmentID`（该项无用）。</span><span class="sxs-lookup"><span data-stu-id="72a4c-342">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="72a4c-343">使用以下代码更新 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="72a4c-343">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="72a4c-344">上述代码添加了 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="72a4c-344">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="72a4c-345">由于未跟踪返回的实体，因此 `AsNoTracking` 提升了性能。</span><span class="sxs-lookup"><span data-stu-id="72a4c-345">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="72a4c-346">未跟踪实体，因为未在当前上下文中更新这些实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-346">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="72a4c-347">使用以下突出显示的标记更新 Pages/Courses/Index.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-347">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="72a4c-348">对基架代码进行了以下更改：</span><span class="sxs-lookup"><span data-stu-id="72a4c-348">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="72a4c-349">将标题从“索引”更改为“课程”。</span><span class="sxs-lookup"><span data-stu-id="72a4c-349">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="72a4c-350">添加了显示 `CourseID` 属性值的“数字”列  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-350">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="72a4c-351">默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。</span><span class="sxs-lookup"><span data-stu-id="72a4c-351">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="72a4c-352">但在此情况下主键是有意义的。</span><span class="sxs-lookup"><span data-stu-id="72a4c-352">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="72a4c-353">更改“院系”列，显示院系名称  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-353">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="72a4c-354">该代码显示已加载到 `Department` 导航属性中的 `Department` 实体的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="72a4c-354">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="72a4c-355">运行应用并选择“课程”选项卡，查看包含系名称的列表  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-355">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![“课程索引”页](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="72a4c-357">使用 Select 加载相关数据</span><span class="sxs-lookup"><span data-stu-id="72a4c-357">Loading related data with Select</span></span>

<span data-ttu-id="72a4c-358">`OnGetAsync` 方法使用 `Include` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="72a4c-358">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="72a4c-359">`Select` 运算符仅加载所需的相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-359">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="72a4c-360">对于单个项（如 `Department.Name`），它使用 SQL INNER JOIN。</span><span class="sxs-lookup"><span data-stu-id="72a4c-360">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="72a4c-361">对于集合，它使用另一个数据库访问，但集合上的 `Include` 运算符也是如此。</span><span class="sxs-lookup"><span data-stu-id="72a4c-361">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="72a4c-362">以下代码使用 `Select` 方法加载相关数据：</span><span class="sxs-lookup"><span data-stu-id="72a4c-362">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="72a4c-363">`CourseViewModel`：</span><span class="sxs-lookup"><span data-stu-id="72a4c-363">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="72a4c-364">有关完整示例的信息，请参阅 [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) 和 [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-364">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="72a4c-365">创建显示“课程”和“注册”的“讲师”页</span><span class="sxs-lookup"><span data-stu-id="72a4c-365">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="72a4c-366">在本部分中，将创建“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="72a4c-366">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="72a4c-367">![“讲师索引”页](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="72a4c-367">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="72a4c-368">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="72a4c-368">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="72a4c-369">讲师列表显示 `OfficeAssignment` 实体（上图中的办公室）的相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-369">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="72a4c-370">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="72a4c-370">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="72a4c-371">预先加载适用于 `OfficeAssignment` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-371">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="72a4c-372">需要显示相关数据时，预先加载通常更高效。</span><span class="sxs-lookup"><span data-stu-id="72a4c-372">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="72a4c-373">在此情况下，会显示讲师的办公室分配。</span><span class="sxs-lookup"><span data-stu-id="72a4c-373">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="72a4c-374">当用户选择一名讲师（上图中的 Harui）时，显示相关的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-374">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="72a4c-375">`Instructor` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="72a4c-375">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="72a4c-376">对 `Course` 实体及其相关的 `Department` 实体使用预先加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-376">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="72a4c-377">这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="72a4c-377">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="72a4c-378">此示例演示如何在位于导航实体内的实体中预先加载这些导航实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-378">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="72a4c-379">当用户选择一门课程（上图中的化学）时，显示 `Enrollments` 实体的相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-379">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="72a4c-380">上图中显示了学生姓名和成绩。</span><span class="sxs-lookup"><span data-stu-id="72a4c-380">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="72a4c-381">`Course` 和 `Enrollment` 实体之间存在一对多的关系。</span><span class="sxs-lookup"><span data-stu-id="72a4c-381">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="72a4c-382">创建“讲师索引”视图的视图模型</span><span class="sxs-lookup"><span data-stu-id="72a4c-382">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="72a4c-383">“讲师”页显示来自三个不同表格的数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-383">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="72a4c-384">创建一个视图模型，该模型中包含表示三个表格的三个实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-384">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="72a4c-385">在 SchoolViewModels  文件夹中，使用以下代码创建 InstructorIndexData.cs  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-385">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="72a4c-386">为讲师模型创建基架</span><span class="sxs-lookup"><span data-stu-id="72a4c-386">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72a4c-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72a4c-387">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="72a4c-388">按照[为“学生”模型搭建基架](xref:data/ef-rp/intro#scaffold-student-pages)中的说明操作，并对模型类使用 `Instructor`。</span><span class="sxs-lookup"><span data-stu-id="72a4c-388">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="72a4c-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="72a4c-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="72a4c-390">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="72a4c-390">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="72a4c-391">上述命令为 `Instructor` 模型创建基架。</span><span class="sxs-lookup"><span data-stu-id="72a4c-391">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="72a4c-392">运行应用并导航到“讲师”页。</span><span class="sxs-lookup"><span data-stu-id="72a4c-392">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="72a4c-393">将 Pages/Instructors/Index.cshtml.cs  替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="72a4c-393">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="72a4c-394">`OnGetAsync` 方法接受所选讲师 ID 的可选路由数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-394">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="72a4c-395">检查 Pages/Instructors/Index.cshtml.cs 文件中的查询  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-395">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="72a4c-396">查询包括两项内容：</span><span class="sxs-lookup"><span data-stu-id="72a4c-396">The query has two includes:</span></span>

* <span data-ttu-id="72a4c-397">`OfficeAssignment`：在[讲师视图](#IP)中显示。</span><span class="sxs-lookup"><span data-stu-id="72a4c-397">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="72a4c-398">`CourseAssignments`：课程的教学内容。</span><span class="sxs-lookup"><span data-stu-id="72a4c-398">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="72a4c-399">更新“讲师索引”页</span><span class="sxs-lookup"><span data-stu-id="72a4c-399">Update the instructors Index page</span></span>

<span data-ttu-id="72a4c-400">使用以下标记更新 Pages/Instructors/Index.cshtml  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-400">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="72a4c-401">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="72a4c-401">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="72a4c-402">将 `page` 指令从 `@page` 更新为 `@page "{id:int?}"`。</span><span class="sxs-lookup"><span data-stu-id="72a4c-402">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="72a4c-403">`"{id:int?}"` 是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="72a4c-403">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="72a4c-404">路由模板将 URL 中的整数查询字符串更改为路由数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-404">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="72a4c-405">例如，单击仅具有 `@page` 指令的讲师的“选择”链接将生成如下 URL  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-405">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="72a4c-406">当页面指令是 `@page "{id:int?}"` 时，之前的 URL 为：</span><span class="sxs-lookup"><span data-stu-id="72a4c-406">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="72a4c-407">页标题为“讲师”  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-407">Page title is **Instructors**.</span></span>
* <span data-ttu-id="72a4c-408">添加了仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-408">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="72a4c-409">由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="72a4c-410">添加了显示每位讲师所授课程的“课程”列  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-410">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="72a4c-411">有关此 Razor 语法的详细信息，请参阅[使用 `@:` 的显式行转换](xref:mvc/views/razor#explicit-line-transition-with-)。</span><span class="sxs-lookup"><span data-stu-id="72a4c-411">See [Explicit line transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="72a4c-412">添加了向所选讲师的 `tr` 元素中动态添加 `class="success"` 的代码。</span><span class="sxs-lookup"><span data-stu-id="72a4c-412">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="72a4c-413">此时会使用 Bootstrap 类为所选行设置背景色。</span><span class="sxs-lookup"><span data-stu-id="72a4c-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="72a4c-414">添加了标记为“选择”的新的超链接  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-414">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="72a4c-415">该链接将所选讲师的 ID 发送给 `Index` 方法并设置背景色。</span><span class="sxs-lookup"><span data-stu-id="72a4c-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="72a4c-416">运行应用并选择“讲师”选项卡  。该页显示来自相关 `OfficeAssignment` 实体的 `Location`（办公室）。</span><span class="sxs-lookup"><span data-stu-id="72a4c-416">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="72a4c-417">如果 OfficeAssignment\` 为 NULL，则显示空白表格单元格。</span><span class="sxs-lookup"><span data-stu-id="72a4c-417">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="72a4c-418">单击“选择”  链接。</span><span class="sxs-lookup"><span data-stu-id="72a4c-418">Click on the **Select** link.</span></span> <span data-ttu-id="72a4c-419">随即更改行样式。</span><span class="sxs-lookup"><span data-stu-id="72a4c-419">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="72a4c-420">添加由所选讲师教授的课程</span><span class="sxs-lookup"><span data-stu-id="72a4c-420">Add courses taught by selected instructor</span></span>

<span data-ttu-id="72a4c-421">将 Pages/Instructors/Index.cshtml.cs  中的 `OnGetAsync` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="72a4c-421">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="72a4c-422">添加 `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="72a4c-422">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="72a4c-423">检查更新后的查询：</span><span class="sxs-lookup"><span data-stu-id="72a4c-423">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="72a4c-424">先前查询添加了 `Department` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-424">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="72a4c-425">选择讲师时 (`id != null`)，将执行以下代码。</span><span class="sxs-lookup"><span data-stu-id="72a4c-425">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="72a4c-426">从视图模型中的讲师列表检索所选讲师。</span><span class="sxs-lookup"><span data-stu-id="72a4c-426">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="72a4c-427">向视图模型的 `Courses` 属性加载来自讲师 `CourseAssignments` 导航属性的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-427">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="72a4c-428">`Where` 方法返回一个集合。</span><span class="sxs-lookup"><span data-stu-id="72a4c-428">The `Where` method returns a collection.</span></span> <span data-ttu-id="72a4c-429">在前面的 `Where` 方法中，仅返回单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-429">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="72a4c-430">`Single` 方法将集合转换为单个 `Instructor` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-430">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="72a4c-431">`Instructor` 实体提供对 `CourseAssignments` 属性的访问。</span><span class="sxs-lookup"><span data-stu-id="72a4c-431">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="72a4c-432">`CourseAssignments` 提供对相关 `Course` 实体的访问。</span><span class="sxs-lookup"><span data-stu-id="72a4c-432">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![讲师-课程 m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="72a4c-434">当集合仅包含一个项时，集合使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="72a4c-434">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="72a4c-435">如果集合为空或包含多个项，`Single` 方法会引发异常。</span><span class="sxs-lookup"><span data-stu-id="72a4c-435">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="72a4c-436">还可使用 `SingleOrDefault`，该方式在集合为空时返回默认值（本例中为 null）。</span><span class="sxs-lookup"><span data-stu-id="72a4c-436">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="72a4c-437">在空集合上使用 `SingleOrDefault`：</span><span class="sxs-lookup"><span data-stu-id="72a4c-437">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="72a4c-438">引发异常（因为尝试在空引用上找到 `Courses` 属性）。</span><span class="sxs-lookup"><span data-stu-id="72a4c-438">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="72a4c-439">异常信息不太能清楚指出问题原因。</span><span class="sxs-lookup"><span data-stu-id="72a4c-439">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="72a4c-440">选中课程时，视图模型的 `Enrollments` 属性将填充以下代码：</span><span class="sxs-lookup"><span data-stu-id="72a4c-440">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="72a4c-441">在 Pages/Instructors/Index.cshtml Razor 页面末尾添加以下标记  ：</span><span class="sxs-lookup"><span data-stu-id="72a4c-441">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="72a4c-442">上述标记显示选中某讲师时与该讲师相关的课程列表。</span><span class="sxs-lookup"><span data-stu-id="72a4c-442">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="72a4c-443">测试应用。</span><span class="sxs-lookup"><span data-stu-id="72a4c-443">Test the app.</span></span> <span data-ttu-id="72a4c-444">单击讲师页面上的“选择”  链接。</span><span class="sxs-lookup"><span data-stu-id="72a4c-444">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="72a4c-445">显示学生数据</span><span class="sxs-lookup"><span data-stu-id="72a4c-445">Show student data</span></span>

<span data-ttu-id="72a4c-446">在本部分中，更新应用以显示所选课程的学生数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-446">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="72a4c-447">使用以下代码在 Pages/Instructors/Index.cshtml.cs  中更新 `OnGetAsync` 方法中的查询：</span><span class="sxs-lookup"><span data-stu-id="72a4c-447">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="72a4c-448">更新 Pages/Instructors/Index.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-448">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="72a4c-449">在文件末尾添加以下标记：</span><span class="sxs-lookup"><span data-stu-id="72a4c-449">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="72a4c-450">上述标记显示已注册所选课程的学生列表。</span><span class="sxs-lookup"><span data-stu-id="72a4c-450">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="72a4c-451">刷新页面并选择讲师。</span><span class="sxs-lookup"><span data-stu-id="72a4c-451">Refresh the page and select an instructor.</span></span> <span data-ttu-id="72a4c-452">选择一门课程，查看已注册的学生及其成绩列表。</span><span class="sxs-lookup"><span data-stu-id="72a4c-452">Select a course to see the list of enrolled students and their grades.</span></span>

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="72a4c-454">使用 Single 方法</span><span class="sxs-lookup"><span data-stu-id="72a4c-454">Using Single</span></span>

<span data-ttu-id="72a4c-455">`Single` 方法可在 `Where` 条件中进行传递，无需分别调用 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="72a4c-455">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="72a4c-456">使用 `Where` 时，前面的 `Single` 方法不适用。</span><span class="sxs-lookup"><span data-stu-id="72a4c-456">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="72a4c-457">一些开发人员更喜欢 `Single` 方法样式。</span><span class="sxs-lookup"><span data-stu-id="72a4c-457">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="72a4c-458">显式加载</span><span class="sxs-lookup"><span data-stu-id="72a4c-458">Explicit loading</span></span>

<span data-ttu-id="72a4c-459">当前代码为 `Enrollments` 和 `Students` 指定预先加载：</span><span class="sxs-lookup"><span data-stu-id="72a4c-459">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="72a4c-460">假设用户几乎不希望课程中显示注册情况。</span><span class="sxs-lookup"><span data-stu-id="72a4c-460">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="72a4c-461">在此情况下，可仅在请求时加载注册数据进行优化。</span><span class="sxs-lookup"><span data-stu-id="72a4c-461">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="72a4c-462">在本部分中，会更新 `OnGetAsync` 以使用 `Enrollments` 和 `Students` 的显式加载。</span><span class="sxs-lookup"><span data-stu-id="72a4c-462">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="72a4c-463">使用以下代码更新 `OnGetAsync`：</span><span class="sxs-lookup"><span data-stu-id="72a4c-463">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="72a4c-464">上述代码取消针对注册和学生数据的 ThenInclude 方法调用  。</span><span class="sxs-lookup"><span data-stu-id="72a4c-464">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="72a4c-465">如果已选中课程，则突出显示的代码会检索：</span><span class="sxs-lookup"><span data-stu-id="72a4c-465">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="72a4c-466">所选课程的 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-466">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="72a4c-467">每个 `Enrollment` 的 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="72a4c-467">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="72a4c-468">请注意，上述代码为 `.AsNoTracking()` 加上注释。</span><span class="sxs-lookup"><span data-stu-id="72a4c-468">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="72a4c-469">对于跟踪的实体，仅可显式加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="72a4c-469">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="72a4c-470">测试应用。</span><span class="sxs-lookup"><span data-stu-id="72a4c-470">Test the app.</span></span> <span data-ttu-id="72a4c-471">对用户而言，该应用的行为与上一版本相同。</span><span class="sxs-lookup"><span data-stu-id="72a4c-471">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="72a4c-472">下一个教程将介绍如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="72a4c-472">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72a4c-473">其他资源</span><span class="sxs-lookup"><span data-stu-id="72a4c-473">Additional resources</span></span>

* [<span data-ttu-id="72a4c-474">本教程的 YouTube 版本（第 1 部分）</span><span class="sxs-lookup"><span data-stu-id="72a4c-474">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="72a4c-475">本教程的 YouTube 版本（第 2 部分）</span><span class="sxs-lookup"><span data-stu-id="72a4c-475">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="72a4c-476">[上一页](xref:data/ef-rp/complex-data-model)
>[下一页](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="72a4c-476">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end