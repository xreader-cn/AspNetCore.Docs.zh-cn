---
title: 第 6 部分，添加搜索
author: rick-anderson
description: Razor 页面教程系列第 6 部分。
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Index
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/search
ms.openlocfilehash: 1e571966b78f93e29e7901dd9648fbe3aca52726
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855477"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="b1df7-103">第 6 部分，将搜索添加到 ASP.NET Core Razor 页面</span><span class="sxs-lookup"><span data-stu-id="b1df7-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="b1df7-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b1df7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b1df7-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="b1df7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="b1df7-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="b1df7-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1df7-107">在以下部分中，添加了按流派或名称搜索电影。</span><span class="sxs-lookup"><span data-stu-id="b1df7-107">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="b1df7-108">将以下突出显示的 using 语句和属性添加到 Pages/Movies/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="b1df7-108">Add the following highlighted using statement and properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

<span data-ttu-id="b1df7-109">在前面的代码中：</span><span class="sxs-lookup"><span data-stu-id="b1df7-109">In the previous code:</span></span>

* <span data-ttu-id="b1df7-110">`SearchString`：包含用户在搜索文本框中输入的文本。</span><span class="sxs-lookup"><span data-stu-id="b1df7-110">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="b1df7-111">`SearchString` 也有 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="b1df7-111">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="b1df7-112">`[BindProperty]` 会绑定名称与属性相同的表单值和查询字符串。</span><span class="sxs-lookup"><span data-stu-id="b1df7-112">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="b1df7-113">在 HTTP GET 请求中进行绑定需要 `[BindProperty(SupportsGet = true)]`。</span><span class="sxs-lookup"><span data-stu-id="b1df7-113">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="b1df7-114">`Genres`：包含流派列表。</span><span class="sxs-lookup"><span data-stu-id="b1df7-114">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="b1df7-115">`Genres` 使用户能够从列表中选择一种流派。</span><span class="sxs-lookup"><span data-stu-id="b1df7-115">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="b1df7-116">`SelectList` 需要 `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="b1df7-116">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="b1df7-117">`MovieGenre`：包含用户选择的特定流派。</span><span class="sxs-lookup"><span data-stu-id="b1df7-117">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="b1df7-118">例如，“Western”。</span><span class="sxs-lookup"><span data-stu-id="b1df7-118">For example, "Western".</span></span>
* <span data-ttu-id="b1df7-119">`Genres` 和 `MovieGenre` 会在本教程的后续部分中使用。</span><span class="sxs-lookup"><span data-stu-id="b1df7-119">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="b1df7-120">使用以下代码更新 Index 页面的 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="b1df7-120">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="b1df7-121">`OnGetAsync` 方法的第一行创建了 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查询用于选择电影：</span><span class="sxs-lookup"><span data-stu-id="b1df7-121">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="b1df7-122">此时仅对查询进行了定义，它还 _不会_ 针对数据库运行。</span><span class="sxs-lookup"><span data-stu-id="b1df7-122">The query is only ***defined** _ at this point, it has _*_not_\*_ been run against the database.</span></span>

<span data-ttu-id="b1df7-123">如果 `SearchString` 属性不为 null 或空，则电影查询会修改为根据搜索字符串进行筛选：</span><span class="sxs-lookup"><span data-stu-id="b1df7-123">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="b1df7-124">`s => s.Title.Contains()` 代码是 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。</span><span class="sxs-lookup"><span data-stu-id="b1df7-124">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b1df7-125">Lambda 在基于方法的 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查询中用作标准查询运算符方法的参数，如 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 方法或 `Contains`。</span><span class="sxs-lookup"><span data-stu-id="b1df7-125">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="b1df7-126">在对 LINQ 查询进行定义或通过调用方法（如 `Where`、`Contains` 或 `OrderBy`）进行修改后，此查询不会执行。</span><span class="sxs-lookup"><span data-stu-id="b1df7-126">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="b1df7-127">相反，会延迟执行查询。</span><span class="sxs-lookup"><span data-stu-id="b1df7-127">Rather, query execution is deferred.</span></span> <span data-ttu-id="b1df7-128">表达式的计算会延迟，直到循环访问其实现的值或者调用 `ToListAsync` 方法为止。</span><span class="sxs-lookup"><span data-stu-id="b1df7-128">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="b1df7-129">有关详细信息，请参阅 [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution)（查询执行）。</span><span class="sxs-lookup"><span data-stu-id="b1df7-129">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b1df7-130">[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法在数据库中运行，而不是在 C# 代码中运行。</span><span class="sxs-lookup"><span data-stu-id="b1df7-130">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="b1df7-131">查询是否区分大小写取决于数据库和排序规则。</span><span class="sxs-lookup"><span data-stu-id="b1df7-131">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="b1df7-132">在 SQL Server 上，`Contains` 映射到 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，这是不区分大小写的。</span><span class="sxs-lookup"><span data-stu-id="b1df7-132">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="b1df7-133">在 SQLite 中，由于使用了默认排序规则，因此需要区分大小写。</span><span class="sxs-lookup"><span data-stu-id="b1df7-133">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="b1df7-134">导航到电影页面，并向 URL 追加一个如 `?searchString=Ghost` 的查询字符串。</span><span class="sxs-lookup"><span data-stu-id="b1df7-134">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="b1df7-135">例如 `https://localhost:5001/Movies?searchString=Ghost`。</span><span class="sxs-lookup"><span data-stu-id="b1df7-135">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="b1df7-136">筛选的电影将显示出来。</span><span class="sxs-lookup"><span data-stu-id="b1df7-136">The filtered movies are displayed.</span></span>

