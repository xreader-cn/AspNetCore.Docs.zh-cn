---
title: ASP.NET Core 中已搭建基架的 Razor 页面
author: rick-anderson
description: 介绍通过搭建基架生成的 Razor 页面。
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: 00a8458b9bee4d30c5774a980ff5c23fb8872737
ms.sourcegitcommit: 38cac2552029fc19428722bb204ff9e16eb94225
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2019
ms.locfileid: "69573142"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="28def-103">ASP.NET Core 中已搭建基架的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="28def-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="28def-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="28def-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="28def-105">本教程介绍在[上一教程](xref:tutorials/razor-pages/model)中通过搭建基架创建的 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="28def-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="28def-106">“创建”、“删除”、“详细信息”和“编辑”页面</span><span class="sxs-lookup"><span data-stu-id="28def-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="28def-107">检查 Pages/Movies/Index.cshtml.cs  页面模型：</span><span class="sxs-lookup"><span data-stu-id="28def-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="28def-108">Razor 页面派生自 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="28def-108">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="28def-109">按照约定，`PageModel` 派生的类称为 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="28def-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="28def-110">此构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将 `RazorPagesMovieContext` 添加到页。</span><span class="sxs-lookup"><span data-stu-id="28def-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="28def-111">所有已搭建基架的页面都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="28def-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="28def-112">请参阅[异步代码](xref:data/ef-rp/intro#asynchronous-code)，了解有关使用实体框架的异步编程的详细信息。</span><span class="sxs-lookup"><span data-stu-id="28def-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="28def-113">对页面发出请求时，`OnGetAsync` 方法向 Razor 页面返回影片列表。</span><span class="sxs-lookup"><span data-stu-id="28def-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="28def-114">在 Razor 页面上调用 `OnGetAsync` 或 `OnGet` 以初始化页面状态。</span><span class="sxs-lookup"><span data-stu-id="28def-114">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="28def-115">在这种情况下，`OnGetAsync` 将获得影片列表并显示出来。</span><span class="sxs-lookup"><span data-stu-id="28def-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="28def-116">当 `OnGet` 返回 `void` 或 `OnGetAsync` 返回 `Task` 时，不使用任何返回方法。</span><span class="sxs-lookup"><span data-stu-id="28def-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="28def-117">当返回类型是 `IActionResult` 或 `Task<IActionResult>` 时，必须提供返回语句。</span><span class="sxs-lookup"><span data-stu-id="28def-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="28def-118">例如，Pages/Movies/Create.cshtml.cs  `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="28def-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<span data-ttu-id="28def-119">检查 Pages/Movies/Index.cshtml<a name="index"></a> Razor 页面  ：</span><span class="sxs-lookup"><span data-stu-id="28def-119"><a name="index"></a> Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="28def-120">Razor 可以从 HTML 转换为 C# 或 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="28def-120">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="28def-121">当 `@` 符号后跟 [Razor 保留关键字](xref:mvc/views/razor#razor-reserved-keywords)时，它会转换为 Razor 特定标记，否则会转换为 C#。</span><span class="sxs-lookup"><span data-stu-id="28def-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="28def-122">`@page` Razor 指令将文件转换为一个 MVC 操作，这意味着它可以处理请求。</span><span class="sxs-lookup"><span data-stu-id="28def-122">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="28def-123">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="28def-123">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="28def-124">`@page` 是转换到 Razor 特定标记的一个示例。</span><span class="sxs-lookup"><span data-stu-id="28def-124">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="28def-125">有关详细信息，请参阅 [Razor 语法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="28def-125">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="28def-126">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="28def-126">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="28def-127">`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="28def-127">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="28def-128">检查 Lambda 表达式（而非求值）。</span><span class="sxs-lookup"><span data-stu-id="28def-128">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="28def-129">这意味着当 `model`、`model.Movie` 或 `model.Movie[0]` 为 `null` 或为空时，不会存在任何访问冲突。</span><span class="sxs-lookup"><span data-stu-id="28def-129">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="28def-130">对 Lambda 表达式求值时（例如，使用 `@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="28def-130">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="28def-131">@model 指令</span><span class="sxs-lookup"><span data-stu-id="28def-131">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="28def-132">`@model` 指令指定传递给 Razor 页面的模型类型。</span><span class="sxs-lookup"><span data-stu-id="28def-132">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="28def-133">在前面的示例中，`@model` 行使 `PageModel` 派生的类可用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="28def-133">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="28def-134">在页面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 帮助程序](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)中使用该模型。</span><span class="sxs-lookup"><span data-stu-id="28def-134">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="28def-135">布局页</span><span class="sxs-lookup"><span data-stu-id="28def-135">The layout page</span></span>

<span data-ttu-id="28def-136">选择菜单链接（“RazorPagesMovie”  、“主页”  和“隐私”  ）。</span><span class="sxs-lookup"><span data-stu-id="28def-136">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="28def-137">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="28def-137">Each page shows the same menu layout.</span></span> <span data-ttu-id="28def-138">菜单布局是在 Pages/Shared/_Layout.cshtml  文件中实现。</span><span class="sxs-lookup"><span data-stu-id="28def-138">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="28def-139">打开 Pages/Shared/_Layout.cshtml  文件。</span><span class="sxs-lookup"><span data-stu-id="28def-139">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="28def-140">[布局](xref:mvc/views/layout)模板允许 HTML 容器具有如下布局：</span><span class="sxs-lookup"><span data-stu-id="28def-140">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="28def-141">在一个位置指定。</span><span class="sxs-lookup"><span data-stu-id="28def-141">Specified in one place.</span></span>
* <span data-ttu-id="28def-142">应用于站点中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="28def-142">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="28def-143">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="28def-143">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="28def-144">`RenderBody` 是显示全部页面专用视图的占位符，已包装  在布局页中。</span><span class="sxs-lookup"><span data-stu-id="28def-144">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="28def-145">例如，选择“隐私”  链接后，Pages/Privacy.cshtml  视图在 `RenderBody` 方法中呈现。</span><span class="sxs-lookup"><span data-stu-id="28def-145">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="28def-146">ViewData 和布局</span><span class="sxs-lookup"><span data-stu-id="28def-146">ViewData and layout</span></span>

<span data-ttu-id="28def-147">考虑来自 Pages/Movies/Index.cshtml  文件中的以下标记：</span><span class="sxs-lookup"><span data-stu-id="28def-147">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="28def-148">前面突出显示的标记是 Razor 转换为 C# 的一个示例。</span><span class="sxs-lookup"><span data-stu-id="28def-148">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="28def-149">`{` 和 `}` 字符括住 C# 代码块。</span><span class="sxs-lookup"><span data-stu-id="28def-149">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="28def-150">`PageModel` 基类包含 `ViewData` 字典属性，可用于添加数据并将其传递到某个视图。</span><span class="sxs-lookup"><span data-stu-id="28def-150">The `PageModel` base class contains a `ViewData` dictionary property that can be used to add data that and pass it to a View.</span></span> <span data-ttu-id="28def-151">可以使用键/值模式将对象添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="28def-151">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="28def-152">在前面的示例中，`"Title"` 属性被添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="28def-152">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="28def-153">`"Title"` 属性用于 Pages/Shared/_Layout.cshtml 文件  。</span><span class="sxs-lookup"><span data-stu-id="28def-153">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="28def-154">以下标记显示 _Layout.cshtml 文件的前几行  。</span><span class="sxs-lookup"><span data-stu-id="28def-154">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="28def-155">行 `@*Markup removed for brevity.*@` 为 Razor 注释。</span><span class="sxs-lookup"><span data-stu-id="28def-155">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="28def-156">与 HTML 注释不同 (`<!-- -->`)，Razor 注释不会发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="28def-156">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="28def-157">更新布局</span><span class="sxs-lookup"><span data-stu-id="28def-157">Update the layout</span></span>

<span data-ttu-id="28def-158">更改 Pages/Shared/_Layout.cshtml 文件中的 `<title>` 元素以显示 Movie 而不是 RazorPagesMovie    。</span><span class="sxs-lookup"><span data-stu-id="28def-158">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="28def-159">在 Pages/Shared/_Layout.cshtml  文件中，查找以下定位点元素。</span><span class="sxs-lookup"><span data-stu-id="28def-159">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="28def-160">将前面的元素替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="28def-160">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="28def-161">前面的定位点元素是一个[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="28def-161">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="28def-162">此处它是[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="28def-162">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="28def-163">`asp-page="/Movies/Index"` 标记帮助程序属性和值可以创建指向 `/Movies/Index` Razor 页面的链接。</span><span class="sxs-lookup"><span data-stu-id="28def-163">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="28def-164">`asp-area` 属性值为空，因此在链接中未使用区域。</span><span class="sxs-lookup"><span data-stu-id="28def-164">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="28def-165">有关详细信息，请参阅[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="28def-165">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="28def-166">保存所做的更改，并通过单击“RpMovie”  链接测试应用。</span><span class="sxs-lookup"><span data-stu-id="28def-166">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="28def-167">如果遇到任何问题，请参阅 GitHub 中的 [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) 文件。</span><span class="sxs-lookup"><span data-stu-id="28def-167">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="28def-168">测试其他链接（“主页”  、“RpMovie”  、“创建”  、“编辑”  和“删除”  ）。</span><span class="sxs-lookup"><span data-stu-id="28def-168">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="28def-169">每个页面都设置有标题，可以在浏览器选项卡中看到标题。将某个页面加入书签时，标题用于该书签。</span><span class="sxs-lookup"><span data-stu-id="28def-169">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="28def-170">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="28def-170">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="28def-171">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的的非英语区域设置，以及支持非美国英语日期格式，必须执行使应用全球化的步骤。</span><span class="sxs-lookup"><span data-stu-id="28def-171">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="28def-172">有关添加十进制逗号的说明，请参阅 [GitHub 问题 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="28def-172">See this [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="28def-173">在 Pages/_ViewStart.cshtml  文件中设置 `Layout` 属性：</span><span class="sxs-lookup"><span data-stu-id="28def-173">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="28def-174">前面的标记针对所有 Razor 文件将布局文件设置为 Pages  文件夹下的 Pages/Shared/_Layout.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="28def-174">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="28def-175">请参阅[布局](xref:razor-pages/index#layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="28def-175">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="28def-176">“创建”页面模型</span><span class="sxs-lookup"><span data-stu-id="28def-176">The Create page model</span></span>

<span data-ttu-id="28def-177">检查 *Pages/Movies/Create.cshtml.cs* 页面模型：</span><span class="sxs-lookup"><span data-stu-id="28def-177">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="28def-178">`OnGet` 方法初始化页面所需的任何状态。</span><span class="sxs-lookup"><span data-stu-id="28def-178">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="28def-179">“创建”页没有任何要初始化的状态，因此返回 `Page`。</span><span class="sxs-lookup"><span data-stu-id="28def-179">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="28def-180">在本教程的后面部分中，将介绍 `OnGet` 初始化状态的示例。</span><span class="sxs-lookup"><span data-stu-id="28def-180">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="28def-181">`Page` 方法创建用于呈现 Create.cshtml  页的 `PageResult` 对象。</span><span class="sxs-lookup"><span data-stu-id="28def-181">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="28def-182">`Movie` 属性使用 `[BindProperty]` 特性来选择加入[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="28def-182">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="28def-183">当“创建”表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="28def-183">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="28def-184">当页面发布表单数据时，运行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="28def-184">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="28def-185">如果不存在任何模型错误，将重新显示表单，以及发布的任何表单数据。</span><span class="sxs-lookup"><span data-stu-id="28def-185">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="28def-186">在发布表单前，可以在客户端捕获到大部分模型错误。</span><span class="sxs-lookup"><span data-stu-id="28def-186">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="28def-187">模型错误的一个示例是，发布的日期字段值无法转换为日期。</span><span class="sxs-lookup"><span data-stu-id="28def-187">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="28def-188">本教程后面讨论了客户端验证和模型验证。</span><span class="sxs-lookup"><span data-stu-id="28def-188">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="28def-189">如果不存在模型错误，将保存数据，并且浏览器会重定向到索引页。</span><span class="sxs-lookup"><span data-stu-id="28def-189">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="28def-190">创建 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="28def-190">The Create Razor Page</span></span>

<span data-ttu-id="28def-191">检查 Pages/Movies/Create.cshtml  Razor 页面文件：</span><span class="sxs-lookup"><span data-stu-id="28def-191">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28def-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28def-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28def-193">Visual Studio 以用于标记帮助程序的特殊加粗字体显示以下标记：</span><span class="sxs-lookup"><span data-stu-id="28def-193">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml 页的 VS17 视图](page/_static/th3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="28def-195">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28def-195">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28def-196">以下标记帮助程序显示在前面的标记中：</span><span class="sxs-lookup"><span data-stu-id="28def-196">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="28def-197">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28def-197">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="28def-198">Visual Studio 以用于标记帮助程序的特殊加粗字体显示以下标记：</span><span class="sxs-lookup"><span data-stu-id="28def-198">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="28def-199">`<form method="post">` 元素是一个[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="28def-199">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="28def-200">表单标记帮助程序会自动包含[防伪令牌](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="28def-200">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="28def-201">基架引擎在模型中为每个字段（ID 除外）创建 Razor 标记，如下所示：</span><span class="sxs-lookup"><span data-stu-id="28def-201">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="28def-202">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)（`<div asp-validation-summary` 和 `<span asp-validation-for`）显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="28def-202">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="28def-203">本系列后面的部分将更详细地讨论有关验证的信息。</span><span class="sxs-lookup"><span data-stu-id="28def-203">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="28def-204">[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) 生成标签描述和 `Title` 属性的 `for` 特性。</span><span class="sxs-lookup"><span data-stu-id="28def-204">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="28def-205">[输入标记帮助程序](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) 使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性并在客户端生成 jQuery 验证所需的 HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="28def-205">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="28def-206">有关标记帮助程序（如 `<form method="post">`）的详细信息，请参阅 [ASP.NET Core 中的标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="28def-206">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28def-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="28def-207">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="28def-208">[上一篇：添加模型](xref:tutorials/razor-pages/model)
> [下一步：数据库](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="28def-208">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="28def-209">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="28def-209">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="28def-210">本教程介绍在[上一教程](xref:tutorials/razor-pages/model)中通过搭建基架创建的 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="28def-210">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="28def-211">[查看或下载](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22)示例。</span><span class="sxs-lookup"><span data-stu-id="28def-211">[View or download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="28def-212">“创建”、“删除”、“详细信息”和“编辑”页面</span><span class="sxs-lookup"><span data-stu-id="28def-212">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="28def-213">检查 Pages/Movies/Index.cshtml.cs  页面模型：</span><span class="sxs-lookup"><span data-stu-id="28def-213">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="28def-214">Razor 页面派生自 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="28def-214">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="28def-215">按照约定，`PageModel` 派生的类称为 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="28def-215">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="28def-216">此构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将 `RazorPagesMovieContext` 添加到页。</span><span class="sxs-lookup"><span data-stu-id="28def-216">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="28def-217">所有已搭建基架的页面都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="28def-217">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="28def-218">请参阅[异步代码](xref:data/ef-rp/intro#asynchronous-code)，了解有关使用实体框架的异步编程的详细信息。</span><span class="sxs-lookup"><span data-stu-id="28def-218">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="28def-219">对页面发出请求时，`OnGetAsync` 方法向 Razor 页面返回影片列表。</span><span class="sxs-lookup"><span data-stu-id="28def-219">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="28def-220">在 Razor 页面上调用 `OnGetAsync` 或 `OnGet` 以初始化页面状态。</span><span class="sxs-lookup"><span data-stu-id="28def-220">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="28def-221">在这种情况下，`OnGetAsync` 将获得影片列表并显示出来。</span><span class="sxs-lookup"><span data-stu-id="28def-221">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="28def-222">当 `OnGet` 返回 `void` 或 `OnGetAsync` 返回 `Task` 时，不使用任何返回方法。</span><span class="sxs-lookup"><span data-stu-id="28def-222">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="28def-223">当返回类型是 `IActionResult` 或 `Task<IActionResult>` 时，必须提供返回语句。</span><span class="sxs-lookup"><span data-stu-id="28def-223">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="28def-224">例如，Pages/Movies/Create.cshtml.cs  `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="28def-224">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<span data-ttu-id="28def-225">检查 Pages/Movies/Index.cshtml<a name="index"></a> Razor 页面  ：</span><span class="sxs-lookup"><span data-stu-id="28def-225"><a name="index"></a> Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="28def-226">Razor 可以从 HTML 转换为 C# 或 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="28def-226">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="28def-227">当 `@` 符号后跟 [Razor 保留关键字](xref:mvc/views/razor#razor-reserved-keywords)时，它会转换为 Razor 特定标记，否则会转换为 C#。</span><span class="sxs-lookup"><span data-stu-id="28def-227">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="28def-228">`@page` Razor 指令将文件转换为一个 MVC 操作，这意味着它可以处理请求。</span><span class="sxs-lookup"><span data-stu-id="28def-228">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="28def-229">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="28def-229">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="28def-230">`@page` 是转换到 Razor 特定标记的一个示例。</span><span class="sxs-lookup"><span data-stu-id="28def-230">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="28def-231">有关详细信息，请参阅 [Razor 语法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="28def-231">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="28def-232">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="28def-232">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="28def-233">`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="28def-233">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="28def-234">检查 Lambda 表达式（而非求值）。</span><span class="sxs-lookup"><span data-stu-id="28def-234">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="28def-235">这意味着当 `model`、`model.Movie` 或 `model.Movie[0]` 为 `null` 或为空时，不会存在任何访问冲突。</span><span class="sxs-lookup"><span data-stu-id="28def-235">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="28def-236">对 Lambda 表达式求值时（例如，使用 `@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="28def-236">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="28def-237">@model 指令</span><span class="sxs-lookup"><span data-stu-id="28def-237">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="28def-238">`@model` 指令指定传递给 Razor 页面的模型类型。</span><span class="sxs-lookup"><span data-stu-id="28def-238">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="28def-239">在前面的示例中，`@model` 行使 `PageModel` 派生的类可用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="28def-239">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="28def-240">在页面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 帮助程序](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)中使用该模型。</span><span class="sxs-lookup"><span data-stu-id="28def-240">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="28def-241">布局页</span><span class="sxs-lookup"><span data-stu-id="28def-241">The layout page</span></span>

