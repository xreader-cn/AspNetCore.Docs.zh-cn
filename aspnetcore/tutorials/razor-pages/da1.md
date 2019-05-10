---
title: 在 ASP.NET Core 应用中更新生成的页面
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中更新生成的页面。
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 19f54e04e748f5cf0469c2b5428800694d2c9344
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884072"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>在 ASP.NET Core 应用中更新生成的页面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

构架的电影应用有个不错的开始，但是展示效果还不够理想。 ReleaseDate 应是 Release Date（两个词）。

![在 Chrome 中打开的电影应用程序](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>更新生成的代码

打开 Models/Movie.cs 文件，并添加以下代码中突出显示的行：

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=12,17)]

`[Column(TypeName = "decimal(18, 2)")]` 数据注释使 Entity Framework Core 可以将 `Price` 正确映射到数据库中的货币。 有关详细信息，请参阅[数据类型](/ef/core/modeling/relational/data-types)。

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 未包括在下一个教程中。 [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) 特性指定要显示的字段名称的内容（本例中应为“Release Date”，而不是“ReleaseDate”）。 [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性指定数据的类型（日期），使字段中存储的时间信息不会显示。

浏览到 Pages/Movies，并将鼠标悬停在“编辑”链接上以查看目标 URL。

![鼠标悬停在“编辑”链接上的浏览器窗口，显示了 http://localhost:1234/Movies/Edit/5 的链接 URL](~/tutorials/razor-pages/da1/edit7.png)

“编辑”、“详细信息”和“删除”链接是在 Pages/Movies/Index.cshtml 文件中由[定位标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)生成的。

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。 在前面的代码中，`AnchorTagHelper` 从 Razor 页面（路由是相对的）、`asp-page` 和路由 ID (`asp-route-id`) 动态生成 HTML `href` 特性值。 有关详细信息，请参阅[页面的 URL 生成](xref:razor-pages/index#url-generation-for-pages)。

在最喜欢的浏览器中使用“查看源”来检查生成的标记。 生成的 HTML 的一部分如下所示：

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

动态生成的链接通过查询字符串传递电影 ID（例如，`https://localhost:5001/Movies/Details?id=1` 中的 `?id=1`）。

更新“编辑”、“详细信息”和“删除”Razor 页面以使用“{id:int?}”路由模板。 将上述每个页面的页面指令从 `@page` 更改为 `@page "{id:int}"`。 运行应用，然后查看源。 生成的 HTML 会将 ID 添加到 URL 的路径部分：

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

如果对具有“{id: int}” 路由模板的页面进行的请求中不包含整数，则将返回 HTTP 404（未找到）错误。 例如，`http://localhost:5000/Movies/Details` 将返回 404 错误。 若要使 ID 可选，请将 `?` 追加到路由约束：

 ```cshtml
@page "{id:int?}"
```

若要测试 `@page "{id:int?}"` 的行为：

* 在 Pages/Movies/Details.cshtml 中将 page 指令设置为 `@page "{id:int?}"`。
* 在 `public async Task<IActionResult> OnGetAsync(int? id)` 中（位于 Pages/Movies/Details.cshtml.cs 中）设置断点。
* 导航到 `https://localhost:5001/Movies/Details/`。

使用 `@page "{id:int}"` 指令时，永远不会命中断点。 路由引擎返回 HTTP 404。 使用 `@page "{id:int?}"` 时，`OnGetAsync` 方法返回 `NotFound` (HTTP 404)。

尽管不建议这样做，但仍可以将 `OnGetAsync` 方法（在 Pages/Movies/Delete.cshtml.cs 中）编写为：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Delete.cshtml.cs?name=snippet)]

测试上述代码：

* 选择删除链接。
* 从 URL 中删除 ID。 例如，将 `https://localhost:5001/Movies/Delete/8` 更改为 `https://localhost:5001/Movies/Delete`。
* 在调试程序中逐步执行代码。

### <a name="review-concurrency-exception-handling"></a>查看并发异常处理

查看 Pages/Movies/Edit.cshtml.cs 文件中的 `OnPostAsync` 方法：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

当一个客户端删除电影并且另一个客户端对电影发布更改时，前面的代码会检测并发异常。

测试 `catch` 块：

* 在 `catch (DbUpdateConcurrencyException)`上设置断点
* 对电影选择“编辑”，进行更改，但不要输入“保存”。
* 在其他浏览器窗口中，选择同一电影的“删除”链接，然后删除此电影。
* 在之前的浏览器窗口中，将更改发布到电影。

生产代码可能要检测并发冲突。 有关详细信息，请参阅[处理并发冲突](xref:data/ef-rp/concurrency)。

### <a name="posting-and-binding-review"></a>发布和绑定审阅

检查 Pages/Movies/Edit.cshtml.cs 文件：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

当对 Movies/Edit 页面进行 HTTP GET 请求时（例如 `http://localhost:5000/Movies/Edit/2`）：

* `OnGetAsync` 方法从数据库提取电影并返回 `Page` 方法。 
* `Page` 方法呈现“Pages/Movies/Edit.cshtml”Razor 页面。 Pages/Movies/Edit.cshtml 文件包含模型指令 (`@model RazorPagesMovie.Pages.Movies.EditModel`)，这使电影模型在页面上可用。
* “编辑”表单中会显示电影的值。

当发布 Movies/Edit 页面时：

* 此页面上的表单值将绑定到 `Movie` 属性。 `[BindProperty]` 特性会启用[模型绑定](xref:mvc/models/model-binding)。

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* 如果模型状态中存在错误（例如，`ReleaseDate` 无法被转换为日期），则会使用已提交的值显示表单。
* 如果没有模型错误，则电影已保存。

“索引”、“创建”和“删除”Razor 页面中的 HTTP GET 方法遵循一个类似的模式。 “创建”Razor 页面中的 HTTP POST `OnPostAsync` 方法遵循的模式类似于“编辑”Razor 页面中的 `OnPostAsync` 方法所遵循的模式。

在下一教程中将添加搜索。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [上一篇：使用数据库](xref:tutorials/razor-pages/sql)
> [下一篇：添加搜索](xref:tutorials/razor-pages/search)
