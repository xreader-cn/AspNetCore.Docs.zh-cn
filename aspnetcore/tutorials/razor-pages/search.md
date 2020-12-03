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
ms.openlocfilehash: 3b95fe117895555ebcd44f971e7bb9d1173e1697
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419975"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>第 6 部分，将搜索添加到 ASP.NET Core Razor 页面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

在以下部分中，添加了按流派或名称搜索电影。

将以下突出显示的 using 语句和属性添加到 Pages/Movies/Index.cshtml.cs：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

在前面的代码中：

* `SearchString`：包含用户在搜索文本框中输入的文本。 `SearchString` 也有 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性。 `[BindProperty]` 会绑定名称与属性相同的表单值和查询字符串。 在 HTTP GET 请求中进行绑定需要 `[BindProperty(SupportsGet = true)]`。
* `Genres`：包含流派列表。 `Genres` 使用户能够从列表中选择一种流派。 `SelectList` 需要 `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`：包含用户选择的特定流派。 例如，“Western”。
* `Genres` 和 `MovieGenre` 会在本教程的后续部分中使用。

[!INCLUDE[](~/includes/bind-get.md)]

使用以下代码更新 Index 页面的 `OnGetAsync` 方法：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` 方法的第一行创建了 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查询用于选择电影：

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

此时仅对查询进行了定义，它还不会针对数据库运行。

如果 `SearchString` 属性不为 null 或空，则电影查询会修改为根据搜索字符串进行筛选：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` 代码是 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。 Lambda 在基于方法的 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查询中用作标准查询运算符方法的参数，如 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 方法或 `Contains`。 在对 LINQ 查询进行定义或通过调用方法（如 `Where`、`Contains` 或 `OrderBy`）进行修改后，此查询不会执行。 相反，会延迟执行查询。 表达式的计算会延迟，直到循环访问其实现的值或者调用 `ToListAsync` 方法为止。 有关详细信息，请参阅 [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution)（查询执行）。

> [!NOTE]
> [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法在数据库中运行，而不是在 C# 代码中运行。 查询是否区分大小写取决于数据库和排序规则。 在 SQL Server 上，`Contains` 映射到 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，这是不区分大小写的。 在 SQLite 中，由于使用了默认排序规则，因此需要区分大小写。

导航到电影页面，并向 URL 追加一个如 `?searchString=Ghost` 的查询字符串。 例如 `https://localhost:5001/Movies?searchString=Ghost`。 筛选的电影将显示出来。

![Index 视图](search/_static/ghost.png)

如果向 Index 页面添加了以下路由模板，搜索字符串则可作为 URL 段传递。 例如 `https://localhost:5001/Movies/Ghost`。

```cshtml
@page "{searchString?}"
```

前面的路由约束允许按路由数据（URL 段）搜索标题，而不是按查询字符串值进行搜索。  `"{searchString?}"` 中的 `?` 表示这是可选路由参数。

![Index 视图，显示添加到 URL 的“ghost”一词，以及返回的两部电影（Ghostbusters 和 Ghostbusters 2）的电影列表](search/_static/g2.png)

ASP.NET Core 运行时使用[模型绑定](xref:mvc/models/model-binding)，通过查询字符串 (`?searchString=Ghost`) 或路由数据 (`https://localhost:5001/Movies/Ghost`) 设置 `SearchString` 属性的值。 模型绑定不区分大小写*_。

但是，用户不能通过修改 URL 来搜索电影。 在此步骤中，会添加 UI 来筛选电影。 如果已添加路由约束 `"{searchString?}"`，请将它删除。

打开 _Pages/Movies/Index.cshtml* 文件，并添加以下代码中突出显示的标记：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` 标记使用以下[标记帮助程序](xref:mvc/views/tag-helpers/intro)：

* [表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。 提交表单时，筛选器字符串将通过查询字符串发送到 Pages/Movies/Index 页面。
* [输入标记帮助程序](xref:mvc/views/working-with-forms#the-input-tag-helper)

保存更改并测试筛选器。

![显示标题筛选器文本框中键入了 ghost 一词的 Index 视图](search/_static/filter.png)

## <a name="search-by-genre"></a>按流派搜索

使用以下代码更新 Index 页面的 `OnGetAsync` 方法：

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

下面的代码是一种 LINQ 查询，可从数据库中检索所有流派。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

流派的 `SelectList` 是通过投影不包含重复值的流派创建的。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>将按流派搜索添加到 Razor 页面

1. 更新 Index.cshtml [`<form>` 元素] (https://developer.mozilla.org/docs/Web/HTML/Element/form) ，如以下标记中突出显示：

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. 通过按流派或/和电影标题搜索来测试应用。

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：更新页面](xref:tutorials/razor-pages/da1)
> [下一步：添加新字段](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。

在以下部分中，添加了按流派或名称搜索电影。

将以下突出显示的属性添加到 Pages/Movies/Index.cshtml.cs：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`：包含用户在搜索文本框中输入的文本。 `SearchString` 也有 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性。 `[BindProperty]` 会绑定名称与属性相同的表单值和查询字符串。 在 HTTP GET 请求中进行绑定需要 `[BindProperty(SupportsGet = true)]`。
* `Genres`：包含流派列表。 `Genres` 使用户能够从列表中选择一种流派。 `SelectList` 需要 `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`：包含用户选择的特定流派。 例如，“Western”。
* `Genres` 和 `MovieGenre` 会在本教程的后续部分中使用。

[!INCLUDE[](~/includes/bind-get.md)]

使用以下代码更新 Index 页面的 `OnGetAsync` 方法：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` 方法的第一行创建了 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查询用于选择电影：

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

此时仅对查询进行了定义，它还不会针对数据库运行。

如果 `SearchString` 属性不为 null 或空，则电影查询会修改为根据搜索字符串进行筛选：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` 代码是 [Lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。 Lambda 在基于方法的 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) 查询中用作标准查询运算符方法的参数，如 [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) 方法或 `Contains`。 在对 LINQ 查询进行定义或通过调用方法（如 `Where`、`Contains` 或 `OrderBy`）进行修改后，此查询不会执行。 相反，会延迟执行查询。 表达式的计算会延迟，直到循环访问其实现的值或者调用 `ToListAsync` 方法为止。 有关详细信息，请参阅 [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution)（查询执行）。

**注意：** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) 方法在数据库中运行，而不是在 C# 代码中运行。 查询是否区分大小写取决于数据库和排序规则。 在 SQL Server 上，`Contains` 映射到 [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql)，这是不区分大小写的。 在 SQLite 中，由于使用了默认排序规则，因此需要区分大小写。