<span data-ttu-id="28def-242">选择菜单链接（“RazorPagesMovie”  、“主页”  和“隐私”  ）。</span><span class="sxs-lookup"><span data-stu-id="28def-242">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="28def-243">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="28def-243">Each page shows the same menu layout.</span></span> <span data-ttu-id="28def-244">菜单布局是在 Pages/Shared/_Layout.cshtml  文件中实现。</span><span class="sxs-lookup"><span data-stu-id="28def-244">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="28def-245">打开 Pages/Shared/_Layout.cshtml  文件。</span><span class="sxs-lookup"><span data-stu-id="28def-245">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="28def-246">[布局](xref:mvc/views/layout)模板使你能够在一个位置指定网站的 HTML 容器布局，然后将它应用到网站中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="28def-246">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="28def-247">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="28def-247">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="28def-248">`RenderBody` 是显示所创建的全部页面专用视图的占位符，已包装  在布局页中。</span><span class="sxs-lookup"><span data-stu-id="28def-248">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="28def-249">例如，如果选择“隐私”  链接，Pages/Privacy.cshtml  视图在 `RenderBody` 方法中呈现。</span><span class="sxs-lookup"><span data-stu-id="28def-249">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="28def-250">ViewData 和布局</span><span class="sxs-lookup"><span data-stu-id="28def-250">ViewData and layout</span></span>

