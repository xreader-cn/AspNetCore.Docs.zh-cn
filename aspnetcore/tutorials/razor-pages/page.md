---
title: 第 3 部分，ASP.NET Core 中已搭建基架的 Razor 页面
author: rick-anderson
description: Razor 页面教程系列的第 3 部分。
ms.author: riande
ms.date: 08/17/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 898f3be045755a0ee14c738b6ffc104408ded7b6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407767"
---
# <a name="part-3-scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="bd076-103">第 3 部分，ASP.NET Core 中已搭建基架的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="bd076-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd076-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd076-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bd076-105">本教程介绍[上一教程](xref:tutorials/razor-pages/model)中通过搭建基架创建的 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bd076-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="bd076-106">“创建”、“删除”、“详细信息”和“编辑”页面</span><span class="sxs-lookup"><span data-stu-id="bd076-106">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="bd076-107">检查 Pages/Movies/Index.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="bd076-107">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor<span data-ttu-id="bd076-108"> 页面派生自 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="bd076-108"> Pages are derived from `PageModel`.</span></span> <span data-ttu-id="bd076-109">按照约定，`PageModel` 派生的类称为 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="bd076-109">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="bd076-110">此构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将 `RazorPagesMovieContext` 添加到页。</span><span class="sxs-lookup"><span data-stu-id="bd076-110">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="bd076-111">所有已搭建基架的页面都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="bd076-111">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="bd076-112">请参阅[异步代码](xref:data/ef-rp/intro#asynchronous-code)，了解有关使用实体框架的异步编程的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bd076-112">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="bd076-113">对页面发出请求时，`OnGetAsync` 方法向 Razor 页面返回影片列表。</span><span class="sxs-lookup"><span data-stu-id="bd076-113">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="bd076-114">调用 `OnGetAsync` 或 `OnGet` 以初始化页面的状态。</span><span class="sxs-lookup"><span data-stu-id="bd076-114">`OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="bd076-115">在这种情况下，`OnGetAsync` 将获得影片列表并显示出来。</span><span class="sxs-lookup"><span data-stu-id="bd076-115">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="bd076-116">当 `OnGet` 返回 `void` 或 `OnGetAsync` 返回 `Task` 时，不使用任何返回语句。</span><span class="sxs-lookup"><span data-stu-id="bd076-116">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return statement is used.</span></span> <span data-ttu-id="bd076-117">当返回类型是 `IActionResult` 或 `Task<IActionResult>` 时，必须提供返回语句。</span><span class="sxs-lookup"><span data-stu-id="bd076-117">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="bd076-118">例如，Pages/Movies/Create.cshtml.cs `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="bd076-118">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="bd076-119">检查 Pages/Movies/Index.cshtml Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="bd076-119">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor<span data-ttu-id="bd076-120"> 可以从 HTML 转换为 C# 或 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="bd076-120"> can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="bd076-121">当 `@` 符号后跟 [Razor 保留关键字](xref:mvc/views/razor#razor-reserved-keywords)时，它会转换为 Razor 特定标记，否则会转换为 C#。</span><span class="sxs-lookup"><span data-stu-id="bd076-121">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="bd076-122">@page 指令</span><span class="sxs-lookup"><span data-stu-id="bd076-122">The @page directive</span></span>

