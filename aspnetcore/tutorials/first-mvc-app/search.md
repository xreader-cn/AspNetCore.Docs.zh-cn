---
title: 将搜索添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: 演示如何将搜索添加到基本 ASP.NET Core MVC 应用
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171634"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>将搜索添加到 ASP.NET Core MVC 应用

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

在本部分中，将向 `Index` 操作方法添加搜索功能，以实现按“类型”或“名称”搜索电影   。

使用以下代码更新 Controllers/MoviesController.cs  中的 `Index` 方法：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

`Index` 操作方法的第一行创建了 [LINQ](/dotnet/standard/using-linq) 查询用于选择电影：

```csharp
var movies = from m in _context.Movie
             select m;
```

此时仅对查询进行了定义，它还不会针对数据库运行   。

如果 `searchString` 参数包含一个字符串，电影查询则会被修改为根据搜索字符串的值进行筛选：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

上面的 `s => s.Title.Contains()` 代码是 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。 Lambda 在基于方法的 [LINQ](/dotnet/standard/using-linq) 查询中用作标准查询运算符方法的参数，如 [Where](/dotnet/api/system.linq.enumerable.where) 方法或 `Contains`（上述的代码中所使用的）。 在对 LINQ 查询进行定义或通过调用方法（如 `Where`、`Contains` 或 `OrderBy`）进行修改后，此查询不会被执行。 相反，会延迟执行查询。  这意味着表达式的计算会延迟，直到真正循环访问其实现的值或者调用 `ToListAsync` 方法为止。 有关延迟执行查询的详细信息，请参阅[Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution)（查询执行）。

注意：[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法在数据库上运行，而不是在上面显示的 C# 代码中运行。 查询是否区分大小写取决于数据库和排序规则。 在 SQL Server 上，[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 映射到 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，这是不区分大小写的。 在 SQLite 中，由于使用了默认排序规则，因此需要区分大小写。

导航到 `/Movies/Index`。 将查询字符串（如 `?searchString=Ghost`）追加到 URL。 筛选的电影将显示出来。

![索引视图](~/tutorials/first-mvc-app/search/_static/ghost.png)

如果将 `Index` 方法的签名更改为具有名称为 `id` 的参数，则 `id` 参数将匹配 Startup.cs 中设置的默认路由的可选 `{id}` 占位符  。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

将参数更改为 `id`，并将出现的所有 `searchString` 更改为 `id`。

之前的 `Index` 方法：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

更新后带 `id` 参数的 `Index` 方法：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

现可将搜索标题作为路由数据（ URL 段）而非查询字符串值进行传递。

![索引视图，显示添加到 URL 的“ghost”一词以及返回的两部电影（Ghostbusters 和 Ghostbusters 2）的电影列表](~/tutorials/first-mvc-app/search/_static/g2.png)

但是，不能指望用户在每次要搜索电影时都修改 URL。 因此需要添加 UI 元素来帮助他们筛选电影。 若已更改 `Index` 方法的签名，以测试如何传递绑定路由的 `ID` 参数，请改回原样，使其采用名为 `searchString` 的参数：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

打开“Views/Movies/Index.cshtml”文件，并添加以下突出显示的 `<form>` 标记  ：

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

此 HTML `<form>` 标记使用[表单标记帮助程序](xref:mvc/views/working-with-forms)，因此提交表单时，筛选器字符串会发布到电影控制器的 `Index` 操作。 保存更改，然后测试筛选器。

![显示标题筛选器文本框中键入了 ghost 一词的索引视图](~/tutorials/first-mvc-app/search/_static/filter.png)

如你所料，不存在 `Index` 方法的 `[HttpPost]` 重载。 无需重载，因为该方法不更改应用的状态，仅筛选数据。

可添加以下 `[HttpPost] Index` 方法。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed` 参数用于创建 `Index` 方法的重载。 本教程稍后将对此进行探讨。

如果添加此方法，则操作调用程序将与 `[HttpPost] Index` 方法匹配，且将运行 `[HttpPost] Index` 方法，如下图所示。

![显示“来自 HttpPost 索引: 筛选 ghost”应用程序响应的浏览器窗口](~/tutorials/first-mvc-app/search/_static/fo.png)

但是，即使添加 `Index` 方法的 `[HttpPost]` 版本，其实现方式也受到限制。 假设你想要将特定搜索加入书签，或向朋友发送一个链接，让他们单击链接即可查看筛选出的相同电影列表。 请注意 HTTP POST 请求的 URL 与 GET 请求的 URL (localhost:{PORT}/Movies/Index) 相同，URL 中没有任何搜索信息。 搜索字符串信息作为[表单域值](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)发送给服务器。 可使用浏览器开发人员工具或出色的 [Fiddler 工具](https://www.telerik.com/fiddler)对其进行验证。 下图展示了 Chrome 浏览器开发人员工具：

![Microsoft Edge 中开发人员工具的“网络”选项卡，显示了 ghost 的 searchString 值的请求正文](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

在请求正文中，可看到搜索参数和 [XSRF](xref:security/anti-request-forgery) 标记。 请注意，正如之前教程所述，[表单标记帮助程序](xref:mvc/views/working-with-forms) 会生成一个 [XSRF](xref:security/anti-request-forgery) 防伪标记。 不会修改数据，因此无需验证控制器方法中的标记。

搜索参数位于请求正文而非 URL 中，因此无法捕获该搜索信息进行书签设定或与他人共享。 修复方法是指定：请求应为 Views/Movies/Index.cshtml  文件中的 `HTTP GET`。

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

现在提交搜索后，URL 将包含搜索查询字符串。 即使具备 `HttpPost Index` 方法，搜索也将转到 `HttpGet Index` 操作方法。

![URL 中显示 searchString=ghost 且返回了 Ghostbusters 和 Ghostbusters 2 的浏览器窗口包含 ghost 一词](~/tutorials/first-mvc-app/search/_static/search_get.png)

以下标记显示对 `form` 标记的更改：

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>添加按流派搜索

将以下 `MovieGenreViewModel` 类添加到“模型”文件夹  ：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

“电影流派”视图模型将包含：

* 电影列表。
* 包含流派列表的 `SelectList`。 这使用户能够从列表中选择一种流派。
* 包含所选流派的 `MovieGenre`。
* `SearchString`包含用户在搜索文本框中输入的文本。

将 `MoviesController.cs` 中的 `Index` 方法替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

以下代码是一种 `LINQ` 查询，可从数据库中检索所有流派。

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

通过投影不同的流派创建 `SelectList`（我们不希望选择列表中的流派重复）。

当用户搜索某个项目时，搜索值会保留在搜索框中。

## <a name="add-search-by-genre-to-the-index-view"></a>向“索引”视图添加“按流派搜索”

按如下所示更新 Views/Movies/  中的 `Index.cshtml`：

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

检查以下 HTML 帮助程序中使用的 Lambda 表达式：

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

在上述代码中，`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。 由于只检查但未计算 Lambda 表达式，因此当 `model`、`model.Movies[0]` 或 `model.Movies` 为 `null` 或空时，你不会收到访问冲突。 对 Lambda 表达式求值时（例如，`@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。

通过按流派搜索、按电影标题搜索以及按流派和电影标题搜索来测试应用：

![显示 https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2 结果的浏览器窗口](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [上一页](controller-methods-views.md)
> [下一页](new-field.md)
