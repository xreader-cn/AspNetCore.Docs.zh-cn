---
title: ASP.NET Core 中的 Razor 页面和 EF Core - CRUD - 第 2 个教程（共 8 个）
author: rick-anderson
description: 演示如何使用 EF Core 进行创建、读取、更新和删除
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/crud
ms.openlocfilehash: ec22c82b3b70399017b5b9aa61896440db647062
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750019"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a><span data-ttu-id="fad85-103">ASP.NET Core 中的 Razor 页面和 EF Core - CRUD - 第 2 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="fad85-103">Razor Pages with EF Core in ASP.NET Core - CRUD - 2 of 8</span></span>

<span data-ttu-id="fad85-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fad85-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="fad85-105">本教程将介绍和自定义已搭建基架的 CRUD （创建、读取、更新、删除）代码。</span><span class="sxs-lookup"><span data-stu-id="fad85-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="fad85-106">为最大程度降低复杂性并让这些教程集中介绍 EF Core，将在页面模型中使用 EF Core 代码。</span><span class="sxs-lookup"><span data-stu-id="fad85-106">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="fad85-107">某些开发人员使用服务层或存储库模式在 UI（Razor 页面）和数据访问层之间创建抽象层。</span><span class="sxs-lookup"><span data-stu-id="fad85-107">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="fad85-108">本教程将检查“学生”文件夹中的“创建”、“编辑”、“删除”和“详细信息”Razor Pages  。</span><span class="sxs-lookup"><span data-stu-id="fad85-108">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="fad85-109">基架代码将以下模式用于“创建”、“编辑”和“删除”页面：</span><span class="sxs-lookup"><span data-stu-id="fad85-109">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="fad85-110">使用 HTTP GET 方法 `OnGetAsync` 获取和显示请求数据。</span><span class="sxs-lookup"><span data-stu-id="fad85-110">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="fad85-111">使用 HTTP POST 方法 `OnPostAsync` 将更改保存到数据。</span><span class="sxs-lookup"><span data-stu-id="fad85-111">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="fad85-112">“索引”和“详细信息”页面使用 HTTP GET 方法 `OnGetAsync` 获取和显示请求数据</span><span class="sxs-lookup"><span data-stu-id="fad85-112">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="fad85-113">SingleOrDefaultAsync 与FirstOrDefaultAsync</span><span class="sxs-lookup"><span data-stu-id="fad85-113">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="fad85-114">生成的代码使用 [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_)其推荐度通常高于 [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。</span><span class="sxs-lookup"><span data-stu-id="fad85-114">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="fad85-115">提取一个实体时，使用 `FirstOrDefaultAsync` 比使用 `SingleOrDefaultAsync` 更高效：</span><span class="sxs-lookup"><span data-stu-id="fad85-115">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="fad85-116">代码需要验证查询仅返回一个实体时除外。</span><span class="sxs-lookup"><span data-stu-id="fad85-116">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="fad85-117">`SingleOrDefaultAsync` 会提取更多数据并执行不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="fad85-117">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="fad85-118">如果有多个实体符合筛选部分，`SingleOrDefaultAsync` 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="fad85-118">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="fad85-119">如果有多个实体符合筛选部分，`FirstOrDefaultAsync` 不引发异常。</span><span class="sxs-lookup"><span data-stu-id="fad85-119">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="fad85-120">FindAsync</span><span class="sxs-lookup"><span data-stu-id="fad85-120">FindAsync</span></span>

<span data-ttu-id="fad85-121">在大部分基架代码中，[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) 可用于替代 `FirstOrDefaultAsync`。</span><span class="sxs-lookup"><span data-stu-id="fad85-121">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="fad85-122">`FindAsync`：</span><span class="sxs-lookup"><span data-stu-id="fad85-122">`FindAsync`:</span></span>

* <span data-ttu-id="fad85-123">查找具有主键 (PK) 的实体。</span><span class="sxs-lookup"><span data-stu-id="fad85-123">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="fad85-124">如果具有 PK 的实体正在由上下文跟踪，会返回该实体且不向 DB 发出请求。</span><span class="sxs-lookup"><span data-stu-id="fad85-124">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="fad85-125">既简单又简洁。</span><span class="sxs-lookup"><span data-stu-id="fad85-125">Is simple and concise.</span></span>
* <span data-ttu-id="fad85-126">经过优化后可查找单个实体。</span><span class="sxs-lookup"><span data-stu-id="fad85-126">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="fad85-127">在某些情况下可以提供性能优势，但很少发生在典型的 Web 应用中。</span><span class="sxs-lookup"><span data-stu-id="fad85-127">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="fad85-128">以隐式方式使用 [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) 而不是 [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_)。</span><span class="sxs-lookup"><span data-stu-id="fad85-128">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="fad85-129">如果想要 `Include` 其他实体，则 `FindAsync` 将不再适用。</span><span class="sxs-lookup"><span data-stu-id="fad85-129">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="fad85-130">这意味着可能需要放弃 `FindAsync` 并随着应用运行移动到查询。</span><span class="sxs-lookup"><span data-stu-id="fad85-130">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="fad85-131">自定义“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="fad85-131">Customize the Details page</span></span>

<span data-ttu-id="fad85-132">浏览到 `Pages/Students` 页面。</span><span class="sxs-lookup"><span data-stu-id="fad85-132">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="fad85-133">“编辑”、“详细信息”和“删除”链接是在 Pages/Students/Index.cshtml 文件中由[定位点标记帮助器](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)生成的     。</span><span class="sxs-lookup"><span data-stu-id="fad85-133">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="fad85-134">运行应用并选择“详细信息”链接  。</span><span class="sxs-lookup"><span data-stu-id="fad85-134">Run the app and select a **Details** link.</span></span> <span data-ttu-id="fad85-135">URL 的格式为 `http://localhost:5000/Students/Details?id=2`。</span><span class="sxs-lookup"><span data-stu-id="fad85-135">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="fad85-136">“学生 ID”通过查询字符串 (`?id=2`) 进行传递。</span><span class="sxs-lookup"><span data-stu-id="fad85-136">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="fad85-137">更新“编辑”、“详细信息”和“删除”Razor 页面以使用 `"{id:int}"` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="fad85-137">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="fad85-138">将上述每个页面的页面指令从 `@page` 更改为 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="fad85-138">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="fad85-139">如果对具有不包含整数路由值的“{id:int}”路由模板的页面发起请求，则该请求将返回 HTTP 404（找不到）错误  。</span><span class="sxs-lookup"><span data-stu-id="fad85-139">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="fad85-140">例如，`http://localhost:5000/Students/Details` 返回 404 错误。</span><span class="sxs-lookup"><span data-stu-id="fad85-140">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="fad85-141">若要使 ID 可选，请将 `?` 追加到路由约束：</span><span class="sxs-lookup"><span data-stu-id="fad85-141">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="fad85-142">运行应用，单击“详细信息”链接，并验证确认 URL 正在将 ID 作为路由数据 (`http://localhost:5000/Students/Details/2`) 进行传递。</span><span class="sxs-lookup"><span data-stu-id="fad85-142">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="fad85-143">不要将 `@page` 全局更改为 `@page "{id:int}"`，执行此操作会将链接拆分为“主页”和“创建”页。</span><span class="sxs-lookup"><span data-stu-id="fad85-143">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="fad85-144">添加相关数据</span><span class="sxs-lookup"><span data-stu-id="fad85-144">Add related data</span></span>

<span data-ttu-id="fad85-145">“学生索引”页的基架代码不包括 `Enrollments` 属性。</span><span class="sxs-lookup"><span data-stu-id="fad85-145">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="fad85-146">在本部分，`Enrollments` 集合的内容显示在“详细信息”页中。</span><span class="sxs-lookup"><span data-stu-id="fad85-146">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="fad85-147">Pages/Students/Details.cshtml.cs 的 `OnGetAsync` 方法使用 `FirstOrDefaultAsync` 方法检索单个 `Student` 实体  。</span><span class="sxs-lookup"><span data-stu-id="fad85-147">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="fad85-148">添加以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="fad85-148">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="fad85-149">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) 和 [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) 方法使上下文加载 `Student.Enrollments` 导航属性，并在每个注册中加载 `Enrollment.Course` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="fad85-149">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="fad85-150">这些方法将在与数据读取相关的教程中进行详细介绍。</span><span class="sxs-lookup"><span data-stu-id="fad85-150">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="fad85-151">对于返回的实体未在当前上下文中更新的情况，[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) 方法将会提升性能。</span><span class="sxs-lookup"><span data-stu-id="fad85-151">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="fad85-152">`AsNoTracking` 将在本教程的后续部分中讨论。</span><span class="sxs-lookup"><span data-stu-id="fad85-152">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="fad85-153">在“详细信息”页中显示相关注册</span><span class="sxs-lookup"><span data-stu-id="fad85-153">Display related enrollments on the Details page</span></span>