<span data-ttu-id="bd076-123">`@page` Razor 指令将文件转换为一个 MVC 操作，这意味着它可以处理请求。</span><span class="sxs-lookup"><span data-stu-id="bd076-123">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="bd076-124">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="bd076-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bd076-125">`@page` 是转换为 Razor 特定标记的示例。</span><span class="sxs-lookup"><span data-stu-id="bd076-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="bd076-126">有关详细信息，请参阅 [Razor 语法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="bd076-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="bd076-127">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="bd076-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="bd076-128">`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="bd076-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="bd076-129">检查 Lambda 表达式（而非求值）。</span><span class="sxs-lookup"><span data-stu-id="bd076-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="bd076-130">这意味着当 `model`、`model.Movie` 或 `model.Movie[0]` 为 `null` 或为空时，不会存在任何访问冲突。</span><span class="sxs-lookup"><span data-stu-id="bd076-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="bd076-131">对 Lambda 表达式求值时（例如，使用 `@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="bd076-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="bd076-132">@model 指令</span><span class="sxs-lookup"><span data-stu-id="bd076-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="bd076-133">`@model` 指令指定传递到 Razor 页面的模型类型。</span><span class="sxs-lookup"><span data-stu-id="bd076-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="bd076-134">在前面的示例中，`@model` 行使 `PageModel` 派生的类可用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bd076-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="bd076-135">在页面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 帮助程序](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)中使用该模型。</span><span class="sxs-lookup"><span data-stu-id="bd076-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="bd076-136">布局页</span><span class="sxs-lookup"><span data-stu-id="bd076-136">The layout page</span></span>

<span data-ttu-id="bd076-137">选择菜单链接（“RazorPagesMovie”、“主页”和“隐私”）。</span><span class="sxs-lookup"><span data-stu-id="bd076-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="bd076-138">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="bd076-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="bd076-139">菜单布局是在 Pages/Shared/_Layout.cshtml 文件中实现。</span><span class="sxs-lookup"><span data-stu-id="bd076-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bd076-140">打开 Pages/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="bd076-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bd076-141">[布局](xref:mvc/views/layout)模板允许 HTML 容器具有如下布局：</span><span class="sxs-lookup"><span data-stu-id="bd076-141">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="bd076-142">在一个位置指定。</span><span class="sxs-lookup"><span data-stu-id="bd076-142">Specified in one place.</span></span>
* <span data-ttu-id="bd076-143">应用于站点中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="bd076-143">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="bd076-144">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="bd076-144">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="bd076-145">`RenderBody` 是显示全部页面专用视图的占位符，已包装在布局页中。</span><span class="sxs-lookup"><span data-stu-id="bd076-145">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="bd076-146">例如，选择“隐私”链接后，Pages/Privacy.cshtml 视图在 `RenderBody` 方法中呈现。</span><span class="sxs-lookup"><span data-stu-id="bd076-146">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="bd076-147">ViewData 和布局</span><span class="sxs-lookup"><span data-stu-id="bd076-147">ViewData and layout</span></span>

<span data-ttu-id="bd076-148">考虑来自 Pages/Movies/Index.cshtml 文件中的以下标记：</span><span class="sxs-lookup"><span data-stu-id="bd076-148">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="bd076-149">前面突出显示的标记是 Razor 转换为 C# 的一个示例。</span><span class="sxs-lookup"><span data-stu-id="bd076-149">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="bd076-150">`{` 和 `}` 字符括住 C# 代码块。</span><span class="sxs-lookup"><span data-stu-id="bd076-150">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="bd076-151">`PageModel` 基类包含 `ViewData` 字典属性，可用于将数据传递到某个视图。</span><span class="sxs-lookup"><span data-stu-id="bd076-151">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="bd076-152">可以使用键/值模式将对象添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="bd076-152">Objects are added to the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="bd076-153">在前面的示例中，`"Title"` 属性被添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="bd076-153">In the preceding sample, the `"Title"` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="bd076-154">`"Title"` 属性用于 Pages/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="bd076-154">The `"Title"` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bd076-155">以下标记显示 _Layout.cshtml 文件的前几行。</span><span class="sxs-lookup"><span data-stu-id="bd076-155">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="bd076-156">行 `@*Markup removed for brevity.*@` 是 Razor 注释。</span><span class="sxs-lookup"><span data-stu-id="bd076-156">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="bd076-157">与 HTML 注释 (`<!-- -->`) 不同，Razor 注释不会发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="bd076-157">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="bd076-158">更新布局</span><span class="sxs-lookup"><span data-stu-id="bd076-158">Update the layout</span></span>

<span data-ttu-id="bd076-159">更改 Pages/Shared/_Layout.cshtml 文件中的 `<title>` 元素以显示 Movie 而不是 RazorPagesMovie 。</span><span class="sxs-lookup"><span data-stu-id="bd076-159">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="bd076-160">在 Pages/Shared/_Layout.cshtml 文件中，查找以下定位点元素。</span><span class="sxs-lookup"><span data-stu-id="bd076-160">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="bd076-161">将前面的元素替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="bd076-161">Replace the preceding element with the following markup:</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="bd076-162">前面的定位点元素是一个[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="bd076-162">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bd076-163">此处它是[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="bd076-163">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="bd076-164">`asp-page="/Movies/Index"` 标记帮助程序属性和值可以创建指向 `/Movies/Index` Razor 页面的链接。</span><span class="sxs-lookup"><span data-stu-id="bd076-164">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="bd076-165">`asp-area` 属性值为空，因此在链接中未使用区域。</span><span class="sxs-lookup"><span data-stu-id="bd076-165">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="bd076-166">有关详细信息，请参阅[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="bd076-166">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="bd076-167">保存所做的更改，并通过单击“RpMovie”链接测试应用。</span><span class="sxs-lookup"><span data-stu-id="bd076-167">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="bd076-168">如果遇到任何问题，请参阅 GitHub 中的 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) 文件。</span><span class="sxs-lookup"><span data-stu-id="bd076-168">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="bd076-169">测试其他链接（“主页”、“RpMovie”、“创建”、“编辑”和“删除”）。</span><span class="sxs-lookup"><span data-stu-id="bd076-169">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="bd076-170">每个页面都设置有标题，可以在浏览器选项卡中看到标题。将某个页面加入书签时，标题用于该书签。</span><span class="sxs-lookup"><span data-stu-id="bd076-170">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="bd076-171">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="bd076-171">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bd076-172">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的的非英语区域设置，以及支持非美国英语日期格式，必须执行使应用全球化的步骤。</span><span class="sxs-lookup"><span data-stu-id="bd076-172">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="bd076-173">有关添加十进制逗号的说明，请参阅 [GitHub 问题 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="bd076-173">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="bd076-174">在 Pages/_ViewStart.cshtml 文件中设置 `Layout` 属性：</span><span class="sxs-lookup"><span data-stu-id="bd076-174">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bd076-175">前面的标记针对 Pages 文件夹下的所有 Razor 文件将布局文件设置为 Pages/Shared/_Layout.cshtml。 </span><span class="sxs-lookup"><span data-stu-id="bd076-175">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="bd076-176">请参阅[布局](xref:razor-pages/index#layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="bd076-176">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="bd076-177">“创建”页面模型</span><span class="sxs-lookup"><span data-stu-id="bd076-177">The Create page model</span></span>

<span data-ttu-id="bd076-178">检查 *Pages/Movies/Create.cshtml.cs* 页面模型：</span><span class="sxs-lookup"><span data-stu-id="bd076-178">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="bd076-179">`OnGet` 方法初始化页面所需的任何状态。</span><span class="sxs-lookup"><span data-stu-id="bd076-179">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="bd076-180">“创建”页没有任何要初始化的状态，因此返回 `Page`。</span><span class="sxs-lookup"><span data-stu-id="bd076-180">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="bd076-181">在本教程的后面部分中，将介绍 `OnGet` 初始化状态的示例。</span><span class="sxs-lookup"><span data-stu-id="bd076-181">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="bd076-182">`Page` 方法创建用于呈现 Create.cshtml 页的 `PageResult` 对象。</span><span class="sxs-lookup"><span data-stu-id="bd076-182">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="bd076-183">`Movie` 属性使用 `[BindProperty]` 特性来选择加入[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="bd076-183">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="bd076-184">当“创建”表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="bd076-184">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="bd076-185">当页面发布表单数据时，运行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="bd076-185">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="bd076-186">如果不存在任何模型错误，将重新显示表单，以及发布的任何表单数据。</span><span class="sxs-lookup"><span data-stu-id="bd076-186">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="bd076-187">在发布表单前，可以在客户端捕获到大部分模型错误。</span><span class="sxs-lookup"><span data-stu-id="bd076-187">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="bd076-188">模型错误的一个示例是，发布的日期字段值无法转换为日期。</span><span class="sxs-lookup"><span data-stu-id="bd076-188">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="bd076-189">本教程后面讨论了客户端验证和模型验证。</span><span class="sxs-lookup"><span data-stu-id="bd076-189">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="bd076-190">如果不存在模型错误，将保存数据，并且浏览器会重定向到索引页。</span><span class="sxs-lookup"><span data-stu-id="bd076-190">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="bd076-191">“创建 Razor”页面</span><span class="sxs-lookup"><span data-stu-id="bd076-191">The Create Razor Page</span></span>

<span data-ttu-id="bd076-192">检查 Pages/Movies/Create.cshtml Razor 页面文件：</span><span class="sxs-lookup"><span data-stu-id="bd076-192">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="bd076-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd076-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd076-194">Visual Studio 以用于标记帮助程序的特殊加粗字体显示以下标记：</span><span class="sxs-lookup"><span data-stu-id="bd076-194">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml 页的 VS17 视图](page/_static/th3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd076-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd076-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bd076-197">以下标记帮助程序显示在前面的标记中：</span><span class="sxs-lookup"><span data-stu-id="bd076-197">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd076-198">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="bd076-198">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bd076-199">Visual Studio 以用于标记帮助程序的特殊加粗字体显示以下标记：</span><span class="sxs-lookup"><span data-stu-id="bd076-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="bd076-200">`<form method="post">` 元素是一个[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="bd076-200">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="bd076-201">表单标记帮助程序会自动包含[防伪令牌](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="bd076-201">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="bd076-202">基架引擎在模型中为每个字段（ID 除外）创建 Razor 标记，如下所示：</span><span class="sxs-lookup"><span data-stu-id="bd076-202">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="bd076-203">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)（`<div asp-validation-summary` 和 `<span asp-validation-for`）显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="bd076-203">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="bd076-204">本系列后面的部分将更详细地讨论有关验证的信息。</span><span class="sxs-lookup"><span data-stu-id="bd076-204">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="bd076-205">[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) 生成标签描述和 `Title` 属性的 `for` 特性。</span><span class="sxs-lookup"><span data-stu-id="bd076-205">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="bd076-206">[输入标记帮助程序](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) 使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性并在客户端生成 jQuery 验证所需的 HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="bd076-206">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="bd076-207">有关标记帮助程序（如 `<form method="post">`）的详细信息，请参阅 [ASP.NET Core 中的标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="bd076-207">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd076-208">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd076-208">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bd076-209">[上一篇：添加模型](xref:tutorials/razor-pages/model)
> [下一步：数据库](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="bd076-209">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd076-210">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd076-210">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bd076-211">本教程介绍[上一教程](xref:tutorials/razor-pages/model)中通过搭建基架创建的 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bd076-211">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="bd076-212">[查看或下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22)示例。</span><span class="sxs-lookup"><span data-stu-id="bd076-212">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="bd076-213">“创建”、“删除”、“详细信息”和“编辑”页面</span><span class="sxs-lookup"><span data-stu-id="bd076-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="bd076-214">检查 Pages/Movies/Index.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="bd076-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor<span data-ttu-id="bd076-215"> 页面派生自 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="bd076-215"> Pages are derived from `PageModel`.</span></span> <span data-ttu-id="bd076-216">按照约定，`PageModel` 派生的类称为 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="bd076-216">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="bd076-217">此构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将 `RazorPagesMovieContext` 添加到页。</span><span class="sxs-lookup"><span data-stu-id="bd076-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="bd076-218">所有已搭建基架的页面都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="bd076-218">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="bd076-219">请参阅[异步代码](xref:data/ef-rp/intro#asynchronous-code)，了解有关使用实体框架的异步编程的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bd076-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="bd076-220">对页面发出请求时，`OnGetAsync` 方法向 Razor 页面返回影片列表。</span><span class="sxs-lookup"><span data-stu-id="bd076-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="bd076-221">`OnGetAsync` 或 `OnGet` 在 Razor 页面上调用，以初始化该页面的状态。</span><span class="sxs-lookup"><span data-stu-id="bd076-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="bd076-222">在这种情况下，`OnGetAsync` 将获得影片列表并显示出来。</span><span class="sxs-lookup"><span data-stu-id="bd076-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="bd076-223">当 `OnGet` 返回 `void` 或 `OnGetAsync` 返回 `Task` 时，不使用任何返回方法。</span><span class="sxs-lookup"><span data-stu-id="bd076-223">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="bd076-224">当返回类型是 `IActionResult` 或 `Task<IActionResult>` 时，必须提供返回语句。</span><span class="sxs-lookup"><span data-stu-id="bd076-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="bd076-225">例如，Pages/Movies/Create.cshtml.cs `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="bd076-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="bd076-226">检查 Pages/Movies/Index.cshtml Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="bd076-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor<span data-ttu-id="bd076-227"> 可以从 HTML 转换为 C# 或 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="bd076-227"> can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="bd076-228">当 `@` 符号后跟 [Razor 保留关键字](xref:mvc/views/razor#razor-reserved-keywords)时，它会转换为 Razor 特定标记，否则会转换为 C#。</span><span class="sxs-lookup"><span data-stu-id="bd076-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="bd076-229">`@page` Razor 指令将文件转换为一个 MVC 操作，这意味着它可以处理请求。</span><span class="sxs-lookup"><span data-stu-id="bd076-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="bd076-230">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="bd076-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bd076-231">`@page` 是转换为 Razor 特定标记的示例。</span><span class="sxs-lookup"><span data-stu-id="bd076-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="bd076-232">有关详细信息，请参阅 [Razor 语法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="bd076-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="bd076-233">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="bd076-233">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="bd076-234">`DisplayNameFor` HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="bd076-234">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="bd076-235">检查 Lambda 表达式（而非求值）。</span><span class="sxs-lookup"><span data-stu-id="bd076-235">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="bd076-236">这意味着当 `model`、`model.Movie` 或 `model.Movie[0]` 为 `null` 或为空时，不会存在任何访问冲突。</span><span class="sxs-lookup"><span data-stu-id="bd076-236">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="bd076-237">对 Lambda 表达式求值时（例如，使用 `@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="bd076-237">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="bd076-238">@model 指令</span><span class="sxs-lookup"><span data-stu-id="bd076-238">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="bd076-239">`@model` 指令指定传递到 Razor 页面的模型类型。</span><span class="sxs-lookup"><span data-stu-id="bd076-239">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="bd076-240">在前面的示例中，`@model` 行使 `PageModel` 派生的类可用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bd076-240">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="bd076-241">在页面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 帮助程序](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)中使用该模型。</span><span class="sxs-lookup"><span data-stu-id="bd076-241">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="bd076-242">布局页</span><span class="sxs-lookup"><span data-stu-id="bd076-242">The layout page</span></span>

