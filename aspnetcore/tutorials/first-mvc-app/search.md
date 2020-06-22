---
title: 第 7 部分，将搜索添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: ASP.NET Core MVC 教程系列第 7 部分。
ms.author: riande
ms.date: 12/13/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 83bd904f90190456d42453653d3420c4c572fe8c
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652756"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="f1235-103">第 7 部分，将搜索添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="f1235-103">Part 7, add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="f1235-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f1235-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f1235-105">在本部分中，将向 `Index` 操作方法添加搜索功能，以实现按“类型”或“名称”搜索电影 。</span><span class="sxs-lookup"><span data-stu-id="f1235-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="f1235-106">使用以下代码更新 Controllers/MoviesController.cs 中的 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="f1235-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="f1235-107">`Index` 操作方法的第一行创建了 [LINQ](/dotnet/standard/using-linq) 查询用于选择电影：</span><span class="sxs-lookup"><span data-stu-id="f1235-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="f1235-108">此时仅对查询进行了定义，它还不会针对数据库运行。</span><span class="sxs-lookup"><span data-stu-id="f1235-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="f1235-109">如果 `searchString` 参数包含一个字符串，电影查询则会被修改为根据搜索字符串的值进行筛选：</span><span class="sxs-lookup"><span data-stu-id="f1235-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="f1235-110">上面的 `s => s.Title.Contains()` 代码是 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。</span><span class="sxs-lookup"><span data-stu-id="f1235-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f1235-111">Lambda 在基于方法的 [LINQ](/dotnet/standard/using-linq) 查询中用作标准查询运算符方法的参数，如 [Where](/dotnet/api/system.linq.enumerable.where) 方法或 `Contains`（上述的代码中所使用的）。</span><span class="sxs-lookup"><span data-stu-id="f1235-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="f1235-112">在对 LINQ 查询进行定义或通过调用方法（如 `Where`、`Contains` 或 `OrderBy`）进行修改后，此查询不会被执行。</span><span class="sxs-lookup"><span data-stu-id="f1235-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="f1235-113">相反，会延迟执行查询。</span><span class="sxs-lookup"><span data-stu-id="f1235-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="f1235-114">这意味着表达式的计算会延迟，直到真正循环访问其实现的值或者调用 `ToListAsync` 方法为止。</span><span class="sxs-lookup"><span data-stu-id="f1235-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="f1235-115">有关延迟执行查询的详细信息，请参阅[Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution)（查询执行）。</span><span class="sxs-lookup"><span data-stu-id="f1235-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="f1235-116">注意：[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法在数据库上运行，而不是在上面显示的 C# 代码中运行。</span><span class="sxs-lookup"><span data-stu-id="f1235-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="f1235-117">查询是否区分大小写取决于数据库和排序规则。</span><span class="sxs-lookup"><span data-stu-id="f1235-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="f1235-118">在 SQL Server 上，[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 映射到 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，这是不区分大小写的。</span><span class="sxs-lookup"><span data-stu-id="f1235-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="f1235-119">在 SQLite 中，由于使用了默认排序规则，因此需要区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f1235-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="f1235-120">导航到 `/Movies/Index`。</span><span class="sxs-lookup"><span data-stu-id="f1235-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="f1235-121">将查询字符串（如 `?searchString=Ghost`）追加到 URL。</span><span class="sxs-lookup"><span data-stu-id="f1235-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="f1235-122">筛选的电影将显示出来。</span><span class="sxs-lookup"><span data-stu-id="f1235-122">The filtered movies are displayed.</span></span>