<span data-ttu-id="28def-251">考虑来自 Pages/Movies/Index.cshtml  文件中的以下代码：</span><span class="sxs-lookup"><span data-stu-id="28def-251">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="28def-252">前面突出显示的代码是 Razor 转换为 C# 的一个示例。</span><span class="sxs-lookup"><span data-stu-id="28def-252">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="28def-253">`{` 和 `}` 字符括住 C# 代码块。</span><span class="sxs-lookup"><span data-stu-id="28def-253">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="28def-254">`PageModel` 基类具有 `ViewData` 字典属性，可用于添加要传递到某个视图的数据。</span><span class="sxs-lookup"><span data-stu-id="28def-254">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="28def-255">可以使用键/值模式将对象添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="28def-255">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="28def-256">在前面的示例中，“Title”属性被添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="28def-256">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="28def-257">“Title”属性用于 Pages/Shared/_Layout.cshtml 文件  。</span><span class="sxs-lookup"><span data-stu-id="28def-257">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="28def-258">以下标记显示 _Layout.cshtml 文件的前几行  。</span><span class="sxs-lookup"><span data-stu-id="28def-258">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="28def-259">行 `@*Markup removed for brevity.*@` 是不会出现在布局文件中的 Razor 注释。</span><span class="sxs-lookup"><span data-stu-id="28def-259">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="28def-260">与 HTML 注释不同 (`<!-- -->`)，Razor 注释不会发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="28def-260">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="28def-261">更新布局</span><span class="sxs-lookup"><span data-stu-id="28def-261">Update the layout</span></span>