<span data-ttu-id="bd076-243">选择菜单链接（“RazorPagesMovie”、“主页”和“隐私”）。</span><span class="sxs-lookup"><span data-stu-id="bd076-243">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="bd076-244">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="bd076-244">Each page shows the same menu layout.</span></span> <span data-ttu-id="bd076-245">菜单布局是在 Pages/Shared/_Layout.cshtml 文件中实现。</span><span class="sxs-lookup"><span data-stu-id="bd076-245">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bd076-246">打开 Pages/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="bd076-246">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bd076-247">[布局](xref:mvc/views/layout)模板使你能够在一个位置指定网站的 HTML 容器布局，然后将它应用到网站中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="bd076-247">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="bd076-248">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="bd076-248">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="bd076-249">`RenderBody` 是显示所创建的全部页面专用视图的占位符，已包装在布局页中。</span><span class="sxs-lookup"><span data-stu-id="bd076-249">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="bd076-250">例如，如果选择“隐私”链接，Pages/Privacy.cshtml 视图在 `RenderBody` 方法中呈现。</span><span class="sxs-lookup"><span data-stu-id="bd076-250">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="bd076-251">ViewData 和布局</span><span class="sxs-lookup"><span data-stu-id="bd076-251">ViewData and layout</span></span>

<span data-ttu-id="bd076-252">考虑来自 Pages/Movies/Index.cshtml 文件中的以下代码：</span><span class="sxs-lookup"><span data-stu-id="bd076-252">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="bd076-253">前面突出显示的代码是 Razor 转换为 C# 的一个示例。</span><span class="sxs-lookup"><span data-stu-id="bd076-253">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="bd076-254">`{` 和 `}` 字符括住 C# 代码块。</span><span class="sxs-lookup"><span data-stu-id="bd076-254">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="bd076-255">`PageModel` 基类具有 `ViewData` 字典属性，可用于添加要传递到某个视图的数据。</span><span class="sxs-lookup"><span data-stu-id="bd076-255">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="bd076-256">可以使用键/值模式将对象添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="bd076-256">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="bd076-257">在前面的示例中，“Title”属性被添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="bd076-257">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="bd076-258">“Title”属性用于 Pages/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="bd076-258">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bd076-259">以下标记显示 _Layout.cshtml 文件的前几行。</span><span class="sxs-lookup"><span data-stu-id="bd076-259">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="bd076-260">行 `@*Markup removed for brevity.*@` 是不会出现在布局文件中的 Razor 注释。</span><span class="sxs-lookup"><span data-stu-id="bd076-260">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="bd076-261">与 HTML 注释 (`<!-- -->`) 不同，Razor 注释不会发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="bd076-261">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="bd076-262">更新布局</span><span class="sxs-lookup"><span data-stu-id="bd076-262">Update the layout</span></span>