![索引视图](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="f1235-124">如果将 `Index` 方法的签名更改为具有名称为 `id` 的参数，则 `id` 参数将匹配 Startup.cs 中设置的默认路由的可选 `{id}` 占位符。</span><span class="sxs-lookup"><span data-stu-id="f1235-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="f1235-125">将参数更改为 `id`，并将出现的所有 `searchString` 更改为 `id`。</span><span class="sxs-lookup"><span data-stu-id="f1235-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="f1235-126">之前的 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="f1235-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="f1235-127">更新后带 `id` 参数的 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="f1235-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="f1235-128">现可将搜索标题作为路由数据（ URL 段）而非查询字符串值进行传递。</span><span class="sxs-lookup"><span data-stu-id="f1235-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![索引视图，显示添加到 URL 的“ghost”一词以及返回的两部电影（Ghostbusters 和 Ghostbusters 2）的电影列表](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="f1235-130">但是，不能指望用户在每次要搜索电影时都修改 URL。</span><span class="sxs-lookup"><span data-stu-id="f1235-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="f1235-131">因此需要添加 UI 元素来帮助他们筛选电影。</span><span class="sxs-lookup"><span data-stu-id="f1235-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="f1235-132">若已更改 `Index` 方法的签名，以测试如何传递绑定路由的 `ID` 参数，请改回原样，使其采用名为 `searchString` 的参数：</span><span class="sxs-lookup"><span data-stu-id="f1235-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="f1235-133">打开“Views/Movies/Index.cshtml”文件，并添加以下突出显示的 `<form>` 标记：</span><span class="sxs-lookup"><span data-stu-id="f1235-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="f1235-134">此 HTML `<form>` 标记使用[表单标记帮助程序](xref:mvc/views/working-with-forms)，因此提交表单时，筛选器字符串会发布到电影控制器的 `Index` 操作。</span><span class="sxs-lookup"><span data-stu-id="f1235-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="f1235-135">保存更改，然后测试筛选器。</span><span class="sxs-lookup"><span data-stu-id="f1235-135">Save your changes and then test the filter.</span></span>

![显示标题筛选器文本框中键入了 ghost 一词的索引视图](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="f1235-137">如你所料，不存在 `Index` 方法的 `[HttpPost]` 重载。</span><span class="sxs-lookup"><span data-stu-id="f1235-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="f1235-138">无需重载，因为该方法不更改应用的状态，仅筛选数据。</span><span class="sxs-lookup"><span data-stu-id="f1235-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="f1235-139">可添加以下 `[HttpPost] Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="f1235-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="f1235-140">`notUsed` 参数用于创建 `Index` 方法的重载。</span><span class="sxs-lookup"><span data-stu-id="f1235-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="f1235-141">本教程稍后将对此进行探讨。</span><span class="sxs-lookup"><span data-stu-id="f1235-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="f1235-142">如果添加此方法，则操作调用程序将与 `[HttpPost] Index` 方法匹配，且将运行 `[HttpPost] Index` 方法，如下图所示。</span><span class="sxs-lookup"><span data-stu-id="f1235-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![显示“来自 HttpPost 索引: 筛选 ghost”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="f1235-144">但是，即使添加 `Index` 方法的 `[HttpPost]` 版本，其实现方式也受到限制。</span><span class="sxs-lookup"><span data-stu-id="f1235-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="f1235-145">假设你想要将特定搜索加入书签，或向朋友发送一个链接，让他们单击链接即可查看筛选出的相同电影列表。</span><span class="sxs-lookup"><span data-stu-id="f1235-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="f1235-146">请注意 HTTP POST 请求的 URL 与 GET 请求的 URL (localhost:{PORT}/Movies/Index) 相同，URL 中没有任何搜索信息。</span><span class="sxs-lookup"><span data-stu-id="f1235-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="f1235-147">搜索字符串信息作为[表单域值](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)发送给服务器。</span><span class="sxs-lookup"><span data-stu-id="f1235-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="f1235-148">可使用浏览器开发人员工具或出色的 [Fiddler 工具](https://www.telerik.com/fiddler)对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="f1235-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="f1235-149">下图展示了 Chrome 浏览器开发人员工具：</span><span class="sxs-lookup"><span data-stu-id="f1235-149">The image below shows the Chrome browser Developer tools:</span></span>

![Microsoft Edge 中开发人员工具的“网络”选项卡，显示了 ghost 的 searchString 值的请求正文](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="f1235-151">在请求正文中，可看到搜索参数和 [XSRF](xref:security/anti-request-forgery) 标记。</span><span class="sxs-lookup"><span data-stu-id="f1235-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="f1235-152">请注意，正如之前教程所述，[表单标记帮助程序](xref:mvc/views/working-with-forms) 会生成一个 [XSRF](xref:security/anti-request-forgery) 防伪标记。</span><span class="sxs-lookup"><span data-stu-id="f1235-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="f1235-153">不会修改数据，因此无需验证控制器方法中的标记。</span><span class="sxs-lookup"><span data-stu-id="f1235-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="f1235-154">搜索参数位于请求正文而非 URL 中，因此无法捕获该搜索信息进行书签设定或与他人共享。</span><span class="sxs-lookup"><span data-stu-id="f1235-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="f1235-155">修复方法是指定：请求应为 Views/Movies/Index.cshtml 文件中的 `HTTP GET`。</span><span class="sxs-lookup"><span data-stu-id="f1235-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="f1235-156">现在提交搜索后，URL 将包含搜索查询字符串。</span><span class="sxs-lookup"><span data-stu-id="f1235-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="f1235-157">即使具备 `HttpPost Index` 方法，搜索也将转到 `HttpGet Index` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="f1235-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![URL 中显示 searchString=ghost 且返回了 Ghostbusters 和 Ghostbusters 2 的浏览器窗口包含 ghost 一词](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="f1235-159">以下标记显示对 `form` 标记的更改：</span><span class="sxs-lookup"><span data-stu-id="f1235-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="f1235-160">添加按流派搜索</span><span class="sxs-lookup"><span data-stu-id="f1235-160">Add Search by genre</span></span>

<span data-ttu-id="f1235-161">将以下 `MovieGenreViewModel` 类添加到“模型”文件夹：</span><span class="sxs-lookup"><span data-stu-id="f1235-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="f1235-162">“电影流派”视图模型将包含：</span><span class="sxs-lookup"><span data-stu-id="f1235-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="f1235-163">电影列表。</span><span class="sxs-lookup"><span data-stu-id="f1235-163">A list of movies.</span></span>
* <span data-ttu-id="f1235-164">包含流派列表的 `SelectList`。</span><span class="sxs-lookup"><span data-stu-id="f1235-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="f1235-165">这使用户能够从列表中选择一种流派。</span><span class="sxs-lookup"><span data-stu-id="f1235-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="f1235-166">包含所选流派的 `MovieGenre`。</span><span class="sxs-lookup"><span data-stu-id="f1235-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="f1235-167">`SearchString`包含用户在搜索文本框中输入的文本。</span><span class="sxs-lookup"><span data-stu-id="f1235-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="f1235-168">将 `MoviesController.cs` 中的 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="f1235-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="f1235-169">以下代码是一种 `LINQ` 查询，可从数据库中检索所有流派。</span><span class="sxs-lookup"><span data-stu-id="f1235-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="f1235-170">通过投影不同的流派创建 `SelectList`（我们不希望选择列表中的流派重复）。</span><span class="sxs-lookup"><span data-stu-id="f1235-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="f1235-171">当用户搜索某个项目时，搜索值会保留在搜索框中。</span><span class="sxs-lookup"><span data-stu-id="f1235-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="f1235-172">向“索引”视图添加“按流派搜索”</span><span class="sxs-lookup"><span data-stu-id="f1235-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="f1235-173">按如下所示更新 Views/Movies/ 中的 `Index.cshtml`：</span><span class="sxs-lookup"><span data-stu-id="f1235-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="f1235-174">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="f1235-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="f1235-175">在上述代码中，`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="f1235-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="f1235-176">由于只检查但未计算 Lambda 表达式，因此当 `model`、`model.Movies[0]` 或 `model.Movies` 为 `null` 或空时，你不会收到访问冲突。</span><span class="sxs-lookup"><span data-stu-id="f1235-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="f1235-177">对 Lambda 表达式求值时（例如，`@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="f1235-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="f1235-178">通过按流派搜索、按电影标题搜索以及按流派和电影标题搜索来测试应用：</span><span class="sxs-lookup"><span data-stu-id="f1235-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![显示 https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2 结果的浏览器窗口](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="f1235-180">[上一页](controller-methods-views.md)
> [下一页](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="f1235-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