导航到电影页面，并向 URL 追加一个如 `?searchString=Ghost` 的查询字符串。 例如 `https://localhost:5001/Movies?searchString=Ghost`。 筛选的电影将显示出来。

![Index 视图](search/_static/ghost.png)

如果向 Index 页面添加了以下路由模板，搜索字符串则可作为 URL 段传递。 例如 `https://localhost:5001/Movies/Ghost`。

```cshtml
@page "{searchString?}"
```

前面的路由约束允许按路由数据（URL 段）搜索标题，而不是按查询字符串值进行搜索。  `"{searchString?}"` 中的 `?` 表示这是可选路由参数。

![Index 视图，显示添加到 URL 的“ghost”一词，以及返回的两部电影（Ghostbusters 和 Ghostbusters 2）的电影列表](search/_static/g2.png)

ASP.NET Core 运行时使用[模型绑定](xref:mvc/models/model-binding)，通过查询字符串 (`?searchString=Ghost`) 或路由数据 (`https://localhost:5001/Movies/Ghost`) 设置 `SearchString` 属性的值。 模型绑定不区分大小写*_。

但是，用户不能通过修改 URL 来搜索电影。 在此步骤中，会添加 UI 来筛选电影。 如果已添加路由约束 `"{searchString?}"`，请将它删除。

打开 _Pages/Movies/Index.cshtml* 文件，并添加以下代码中突出显示的 `<form>` 标记：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` 标记使用以下[标记帮助程序](xref:mvc/views/tag-helpers/intro)：

* [表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。 提交表单时，筛选器字符串将通过查询字符串发送到 Pages/Movies/Index 页面。
* [输入标记帮助程序](xref:mvc/views/working-with-forms#the-input-tag-helper)

保存更改并测试筛选器。

![显示标题筛选器文本框中键入了 ghost 一词的 Index 视图](search/_static/filter.png)

## <a name="search-by-genre"></a>按流派搜索

使用以下代码更新 `OnGetAsync` 方法：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

下面的代码是一种 LINQ 查询，可从数据库中检索所有流派。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

流派的 `SelectList` 是通过投影不包含重复值的流派创建的。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>将按流派搜索添加到 Razor 页面

更新 Index.cshtml [`<form>` 元素] (https://developer.mozilla.org/docs/Web/HTML/Element/form) ，如以下标记中突出显示：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

通过按流派或/和电影标题搜索来测试应用。
前面的代码使用[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)和选项标记帮助程序。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [上一篇：更新页面](xref:tutorials/razor-pages/da1)
> [下一步：添加新字段](xref:tutorials/razor-pages/new-field)

::: moniker-end