<span data-ttu-id="bd076-263">更改 Pages/Shared/_Layout.cshtml 文件中的 `<title>` 元素以显示 Movie 而不是 RazorPagesMovie 。</span><span class="sxs-lookup"><span data-stu-id="bd076-263">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="bd076-264">在 Pages/Shared/_Layout.cshtml 文件中，查找以下定位点元素。</span><span class="sxs-lookup"><span data-stu-id="bd076-264">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="bd076-265">将前面的元素替换为以下标记。</span><span class="sxs-lookup"><span data-stu-id="bd076-265">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="bd076-266">前面的定位点元素是一个[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="bd076-266">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bd076-267">此处它是[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="bd076-267">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="bd076-268">`asp-page="/Movies/Index"` 标记帮助程序属性和值可以创建指向 `/Movies/Index` Razor 页面的链接。</span><span class="sxs-lookup"><span data-stu-id="bd076-268">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="bd076-269">`asp-area` 属性值为空，因此在链接中未使用区域。</span><span class="sxs-lookup"><span data-stu-id="bd076-269">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="bd076-270">有关详细信息，请参阅[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="bd076-270">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="bd076-271">保存所做的更改，并通过单击“RpMovie”链接测试应用。</span><span class="sxs-lookup"><span data-stu-id="bd076-271">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="bd076-272">如果遇到任何问题，请参阅 GitHub 中的 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) 文件。</span><span class="sxs-lookup"><span data-stu-id="bd076-272">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="bd076-273">测试其他链接（“主页”、“RpMovie”、“创建”、“编辑”和“删除”）。</span><span class="sxs-lookup"><span data-stu-id="bd076-273">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="bd076-274">每个页面都设置有标题，可以在浏览器选项卡中看到标题。将某个页面加入书签时，标题用于该书签。</span><span class="sxs-lookup"><span data-stu-id="bd076-274">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="bd076-275">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="bd076-275">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bd076-276">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的的非英语区域设置，以及支持非美国英语日期格式，必须执行使应用全球化的步骤。</span><span class="sxs-lookup"><span data-stu-id="bd076-276">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="bd076-277">有关添加十进制逗号的说明，请参阅 [GitHub 问题 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="bd076-277">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="bd076-278">在 Pages/_ViewStart.cshtml 文件中设置 `Layout` 属性：</span><span class="sxs-lookup"><span data-stu-id="bd076-278">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bd076-279">前面的标记针对 Pages 文件夹下的所有 Razor 文件将布局文件设置为 Pages/Shared/_Layout.cshtml。 </span><span class="sxs-lookup"><span data-stu-id="bd076-279">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="bd076-280">请参阅[布局](xref:razor-pages/index#layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="bd076-280">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="bd076-281">“创建”页面模型</span><span class="sxs-lookup"><span data-stu-id="bd076-281">The Create page model</span></span>

<span data-ttu-id="bd076-282">检查 *Pages/Movies/Create.cshtml.cs* 页面模型：</span><span class="sxs-lookup"><span data-stu-id="bd076-282">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="bd076-283">`OnGet` 方法初始化页面所需的任何状态。</span><span class="sxs-lookup"><span data-stu-id="bd076-283">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="bd076-284">“创建”页没有任何要初始化的状态，因此返回 `Page`。</span><span class="sxs-lookup"><span data-stu-id="bd076-284">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="bd076-285">教程的后面部分将介绍 `OnGet` 方法初始化状态。</span><span class="sxs-lookup"><span data-stu-id="bd076-285">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="bd076-286">`Page` 方法创建用于呈现 Create.cshtml 页的 `PageResult` 对象。</span><span class="sxs-lookup"><span data-stu-id="bd076-286">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="bd076-287">`Movie` 属性使用 `[BindProperty]` 特性来选择加入[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="bd076-287">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="bd076-288">当“创建”表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="bd076-288">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="bd076-289">当页面发布表单数据时，运行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="bd076-289">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="bd076-290">如果不存在任何模型错误，将重新显示表单，以及发布的任何表单数据。</span><span class="sxs-lookup"><span data-stu-id="bd076-290">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="bd076-291">在发布表单前，可以在客户端捕获到大部分模型错误。</span><span class="sxs-lookup"><span data-stu-id="bd076-291">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="bd076-292">模型错误的一个示例是，发布的日期字段值无法转换为日期。</span><span class="sxs-lookup"><span data-stu-id="bd076-292">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="bd076-293">本教程后面讨论了客户端验证和模型验证。</span><span class="sxs-lookup"><span data-stu-id="bd076-293">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="bd076-294">如果不存在模型错误，将保存数据，并且浏览器会重定向到索引页。</span><span class="sxs-lookup"><span data-stu-id="bd076-294">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="bd076-295">“创建 Razor”页面</span><span class="sxs-lookup"><span data-stu-id="bd076-295">The Create Razor Page</span></span>

<span data-ttu-id="bd076-296">检查 Pages/Movies/Create.cshtml Razor 页面文件：</span><span class="sxs-lookup"><span data-stu-id="bd076-296">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="bd076-297">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd076-297">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd076-298">Visual Studio 以用于标记帮助程序的特殊加粗字体显示 `<form method="post">` 标记：</span><span class="sxs-lookup"><span data-stu-id="bd076-298">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml 页的 VS17 视图](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd076-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd076-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bd076-301">有关标记帮助程序（如 `<form method="post">`）的详细信息，请参阅 [ASP.NET Core 中的标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="bd076-301">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd076-302">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="bd076-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bd076-303">Visual Studio for Mac 以用于标记帮助程序的特殊加粗字体显示 `<form method="post">` 标记。</span><span class="sxs-lookup"><span data-stu-id="bd076-303">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="bd076-304">`<form method="post">` 元素是一个[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="bd076-304">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="bd076-305">表单标记帮助程序会自动包含[防伪令牌](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="bd076-305">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="bd076-306">基架引擎在模型中为每个字段（ID 除外）创建 Razor 标记，如下所示：</span><span class="sxs-lookup"><span data-stu-id="bd076-306">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="bd076-307">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)（`<div asp-validation-summary` 和 `<span asp-validation-for`）显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="bd076-307">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="bd076-308">本系列后面的部分将更详细地讨论有关验证的信息。</span><span class="sxs-lookup"><span data-stu-id="bd076-308">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="bd076-309">[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) 生成标签描述和 `Title` 属性的 `for` 特性。</span><span class="sxs-lookup"><span data-stu-id="bd076-309">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="bd076-310">[输入标记帮助程序](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) 使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性并在客户端生成 jQuery 验证所需的 HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="bd076-310">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd076-311">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd076-311">Additional resources</span></span>

* [<span data-ttu-id="bd076-312">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="bd076-312">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="bd076-313">[上一篇：添加模型](xref:tutorials/razor-pages/model)
> [下一步：数据库](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="bd076-313">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