<span data-ttu-id="fad85-154">打开 Pages/Students/Details.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="fad85-154">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="fad85-155">添加以下突出显示的代码以显示注册列表：</span><span class="sxs-lookup"><span data-stu-id="fad85-155">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="fad85-156">如果代码缩进在粘贴代码后出现错误，请按 CTRL-K-D 进行更正。</span><span class="sxs-lookup"><span data-stu-id="fad85-156">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="fad85-157">上面的代码循环通过 `Enrollments` 导航属性中的实体。</span><span class="sxs-lookup"><span data-stu-id="fad85-157">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="fad85-158">它将针对每个注册显示课程标题和成绩。</span><span class="sxs-lookup"><span data-stu-id="fad85-158">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="fad85-159">课程标题从 Course 实体中检索，该实体存储在 Enrollments 实体的 `Course` 导航属性中。</span><span class="sxs-lookup"><span data-stu-id="fad85-159">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="fad85-160">运行应用，选择“学生”选项卡，然后单击学生的“详细信息”链接   。</span><span class="sxs-lookup"><span data-stu-id="fad85-160">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="fad85-161">随即显示出所选学生的课程和成绩列表。</span><span class="sxs-lookup"><span data-stu-id="fad85-161">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="fad85-162">更新“创建”页</span><span class="sxs-lookup"><span data-stu-id="fad85-162">Update the Create page</span></span>