<span data-ttu-id="28def-262">更改 Pages/Shared/_Layout.cshtml 文件中的 `<title>` 元素以显示 Movie 而不是 RazorPagesMovie    。</span><span class="sxs-lookup"><span data-stu-id="28def-262">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="28def-263">在 Pages/Shared/_Layout.cshtml  文件中，查找以下定位点元素。</span><span class="sxs-lookup"><span data-stu-id="28def-263">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="28def-264">将前面的元素替换为以下标记。</span><span class="sxs-lookup"><span data-stu-id="28def-264">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="28def-265">前面的定位点元素是一个[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="28def-265">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="28def-266">此处它是[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="28def-266">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="28def-267">`asp-page="/Movies/Index"` 标记帮助程序属性和值可以创建指向 `/Movies/Index` Razor 页面的链接。</span><span class="sxs-lookup"><span data-stu-id="28def-267">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="28def-268">`asp-area` 属性值为空，因此在链接中未使用区域。</span><span class="sxs-lookup"><span data-stu-id="28def-268">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="28def-269">有关详细信息，请参阅[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="28def-269">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="28def-270">保存所做的更改，并通过单击“RpMovie”  链接测试应用。</span><span class="sxs-lookup"><span data-stu-id="28def-270">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="28def-271">如果遇到任何问题，请参阅 GitHub 中的 [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) 文件。</span><span class="sxs-lookup"><span data-stu-id="28def-271">See the [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="28def-272">测试其他链接（“主页”  、“RpMovie”  、“创建”  、“编辑”  和“删除”  ）。</span><span class="sxs-lookup"><span data-stu-id="28def-272">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="28def-273">每个页面都设置有标题，可以在浏览器选项卡中看到标题。将某个页面加入书签时，标题用于该书签。</span><span class="sxs-lookup"><span data-stu-id="28def-273">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="28def-274">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="28def-274">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="28def-275">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的的非英语区域设置，以及支持非美国英语日期格式，必须执行使应用全球化的步骤。</span><span class="sxs-lookup"><span data-stu-id="28def-275">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="28def-276">有关添加十进制逗号的说明，请参阅 [GitHub 问题 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="28def-276">This [GitHub issue 4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="28def-277">在 Pages/_ViewStart.cshtml  文件中设置 `Layout` 属性：</span><span class="sxs-lookup"><span data-stu-id="28def-277">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="28def-278">前面的标记针对所有 Razor 文件将布局文件设置为 Pages  文件夹下的 Pages/Shared/_Layout.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="28def-278">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="28def-279">请参阅[布局](xref:razor-pages/index#layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="28def-279">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="28def-280">“创建”页面模型</span><span class="sxs-lookup"><span data-stu-id="28def-280">The Create page model</span></span>

<span data-ttu-id="28def-281">检查 *Pages/Movies/Create.cshtml.cs* 页面模型：</span><span class="sxs-lookup"><span data-stu-id="28def-281">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="28def-282">`OnGet` 方法初始化页面所需的任何状态。</span><span class="sxs-lookup"><span data-stu-id="28def-282">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="28def-283">“创建”页没有任何要初始化的状态，因此返回 `Page`。</span><span class="sxs-lookup"><span data-stu-id="28def-283">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="28def-284">教程的后面部分将介绍 `OnGet` 方法初始化状态。</span><span class="sxs-lookup"><span data-stu-id="28def-284">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="28def-285">`Page` 方法创建用于呈现 Create.cshtml  页的 `PageResult` 对象。</span><span class="sxs-lookup"><span data-stu-id="28def-285">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="28def-286">`Movie` 属性使用 `[BindProperty]` 特性来选择加入[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="28def-286">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="28def-287">当“创建”表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="28def-287">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="28def-288">当页面发布表单数据时，运行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="28def-288">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="28def-289">如果不存在任何模型错误，将重新显示表单，以及发布的任何表单数据。</span><span class="sxs-lookup"><span data-stu-id="28def-289">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="28def-290">在发布表单前，可以在客户端捕获到大部分模型错误。</span><span class="sxs-lookup"><span data-stu-id="28def-290">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="28def-291">模型错误的一个示例是，发布的日期字段值无法转换为日期。</span><span class="sxs-lookup"><span data-stu-id="28def-291">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="28def-292">本教程后面讨论了客户端验证和模型验证。</span><span class="sxs-lookup"><span data-stu-id="28def-292">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="28def-293">如果不存在模型错误，将保存数据，并且浏览器会重定向到索引页。</span><span class="sxs-lookup"><span data-stu-id="28def-293">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="28def-294">创建 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="28def-294">The Create Razor Page</span></span>

<span data-ttu-id="28def-295">检查 Pages/Movies/Create.cshtml  Razor 页面文件：</span><span class="sxs-lookup"><span data-stu-id="28def-295">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28def-296">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28def-296">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="28def-297">Visual Studio 以用于标记帮助程序的特殊加粗字体显示 `<form method="post">` 标记：</span><span class="sxs-lookup"><span data-stu-id="28def-297">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml 页的 VS17 视图](page/_static/th.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="28def-299">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28def-299">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="28def-300">有关标记帮助程序（如 `<form method="post">`）的详细信息，请参阅 [ASP.NET Core 中的标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="28def-300">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="28def-301">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28def-301">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="28def-302">Visual Studio for Mac 以用于标记帮助程序的特殊加粗字体显示 `<form method="post">` 标记。</span><span class="sxs-lookup"><span data-stu-id="28def-302">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="28def-303">`<form method="post">` 元素是一个[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="28def-303">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="28def-304">表单标记帮助程序会自动包含[防伪令牌](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="28def-304">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="28def-305">基架引擎在模型中为每个字段（ID 除外）创建 Razor 标记，如下所示：</span><span class="sxs-lookup"><span data-stu-id="28def-305">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="28def-306">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)（`<div asp-validation-summary` 和 `<span asp-validation-for`）显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="28def-306">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="28def-307">本系列后面的部分将更详细地讨论有关验证的信息。</span><span class="sxs-lookup"><span data-stu-id="28def-307">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="28def-308">[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) 生成标签描述和 `Title` 属性的 `for` 特性。</span><span class="sxs-lookup"><span data-stu-id="28def-308">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="28def-309">[输入标记帮助程序](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) 使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性并在客户端生成 jQuery 验证所需的 HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="28def-309">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28def-310">其他资源</span><span class="sxs-lookup"><span data-stu-id="28def-310">Additional resources</span></span>

* [<span data-ttu-id="28def-311">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="28def-311">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="28def-312">[上一篇：添加模型](xref:tutorials/razor-pages/model)
> [下一步：数据库](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="28def-312">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