![Index 视图](search/_static/ghost.png)

<span data-ttu-id="b1df7-138">如果向 Index 页面添加了以下路由模板，搜索字符串则可作为 URL 段传递。</span><span class="sxs-lookup"><span data-stu-id="b1df7-138">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="b1df7-139">例如 `https://localhost:5001/Movies/Ghost`。</span><span class="sxs-lookup"><span data-stu-id="b1df7-139">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="b1df7-140">前面的路由约束允许按路由数据（URL 段）搜索标题，而不是按查询字符串值进行搜索。</span><span class="sxs-lookup"><span data-stu-id="b1df7-140">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="b1df7-141">`"{searchString?}"` 中的 `?` 表示这是可选路由参数。</span><span class="sxs-lookup"><span data-stu-id="b1df7-141">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Index 视图，显示添加到 URL 的“ghost”一词，以及返回的两部电影（Ghostbusters 和 Ghostbusters 2）的电影列表](search/_static/g2.png)

<span data-ttu-id="b1df7-143">ASP.NET Core 运行时使用[模型绑定](xref:mvc/models/model-binding)，通过查询字符串 (`?searchString=Ghost`) 或路由数据 (`https://localhost:5001/Movies/Ghost`) 设置 `SearchString` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="b1df7-143">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="b1df7-144">模型绑定 _不_ 区分大小写。</span><span class="sxs-lookup"><span data-stu-id="b1df7-144">Model binding is _*_not_*_ case sensitive.</span></span>

