---
title: ASP.NET Core 中的布局
author: ardalis
description: 了解如何在 ASP.NET Core 应用中呈现视图之前，使用通用布局、共享指令和运行常见代码。
ms.author: riande
ms.date: 07/30/2019
uid: mvc/views/layout
ms.openlocfilehash: db8c6c30397593c1a8375ebc800c1c0e34d241cb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655044"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="cb9b6-103">ASP.NET Core 中的布局</span><span class="sxs-lookup"><span data-stu-id="cb9b6-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="cb9b6-104">作者：[Steve Smith](https://ardalis.com/) 和 [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="cb9b6-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="cb9b6-105">页面和视图经常共享视觉对象和程序元素。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="cb9b6-106">本文介绍如何：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="cb9b6-107">使用通用布局。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-107">Use common layouts.</span></span>
* <span data-ttu-id="cb9b6-108">共享指令。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-108">Share directives.</span></span>
* <span data-ttu-id="cb9b6-109">在呈现页面或视图之前运行通用代码。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="cb9b6-110">本文档讨论了 ASP.NET Core MVC 的两种不同方法的布局：Razor Pages 和带有视图的控制器。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="cb9b6-111">在本主题中，差异很小：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="cb9b6-112">Razor Pages 位于“页面”文件夹。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="cb9b6-113">具有视图的控制器使用视图的“视图”文件夹。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="cb9b6-114">什么是布局</span><span class="sxs-lookup"><span data-stu-id="cb9b6-114">What is a Layout</span></span>

<span data-ttu-id="cb9b6-115">大多数 Web 应用都有一个通用布局，可在页面间切换时为用户提供一致体验。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="cb9b6-116">该布局通常包括应用标头、导航或菜单元素以及页脚等常见的用户界面元素。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![页面布局示例](layout/_static/page-layout.png)

<span data-ttu-id="cb9b6-118">应用中的许多页面也经常使用脚本和样式表等常用的 HTML 结构。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="cb9b6-119">所有这些共享元素均可在布局文件中进行定义，应用内使用的任何视图随后均可引用此文件。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="cb9b6-120">布局可减少视图中的重复代码。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="cb9b6-121">按照约定，ASP.NET Core 应用的默认布局名为 _Layout.cshtml。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="cb9b6-122">使用模板创建的新 ASP.NET Core 项目的布局文件为：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="cb9b6-123">Razor Pages：Pages/Shared/_Layout.cshtml</span><span class="sxs-lookup"><span data-stu-id="cb9b6-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![解决方案资源管理器中的页面文件夹](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="cb9b6-125">包含视图的控制器：Views/Shared/_Layout.cshtml</span><span class="sxs-lookup"><span data-stu-id="cb9b6-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![解决方案资源管理器中的视图文件夹](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="cb9b6-127">布局定义应用中的视图的最高级别模板。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="cb9b6-128">应用不需要布局。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-128">Apps don't require a layout.</span></span> <span data-ttu-id="cb9b6-129">应用可以定义多个布局，并且不同的视图指定不同的布局。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="cb9b6-130">下面的代码演示了使用控制器和视图创建的项目模板的布局文件：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="cb9b6-131">指定布局</span><span class="sxs-lookup"><span data-stu-id="cb9b6-131">Specifying a Layout</span></span>

<span data-ttu-id="cb9b6-132">Razor 视图具有 `Layout` 属性。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="cb9b6-133">单个视图通过设置此属性来指定布局：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="cb9b6-134">指定的布局可以使用完整路径（例如 /Pages/Shared/_Layout.cshtml 或 /Views/Shared/_Layout.cshtml）或部分名称（示例： *）。* `_Layout`</span><span class="sxs-lookup"><span data-stu-id="cb9b6-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="cb9b6-135">如果提供部分名称，则 Razor 视图引擎使用其标准发现过程来搜索布局文件。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="cb9b6-136">首先搜索处理程序方法（或控制器）所在的文件夹，然后搜索 Shared 文件夹。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="cb9b6-137">此发现过程与用于发现[分部视图](xref:mvc/views/partial#partial-view-discovery)的过程相同。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="cb9b6-138">默认情况下，每个布局必须调用 `RenderBody`。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="cb9b6-139">无论在何处调用 `RenderBody`，都会呈现视图的内容。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="cb9b6-140">部分</span><span class="sxs-lookup"><span data-stu-id="cb9b6-140">Sections</span></span>

<span data-ttu-id="cb9b6-141">布局可以通过调用  *来选择引用一个或多个节*`RenderSection`。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="cb9b6-142">节提供一种方法来组织某些页面元素应当放置的位置。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="cb9b6-143">每次调用 `RenderSection` 时都可指定该部分是必需还是可选：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="cb9b6-144">如果找不到所需的节，将引发异常。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="cb9b6-145">单个视图使用 `@section` Razor 语法指定要在节中呈现的内容。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="cb9b6-146">如果某个页面或视图定义了一个部分，则必须呈现该部分（否则将发生错误）。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="cb9b6-147">Razor Pages 视图中的示例 `@section` 定义：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="cb9b6-148">在上面的代码中，scripts / main.js 添加到了页面或视图的  *部分*`scripts`。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="cb9b6-149">相同应用中的其他页面或视图可能不需要此脚本且不会定义脚本部分。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="cb9b6-150">以下标记使用[部分标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)来呈现  _ValidationScriptsPartial.cshtml：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="cb9b6-151">前面的标记由[基架标识](xref:security/authentication/scaffold-identity)生成。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="cb9b6-152">在页面或视图中定义的部分仅在其即时布局页面中可用。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="cb9b6-153">不能从部分、视图组件或视图系统的其他部分引用它们。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="cb9b6-154">忽略节</span><span class="sxs-lookup"><span data-stu-id="cb9b6-154">Ignoring sections</span></span>

<span data-ttu-id="cb9b6-155">默认情况下，必须由布局页面呈现内容页中的正文和所有节。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="cb9b6-156">Razor 视图引擎通过跟踪是否已呈现正文和每个节来强制执行此操作。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="cb9b6-157">要让视图引擎忽略正文或节，请调用 `IgnoreBody` 和 `IgnoreSection` 方法。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="cb9b6-158">必须呈现或忽略 Razor 页面中的正文和每个节。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="cb9b6-159">导入共享指令</span><span class="sxs-lookup"><span data-stu-id="cb9b6-159">Importing Shared Directives</span></span>

<span data-ttu-id="cb9b6-160">视图和页面可以使用 Razor 指令导入命名空间和使用[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-160">Views and pages can use Razor directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="cb9b6-161">由多个视图共享的指令可以在通用 _ViewImports.cshtml 文件中进行指定。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="cb9b6-162">`_ViewImports` 文件支持以下指令：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="cb9b6-163">该文件不支持函数和节定义等其他 Razor 功能。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="cb9b6-164">示例 `_ViewImports.cshtml` 文件：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="cb9b6-165">ASP.NET Core MVC 应用的 _ViewImports.cshtml 文件通常放在“页面”（或“视图”）文件夹中。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="cb9b6-166">_ViewImports.cshtml 文件可以放在任何文件夹中，在这种情况下，它只会应用于该文件夹及其子文件夹中的页面或视图。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="cb9b6-167">从根级别开始处理 `_ViewImports` 文件，然后处理在页面或视图本身的位置之前的每个文件夹。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="cb9b6-168">可以在文件夹级别覆盖根级别指定的 `_ViewImports` 设置。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="cb9b6-169">例如，假设：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-169">For example, suppose:</span></span>

* <span data-ttu-id="cb9b6-170">根级别 _ViewImports.cshtml 文件包含 *和*。`@model MyModel1``@addTagHelper *, MyTagHelper1`</span><span class="sxs-lookup"><span data-stu-id="cb9b6-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="cb9b6-171">子文件夹 _ViewImports.cshtml 文件包含 *和*。`@model MyModel2``@addTagHelper *, MyTagHelper2`</span><span class="sxs-lookup"><span data-stu-id="cb9b6-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="cb9b6-172">子文件夹中的页面和视图将有权访问标记帮助程序和 `MyModel2` 模型。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="cb9b6-173">如果在文件层次结构中找到多个 _ViewImports.cshtml 文件，指令的合并行为是：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="cb9b6-174">`@addTagHelper``@removeTagHelper`：按顺序全部运行</span><span class="sxs-lookup"><span data-stu-id="cb9b6-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="cb9b6-175">`@tagHelperPrefix`：最接近视图的文件会替代任何其他文件</span><span class="sxs-lookup"><span data-stu-id="cb9b6-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="cb9b6-176">`@model`：最接近视图的文件会替代任何其他文件</span><span class="sxs-lookup"><span data-stu-id="cb9b6-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="cb9b6-177">`@inherits`：最接近视图的文件会替代任何其他文件</span><span class="sxs-lookup"><span data-stu-id="cb9b6-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="cb9b6-178">`@using`：全部包括在内；忽略重复项</span><span class="sxs-lookup"><span data-stu-id="cb9b6-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="cb9b6-179">`@inject`：针对每个属性，最接近视图的属性会替代具有相同属性名的任何其他属性</span><span class="sxs-lookup"><span data-stu-id="cb9b6-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="cb9b6-180">在呈现每个视图之前运行代码</span><span class="sxs-lookup"><span data-stu-id="cb9b6-180">Running Code Before Each View</span></span>

<span data-ttu-id="cb9b6-181">需要在每个视图或页面之前运行的代码应置于 _ViewStart.cshtml 文件中。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="cb9b6-182">按照约定，_ViewStart.cshtml 文件位于“页面”（或“视图”）文件夹。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="cb9b6-183">在呈现每个完整视图（不是布局，也不是分部视图）之前运行 _ViewStart.cshtml 中列出的语句。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="cb9b6-184">与 [ViewImports.cshtml](xref:mvc/views/layout#viewimports) 一样，_ViewStart.cshtml 也是分层结构。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="cb9b6-185">如果在视图或页面文件夹中定义了 _ViewStart.cshtml 文件，则它将在页面（或视图）的根目录中定义的文件之后运行）文件夹（如果有）。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="cb9b6-186">一个示例 _ViewStart.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="cb9b6-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="cb9b6-187">上述文件指定所有视图都将使用 _Layout.cshtml 布局。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="cb9b6-188">_ViewStart.cshtml 和 _ViewImports.cshtml 通常不置于 /Pages/Shared（或 /Views/Shared）文件夹中。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="cb9b6-189">这些应用级别版本的文件应直接放置在 /Pages（或 /Views）文件夹中。</span><span class="sxs-lookup"><span data-stu-id="cb9b6-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