<span data-ttu-id="fad85-163">将 Pages/Students/Create.cshtml.cs 中的 `OnPostAsync` 方法更新为以下代码  ：</span><span class="sxs-lookup"><span data-stu-id="fad85-163">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="fad85-164">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="fad85-164">TryUpdateModelAsync</span></span>

<span data-ttu-id="fad85-165">检查 [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) 代码：</span><span class="sxs-lookup"><span data-stu-id="fad85-165">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="fad85-166">在前面的代码中，`TryUpdateModelAsync<Student>` 尝试使用 [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 的 [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) 属性中已发布的表单值更新 `emptyStudent` 对象。</span><span class="sxs-lookup"><span data-stu-id="fad85-166">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="fad85-167">`TryUpdateModelAsync` 仅更新列出的属性 (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="fad85-167">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="fad85-168">在上述示例中：</span><span class="sxs-lookup"><span data-stu-id="fad85-168">In the preceding sample:</span></span>

* <span data-ttu-id="fad85-169">第二个自变量 (`"student", // Prefix`) 是用于查找值的前缀。</span><span class="sxs-lookup"><span data-stu-id="fad85-169">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="fad85-170">该自变量不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="fad85-170">It's not case sensitive.</span></span>
* <span data-ttu-id="fad85-171">已发布的表单值通过[模型绑定](xref:mvc/models/model-binding)转换为 `Student` 模型中的类型。</span><span class="sxs-lookup"><span data-stu-id="fad85-171">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="fad85-172">过多发布</span><span class="sxs-lookup"><span data-stu-id="fad85-172">Overposting</span></span>

<span data-ttu-id="fad85-173">使用 `TryUpdateModel` 更新具有已发布值的字段是一种最佳的安全做法，因为这能阻止过多发布。</span><span class="sxs-lookup"><span data-stu-id="fad85-173">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="fad85-174">例如，假设 Student 实体包含此网页不应更新或添加的 `Secret` 属性：</span><span class="sxs-lookup"><span data-stu-id="fad85-174">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="fad85-175">即使应用的创建/更新 Razor 页面上没有 `Secret` 字段，黑客仍可利用过多发布设置 `Secret` 值。</span><span class="sxs-lookup"><span data-stu-id="fad85-175">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="fad85-176">黑客也可使用 Fiddler 等工具或通过编写某个 JavaScript 来发布 `Secret` 表单值。</span><span class="sxs-lookup"><span data-stu-id="fad85-176">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="fad85-177">原始代码不会限制模型绑定器在创建“学生”实例时使用的字段。</span><span class="sxs-lookup"><span data-stu-id="fad85-177">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="fad85-178">黑客为 `Secret` 表单字段指定的任何值都会在 DB 中更新。</span><span class="sxs-lookup"><span data-stu-id="fad85-178">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="fad85-179">下图显示 Fiddler 工具正在将 `Secret` 字段（值为“OverPost”）添加到已发布的表单值。</span><span class="sxs-lookup"><span data-stu-id="fad85-179">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler 添加 Secret 字段](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="fad85-181">值“OverPost”已成功添加到所插入行的 `Secret` 属性中。</span><span class="sxs-lookup"><span data-stu-id="fad85-181">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="fad85-182">应用程序设计器绝不会在“创建”页设置 `Secret` 属性。</span><span class="sxs-lookup"><span data-stu-id="fad85-182">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="fad85-183">视图模型</span><span class="sxs-lookup"><span data-stu-id="fad85-183">View model</span></span>

<span data-ttu-id="fad85-184">视图模型通常包含应用程序所用的模型中包括的属性的子集。</span><span class="sxs-lookup"><span data-stu-id="fad85-184">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="fad85-185">应用程序模型通常称为域模型。</span><span class="sxs-lookup"><span data-stu-id="fad85-185">The application model is often called the domain model.</span></span> <span data-ttu-id="fad85-186">域模型通常包含 DB 中对应实体所需的全部属性。</span><span class="sxs-lookup"><span data-stu-id="fad85-186">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="fad85-187">视图模型仅包含 UI 层（例如“创建”页）所需的属性。</span><span class="sxs-lookup"><span data-stu-id="fad85-187">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="fad85-188">除视图模型外，某些应用使用绑定模型或输入模型在“Razor 页面”页面模型类和浏览器之间传递数据。</span><span class="sxs-lookup"><span data-stu-id="fad85-188">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="fad85-189">请考虑以下 `Student` 视图模型：</span><span class="sxs-lookup"><span data-stu-id="fad85-189">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="fad85-190">视图模型还提供了一种防止过度发布的方法。</span><span class="sxs-lookup"><span data-stu-id="fad85-190">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="fad85-191">视图模型仅包含要查看（显示）或更新的属性。</span><span class="sxs-lookup"><span data-stu-id="fad85-191">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="fad85-192">以下代码使用 `StudentVM` 视图模型创建新的学生：</span><span class="sxs-lookup"><span data-stu-id="fad85-192">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="fad85-193">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) 方法通过从另一个 [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) 对象读取值来设置此对象的值。</span><span class="sxs-lookup"><span data-stu-id="fad85-193">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="fad85-194">`SetValues` 使用属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="fad85-194">`SetValues` uses property name matching.</span></span> <span data-ttu-id="fad85-195">视图模型类型不需要与模型类型相关，它只需要具有匹配的属性。</span><span class="sxs-lookup"><span data-stu-id="fad85-195">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="fad85-196">使用 `StudentVM` 时需要更新 [CreateVM.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) 才能使用 `StudentVM` 而非 `Student`。</span><span class="sxs-lookup"><span data-stu-id="fad85-196">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="fad85-197">在 Razor 页面，`PageModel` 派生类就是视图模型。</span><span class="sxs-lookup"><span data-stu-id="fad85-197">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="fad85-198">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="fad85-198">Update the Edit page</span></span>

<span data-ttu-id="fad85-199">更新“编辑”页的页面模型。</span><span class="sxs-lookup"><span data-stu-id="fad85-199">Update the page model for the Edit page.</span></span> <span data-ttu-id="fad85-200">突出显示所作的主要更改：</span><span class="sxs-lookup"><span data-stu-id="fad85-200">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="fad85-201">代码更改与“创建”页类似，但有少数例外：</span><span class="sxs-lookup"><span data-stu-id="fad85-201">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="fad85-202">`OnPostAsync` 具有可选的 `id` 参数。</span><span class="sxs-lookup"><span data-stu-id="fad85-202">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="fad85-203">当前学生是从 DB 提取的，而非通过创建空学生获得。</span><span class="sxs-lookup"><span data-stu-id="fad85-203">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="fad85-204">已将 `FirstOrDefaultAsync` 替换为 [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync)。</span><span class="sxs-lookup"><span data-stu-id="fad85-204">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="fad85-205">从主键中选择实体时，使用 `FindAsync` 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="fad85-205">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="fad85-206">请参阅 [FindAsync](#FindAsync) 了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="fad85-206">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="fad85-207">测试“编辑”和“创建”页</span><span class="sxs-lookup"><span data-stu-id="fad85-207">Test the Edit and Create pages</span></span>

<span data-ttu-id="fad85-208">创建和编辑几个学生实体。</span><span class="sxs-lookup"><span data-stu-id="fad85-208">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="fad85-209">实体状态</span><span class="sxs-lookup"><span data-stu-id="fad85-209">Entity States</span></span>

<span data-ttu-id="fad85-210">DB 上下文会随时跟踪内存中的实体是否已与其在 DB 中的对应行进行同步。</span><span class="sxs-lookup"><span data-stu-id="fad85-210">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="fad85-211">DB 上下文同步信息可决定调用 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 后的行为。</span><span class="sxs-lookup"><span data-stu-id="fad85-211">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="fad85-212">例如，将新实体传递到 [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) 方法时，该实体的状态设置为 [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added)。</span><span class="sxs-lookup"><span data-stu-id="fad85-212">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="fad85-213">调用 `SaveChangesAsync` 时，DB 上下文会发出 SQL INSERT 命令。</span><span class="sxs-lookup"><span data-stu-id="fad85-213">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="fad85-214">实体可能处于[以下状态](/dotnet/api/microsoft.entityframeworkcore.entitystate)之一：</span><span class="sxs-lookup"><span data-stu-id="fad85-214">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="fad85-215">`Added`：DB 中尚不存在实体。</span><span class="sxs-lookup"><span data-stu-id="fad85-215">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="fad85-216">`SaveChanges` 方法发出 INSERT 语句。</span><span class="sxs-lookup"><span data-stu-id="fad85-216">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="fad85-217">`Unchanged`：无需保存对该实体所做的任何更改。</span><span class="sxs-lookup"><span data-stu-id="fad85-217">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="fad85-218">从 DB 中读取实体时，该实体将具有此状态。</span><span class="sxs-lookup"><span data-stu-id="fad85-218">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="fad85-219">`Modified`：已修改实体的部分或全部属性值。</span><span class="sxs-lookup"><span data-stu-id="fad85-219">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="fad85-220">`SaveChanges` 方法发出 UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="fad85-220">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="fad85-221">`Deleted`：已标记该实体进行删除。</span><span class="sxs-lookup"><span data-stu-id="fad85-221">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="fad85-222">`SaveChanges` 方法发出 DELETE 语句。</span><span class="sxs-lookup"><span data-stu-id="fad85-222">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="fad85-223">`Detached`：DB 上下文未跟踪该实体。</span><span class="sxs-lookup"><span data-stu-id="fad85-223">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="fad85-224">在桌面应用中，通常会自动设置状态更改。</span><span class="sxs-lookup"><span data-stu-id="fad85-224">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="fad85-225">读取实体并执行更改后，实体状态自动更改为 `Modified`。</span><span class="sxs-lookup"><span data-stu-id="fad85-225">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="fad85-226">调用 `SaveChanges` 会生成仅更新已更改属性的 SQL UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="fad85-226">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="fad85-227">在 Web 应用中，读取实体并显示数据的 `DbContext` 将在页面呈现后进行处理。</span><span class="sxs-lookup"><span data-stu-id="fad85-227">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="fad85-228">调用页面 `OnPostAsync` 方法时，将发出具有 `DbContext` 的新实例的 Web 请求。</span><span class="sxs-lookup"><span data-stu-id="fad85-228">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="fad85-229">如果在这个新的上下文中重新读取实体，则会模拟桌面处理。</span><span class="sxs-lookup"><span data-stu-id="fad85-229">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="fad85-230">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="fad85-230">Update the Delete page</span></span>

<span data-ttu-id="fad85-231">在此部分中，当对 `SaveChanges` 的调用失败时，将添加用于实现自定义错误消息的代码。</span><span class="sxs-lookup"><span data-stu-id="fad85-231">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="fad85-232">添加字符串，使其包含可能的错误消息：</span><span class="sxs-lookup"><span data-stu-id="fad85-232">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="fad85-233">将 `OnGetAsync` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fad85-233">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="fad85-234">上述代码包含可选参数 `saveChangesError`。</span><span class="sxs-lookup"><span data-stu-id="fad85-234">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="fad85-235">`saveChangesError` 指示学生对象删除失败后是否调用该方法。</span><span class="sxs-lookup"><span data-stu-id="fad85-235">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="fad85-236">删除操作可能由于暂时性网络问题而失败。</span><span class="sxs-lookup"><span data-stu-id="fad85-236">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="fad85-237">云端更可能出现暂时性网络错误。</span><span class="sxs-lookup"><span data-stu-id="fad85-237">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="fad85-238">通过 UI 调用“删除”页 `OnGetAsync` 时，`saveChangesError` 为 false。</span><span class="sxs-lookup"><span data-stu-id="fad85-238">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="fad85-239">当 `OnPostAsync` 调用 `OnGetAsync`（由于删除操作失败）时，`saveChangesError` 参数为 true。</span><span class="sxs-lookup"><span data-stu-id="fad85-239">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="fad85-240">“删除”页 OnPostAsync 方法</span><span class="sxs-lookup"><span data-stu-id="fad85-240">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="fad85-241">将 `OnPostAsync` 替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fad85-241">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="fad85-242">上述代码检索所选的实体，然后调用 [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) 方法，将实体的状态设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="fad85-242">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="fad85-243">调用 `SaveChanges` 时生成 SQL DELETE 命令。</span><span class="sxs-lookup"><span data-stu-id="fad85-243">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="fad85-244">如果 `Remove` 失败：</span><span class="sxs-lookup"><span data-stu-id="fad85-244">If `Remove` fails:</span></span>

* <span data-ttu-id="fad85-245">会捕获 DB 异常。</span><span class="sxs-lookup"><span data-stu-id="fad85-245">The DB exception is caught.</span></span>
* <span data-ttu-id="fad85-246">通过 `saveChangesError=true` 调用“删除”页 `OnGetAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="fad85-246">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="fad85-247">更新“删除”Razor 页面</span><span class="sxs-lookup"><span data-stu-id="fad85-247">Update the Delete Razor Page</span></span>

<span data-ttu-id="fad85-248">将以下突出显示的错误消息添加到“删除”Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="fad85-248">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="fad85-249">测试“删除”。</span><span class="sxs-lookup"><span data-stu-id="fad85-249">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="fad85-250">常见错误</span><span class="sxs-lookup"><span data-stu-id="fad85-250">Common errors</span></span>

<span data-ttu-id="fad85-251">“学生/索引”或其他链接不起作用：</span><span class="sxs-lookup"><span data-stu-id="fad85-251">Students/Index or other links don't work:</span></span>

<span data-ttu-id="fad85-252">验证确认 Razor 页面包含正确的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="fad85-252">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="fad85-253">例如，“学生/索引”Razor Pages 不得  包含路由模板：</span><span class="sxs-lookup"><span data-stu-id="fad85-253">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="fad85-254">每个 Razor 页面均必须包含 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="fad85-254">Each Razor Page must include the `@page` directive.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="fad85-255">其他资源</span><span class="sxs-lookup"><span data-stu-id="fad85-255">Additional resources</span></span>

* [<span data-ttu-id="fad85-256">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="fad85-256">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="fad85-257">[上一页](xref:data/ef-rp/intro)
> [下一页](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="fad85-257">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>