<span data-ttu-id="b1df7-145">但是，用户不能通过修改 URL 来搜索电影。</span><span class="sxs-lookup"><span data-stu-id="b1df7-145">However, users cannot be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="b1df7-146">在此步骤中，会添加 UI 来筛选电影。</span><span class="sxs-lookup"><span data-stu-id="b1df7-146">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="b1df7-147">如果已添加路由约束 `"{searchString?}"`，请将它删除。</span><span class="sxs-lookup"><span data-stu-id="b1df7-147">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="b1df7-148">打开 _Pages/Movies/Index.cshtml\* 文件，并添加以下代码中突出显示的标记：</span><span class="sxs-lookup"><span data-stu-id="b1df7-148">Open the _Pages/Movies/Index.cshtml\* file, and add the markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="b1df7-149">HTML `<form>` 标记使用以下[标记帮助程序](xref:mvc/views/tag-helpers/intro)：</span><span class="sxs-lookup"><span data-stu-id="b1df7-149">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="b1df7-150">[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="b1df7-150">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="b1df7-151">提交表单时，筛选器字符串将通过查询字符串发送到 Pages/Movies/Index 页面。</span><span class="sxs-lookup"><span data-stu-id="b1df7-151">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="b1df7-152">输入标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="b1df7-152">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="b1df7-153">保存更改并测试筛选器。</span><span class="sxs-lookup"><span data-stu-id="b1df7-153">Save the changes and test the filter.</span></span>

![显示标题筛选器文本框中键入了 ghost 一词的 Index 视图](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="b1df7-155">按流派搜索</span><span class="sxs-lookup"><span data-stu-id="b1df7-155">Search by genre</span></span>

<span data-ttu-id="b1df7-156">使用以下代码更新 Index 页面的 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="b1df7-156">Update the Index page's `OnGetAsync` method with the following code:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="b1df7-157">下面的代码是一种 LINQ 查询，可从数据库中检索所有流派。</span><span class="sxs-lookup"><span data-stu-id="b1df7-157">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="b1df7-158">流派的 `SelectList` 是通过投影不包含重复值的流派创建的。</span><span class="sxs-lookup"><span data-stu-id="b1df7-158">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="b1df7-159">将按流派搜索添加到 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="b1df7-159">Add search by genre to the Razor Page</span></span>

1. <span data-ttu-id="b1df7-160">更新 Index.cshtml [`<form>` 元素] (https://developer.mozilla.org/docs/Web/HTML/Element/form) ，如以下标记中突出显示：</span><span class="sxs-lookup"><span data-stu-id="b1df7-160">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. <span data-ttu-id="b1df7-161">通过按流派或/和电影标题搜索来测试应用。</span><span class="sxs-lookup"><span data-stu-id="b1df7-161">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1df7-162">其他资源</span><span class="sxs-lookup"><span data-stu-id="b1df7-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b1df7-163">[上一篇：更新页面](xref:tutorials/razor-pages/da1)
> [下一步：添加新字段](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="b1df7-163">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1df7-164">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="b1df7-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b1df7-165">在以下部分中，添加了按流派或名称搜索电影。</span><span class="sxs-lookup"><span data-stu-id="b1df7-165">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="b1df7-166">将以下突出显示的属性添加到 Pages/Movies/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="b1df7-166">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="b1df7-167">`SearchString`：包含用户在搜索文本框中输入的文本。</span><span class="sxs-lookup"><span data-stu-id="b1df7-167">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="b1df7-168">`SearchString` 也有 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="b1df7-168">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="b1df7-169">`[BindProperty]` 会绑定名称与属性相同的表单值和查询字符串。</span><span class="sxs-lookup"><span data-stu-id="b1df7-169">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="b1df7-170">在 HTTP GET 请求中进行绑定需要 `[BindProperty(SupportsGet = true)]`。</span><span class="sxs-lookup"><span data-stu-id="b1df7-170">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="b1df7-171">`Genres`：包含流派列表。</span><span class="sxs-lookup"><span data-stu-id="b1df7-171">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="b1df7-172">`Genres` 使用户能够从列表中选择一种流派。</span><span class="sxs-lookup"><span data-stu-id="b1df7-172">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="b1df7-173">`SelectList` 需要 `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="b1df7-173">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="b1df7-174">`MovieGenre`：包含用户选择的特定流派。</span><span class="sxs-lookup"><span data-stu-id="b1df7-174">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="b1df7-175">例如，“Western”。</span><span class="sxs-lookup"><span data-stu-id="b1df7-175">For example, "Western".</span></span>
* <span data-ttu-id="b1df7-176">`Genres` 和 `MovieGenre` 会在本教程的后续部分中使用。</span><span class="sxs-lookup"><span data-stu-id="b1df7-176">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="b1df7-177">使用以下代码更新 Index 页面的 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="b1df7-177">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="b1df7-178">`OnGetAsync` 方法的第一行创建了 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查询用于选择电影：</span><span class="sxs-lookup"><span data-stu-id="b1df7-178">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="b1df7-179">此时仅对查询进行了定义，它还不会针对数据库运行。</span><span class="sxs-lookup"><span data-stu-id="b1df7-179">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="b1df7-180">如果 `SearchString` 属性不为 null 或空，则电影查询会修改为根据搜索字符串进行筛选：</span><span class="sxs-lookup"><span data-stu-id="b1df7-180">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="b1df7-181">`s => s.Title.Contains()` 代码是 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。</span><span class="sxs-lookup"><span data-stu-id="b1df7-181">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b1df7-182">Lambda 在基于方法的 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查询中用作标准查询运算符方法的参数，如 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 方法或 `Contains`。</span><span class="sxs-lookup"><span data-stu-id="b1df7-182">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="b1df7-183">在对 LINQ 查询进行定义或通过调用方法（如 `Where`、`Contains` 或 `OrderBy`）进行修改后，此查询不会执行。</span><span class="sxs-lookup"><span data-stu-id="b1df7-183">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="b1df7-184">相反，会延迟执行查询。</span><span class="sxs-lookup"><span data-stu-id="b1df7-184">Rather, query execution is deferred.</span></span> <span data-ttu-id="b1df7-185">表达式的计算会延迟，直到循环访问其实现的值或者调用 `ToListAsync` 方法为止。</span><span class="sxs-lookup"><span data-stu-id="b1df7-185">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="b1df7-186">有关详细信息，请参阅 [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution)（查询执行）。</span><span class="sxs-lookup"><span data-stu-id="b1df7-186">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="b1df7-187">**注意：** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法在数据库中运行，而不是在 C# 代码中运行。</span><span class="sxs-lookup"><span data-stu-id="b1df7-187">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="b1df7-188">查询是否区分大小写取决于数据库和排序规则。</span><span class="sxs-lookup"><span data-stu-id="b1df7-188">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="b1df7-189">在 SQL Server 上，`Contains` 映射到 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，这是不区分大小写的。</span><span class="sxs-lookup"><span data-stu-id="b1df7-189">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="b1df7-190">在 SQLite 中，由于使用了默认排序规则，因此需要区分大小写。</span><span class="sxs-lookup"><span data-stu-id="b1df7-190">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="b1df7-191">导航到电影页面，并向 URL 追加一个如 `?searchString=Ghost` 的查询字符串。</span><span class="sxs-lookup"><span data-stu-id="b1df7-191">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="b1df7-192">例如 `https://localhost:5001/Movies?searchString=Ghost`。</span><span class="sxs-lookup"><span data-stu-id="b1df7-192">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="b1df7-193">筛选的电影将显示出来。</span><span class="sxs-lookup"><span data-stu-id="b1df7-193">The filtered movies are displayed.</span></span>

![Index 视图](search/_static/ghost.png)

<span data-ttu-id="b1df7-195">如果向 Index 页面添加了以下路由模板，搜索字符串则可作为 URL 段传递。</span><span class="sxs-lookup"><span data-stu-id="b1df7-195">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="b1df7-196">例如 `https://localhost:5001/Movies/Ghost`。</span><span class="sxs-lookup"><span data-stu-id="b1df7-196">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="b1df7-197">前面的路由约束允许按路由数据（URL 段）搜索标题，而不是按查询字符串值进行搜索。</span><span class="sxs-lookup"><span data-stu-id="b1df7-197">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="b1df7-198">`"{searchString?}"` 中的 `?` 表示这是可选路由参数。</span><span class="sxs-lookup"><span data-stu-id="b1df7-198">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Index 视图，显示添加到 URL 的“ghost”一词，以及返回的两部电影（Ghostbusters 和 Ghostbusters 2）的电影列表](search/_static/g2.png)

<span data-ttu-id="b1df7-200">ASP.NET Core 运行时使用[模型绑定](xref:mvc/models/model-binding)，通过查询字符串 (`?searchString=Ghost`) 或路由数据 (`https://localhost:5001/Movies/Ghost`) 设置 `SearchString` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="b1df7-200">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="b1df7-201">模型绑定不区分大小写\*_。</span><span class="sxs-lookup"><span data-stu-id="b1df7-201">Model binding is \**_not_* _ case sensitive.</span></span>

<span data-ttu-id="b1df7-202">但是，用户不能通过修改 URL 来搜索电影。</span><span class="sxs-lookup"><span data-stu-id="b1df7-202">However, users can't be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="b1df7-203">在此步骤中，会添加 UI 来筛选电影。</span><span class="sxs-lookup"><span data-stu-id="b1df7-203">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="b1df7-204">如果已添加路由约束 `"{searchString?}"`，请将它删除。</span><span class="sxs-lookup"><span data-stu-id="b1df7-204">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="b1df7-205">打开 _Pages/Movies/Index.cshtml\* 文件，并添加以下代码中突出显示的 `<form>` 标记：</span><span class="sxs-lookup"><span data-stu-id="b1df7-205">Open the _Pages/Movies/Index.cshtml\* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="b1df7-206">HTML `<form>` 标记使用以下[标记帮助程序](xref:mvc/views/tag-helpers/intro)：</span><span class="sxs-lookup"><span data-stu-id="b1df7-206">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="b1df7-207">[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="b1df7-207">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="b1df7-208">提交表单时，筛选器字符串将通过查询字符串发送到 Pages/Movies/Index 页面。</span><span class="sxs-lookup"><span data-stu-id="b1df7-208">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="b1df7-209">输入标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="b1df7-209">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="b1df7-210">保存更改并测试筛选器。</span><span class="sxs-lookup"><span data-stu-id="b1df7-210">Save the changes and test the filter.</span></span>

![显示标题筛选器文本框中键入了 ghost 一词的 Index 视图](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="b1df7-212">按流派搜索</span><span class="sxs-lookup"><span data-stu-id="b1df7-212">Search by genre</span></span>

<span data-ttu-id="b1df7-213">使用以下代码更新 `OnGetAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="b1df7-213">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="b1df7-214">下面的代码是一种 LINQ 查询，可从数据库中检索所有流派。</span><span class="sxs-lookup"><span data-stu-id="b1df7-214">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="b1df7-215">流派的 `SelectList` 是通过投影不包含重复值的流派创建的。</span><span class="sxs-lookup"><span data-stu-id="b1df7-215">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="b1df7-216">将按流派搜索添加到 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="b1df7-216">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="b1df7-217">更新 Index.cshtml [`<form>` 元素] (https://developer.mozilla.org/docs/Web/HTML/Element/form) ，如以下标记中突出显示：</span><span class="sxs-lookup"><span data-stu-id="b1df7-217">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="b1df7-218">通过按流派或/和电影标题搜索来测试应用。</span><span class="sxs-lookup"><span data-stu-id="b1df7-218">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="b1df7-219">前面的代码使用[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)和选项标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="b1df7-219">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1df7-220">其他资源</span><span class="sxs-lookup"><span data-stu-id="b1df7-220">Additional resources</span></span>

* [<span data-ttu-id="b1df7-221">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="b1df7-221">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="b1df7-222">[上一篇：更新页面](xref:tutorials/razor-pages/da1)
> [下一步：添加新字段](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="b1df7-222">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
