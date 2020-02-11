---
title: ASP.NET Core 中 Razor 页面的路由和应用约定
author: guardrex
description: 了解路由和应用模型提供程序约定如何帮助控制页面路由、发现和处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: d8377c0a0b8a29fe4b6a7fa67beeff84927c8b74
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114765"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="eb6a4-103">ASP.NET Core 中 Razor 页面的路由和应用约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="eb6a4-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eb6a4-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb6a4-105">了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="eb6a4-106">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="eb6a4-107">若要指定页路由、添加路由段或向路由添加参数，请使用页的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="eb6a4-108">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="eb6a4-109">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="eb6a4-110">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="eb6a4-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="eb6a4-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="eb6a4-112">场景</span><span class="sxs-lookup"><span data-stu-id="eb6a4-112">Scenario</span></span> | <span data-ttu-id="eb6a4-113">示例演示...</span><span class="sxs-lookup"><span data-stu-id="eb6a4-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="eb6a4-114">模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="eb6a4-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="eb6a4-115">Conventions.Add</span></span><ul><li><span data-ttu-id="eb6a4-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="eb6a4-119">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="eb6a4-120">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="eb6a4-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="eb6a4-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="eb6a4-124">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="eb6a4-125">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="eb6a4-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-128">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="eb6a4-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="eb6a4-129">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="eb6a4-130">使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法添加和配置 Razor Pages 约定，以 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` 类中的服务集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="eb6a4-131">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="eb6a4-132">路由顺序</span><span class="sxs-lookup"><span data-stu-id="eb6a4-132">Route order</span></span>

<span data-ttu-id="eb6a4-133">路由指定了用于处理的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> （路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="eb6a4-134">Order</span><span class="sxs-lookup"><span data-stu-id="eb6a4-134">Order</span></span>            | <span data-ttu-id="eb6a4-135">行为</span><span class="sxs-lookup"><span data-stu-id="eb6a4-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="eb6a4-136">-1</span><span class="sxs-lookup"><span data-stu-id="eb6a4-136">-1</span></span>               | <span data-ttu-id="eb6a4-137">在处理其他路由之前处理路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="eb6a4-138">0</span><span class="sxs-lookup"><span data-stu-id="eb6a4-138">0</span></span>                | <span data-ttu-id="eb6a4-139">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-139">Order isn't specified (default value).</span></span> <span data-ttu-id="eb6a4-140">不分配 `Order` （`Order = null`）默认情况下，路由 `Order` 为0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="eb6a4-141">1，2，&hellip; n</span><span class="sxs-lookup"><span data-stu-id="eb6a4-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="eb6a4-142">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="eb6a4-143">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="eb6a4-144">按顺序处理路由（-1、0、1、2、&hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="eb6a4-145">当路由具有相同的 `Order`时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="eb6a4-146">当具有相同 `Order` 和相同数量参数的路由匹配请求 URL 时，路由将按照它们添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>的顺序进行处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="eb6a4-147">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="eb6a4-148">通常，路由将选择 URL 匹配的正确路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="eb6a4-149">如果必须将路由 `Order` 属性设置为正确路由请求，则应用的路由方案可能会使客户端混乱，并使其保持脆弱。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="eb6a4-150">设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="eb6a4-151">该示例应用需要显式路由处理顺序才能使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="eb6a4-152">但是，你应尝试避免在生产应用中设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="eb6a4-153">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="eb6a4-154">有关 MVC 主题中的路由顺序的信息，请[参阅路由到控制器操作：排序属性路由](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="eb6a4-155">模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-155">Model conventions</span></span>

<span data-ttu-id="eb6a4-156">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加一个委托，以便添加适用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-157">向所有页面添加路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="eb6a4-158">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，并将其添加到在页路由模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="eb6a4-159">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="eb6a4-161">这可确保示例应用中的以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="eb6a4-162">本主题稍后会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="eb6a4-163">联系人页路由的默认顺序为 `null` （`Order = 0`），因此它在 `{globalTemplate?}` 路由模板之前匹配。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="eb6a4-164">本主题稍后会添加一个 `{aboutTemplate?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="eb6a4-165">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="eb6a4-166">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `RouteData.Values["globalTemplate"]` 属性，“RouteDataValue”会加载到 `Order = 1` (`RouteData.Values["aboutTemplate"]`) 而不是 `Order = 2` (`Order`) 中。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="eb6a4-167">本主题稍后会添加一个 `{otherPagesTemplate?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="eb6a4-168">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="eb6a4-169">当使用路由参数（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的任何页面时，将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-170">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-171">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-172">将 MVC 添加到 `Startup.ConfigureServices`中的服务集合时，会添加 Razor Pages 选项，如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb6a4-173">有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="eb6a4-174">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-177">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="eb6a4-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="eb6a4-178">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，并将其添加到在页面应用模式构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="eb6a4-179">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="eb6a4-180">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="eb6a4-181">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="eb6a4-182">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="eb6a4-183">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-184">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="eb6a4-185">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-187">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="eb6a4-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="eb6a4-188">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>，并将其添加到在页处理程序模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-189">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="eb6a4-190">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-190">Page route action conventions</span></span>

<span data-ttu-id="eb6a4-191">派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 的默认路由模型提供程序调用用于提供扩展点以配置页面路由的约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="eb6a4-192">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-192">Folder route model convention</span></span>

<span data-ttu-id="eb6a4-193">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 来创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该可对指定文件夹下的所有页面调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上的操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="eb6a4-194">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="eb6a4-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="eb6a4-196">这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="eb6a4-197">如果使用路由参数值（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-198">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-199">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-200">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="eb6a4-203">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-203">Page route model convention</span></span>

<span data-ttu-id="eb6a4-204">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="eb6a4-205">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="eb6a4-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="eb6a4-207">这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="eb6a4-208">如果使用 `/About/RouteDataValue`的路由参数值请求 "关于" 页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["aboutTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-209">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-210">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-211">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="eb6a4-214">使用参数转换器自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="eb6a4-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="eb6a4-215">可以使用参数转换器自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="eb6a4-216">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="eb6a4-217">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="eb6a4-218">`PageRouteTransformerConvention` 页路由模型约定将参数变压器应用到应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="eb6a4-219">例如，位于 */Pages/SubscriptionManagement/ViewAll.cshtml*的 Razor Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写到 `/subscription-management/view-all`中。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="eb6a4-220">`PageRouteTransformerConvention` 仅转换来自 Razor Pages 文件夹和文件名的自动生成的页面路由段。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="eb6a4-221">它不会转换添加了 `@page` 指令的路由段。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="eb6a4-222">该约定还不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>添加的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="eb6a4-223">`PageRouteTransformerConvention` 注册为 `Startup.ConfigureServices`中的一个选项：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a><span data-ttu-id="eb6a4-224">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="eb6a4-224">Configure a page route</span></span>

<span data-ttu-id="eb6a4-225">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置指向指定页面路径中的页面的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="eb6a4-226">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="eb6a4-227">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="eb6a4-228">示例应用为 `/TheContactPage`Contact.cshtml*创建指向* 的路由：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="eb6a4-229">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="eb6a4-230">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="eb6a4-231">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="eb6a4-232">请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="eb6a4-235">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="eb6a4-236">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="eb6a4-239">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-239">Page model action conventions</span></span>

<span data-ttu-id="eb6a4-240">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序调用用于为配置页面模型提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="eb6a4-241">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="eb6a4-242">对于本部分中的示例，示例应用使用 `AddHeaderAttribute` 类，该类是一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>，它应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="eb6a4-243">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="eb6a4-244">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-244">**Folder app model convention**</span></span>

<span data-ttu-id="eb6a4-245">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在指定文件夹下的所有页面的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="eb6a4-246">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="eb6a4-247">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="eb6a4-249">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-249">**Page app model convention**</span></span>

<span data-ttu-id="eb6a4-250">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="eb6a4-251">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="eb6a4-252">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="eb6a4-254">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-254">**Configure a filter**</span></span>

<span data-ttu-id="eb6a4-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="eb6a4-256">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="eb6a4-257">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="eb6a4-258">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="eb6a4-259">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="eb6a4-260">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="eb6a4-261">由于 Razor Pages 忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，则 `EmptyFilter` 不起作用。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="eb6a4-262">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="eb6a4-264">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-264">**Configure a filter factory**</span></span>

<span data-ttu-id="eb6a4-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="eb6a4-266">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="eb6a4-267">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="eb6a4-268">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="eb6a4-270">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="eb6a4-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="eb6a4-271">Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="eb6a4-272">可使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="eb6a4-273">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="eb6a4-274">页面筛选器（<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>）是应用于 Razor Pages 的筛选器。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="eb6a4-275">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb6a4-276">其他资源</span><span class="sxs-lookup"><span data-stu-id="eb6a4-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="eb6a4-277">了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-277">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="eb6a4-278">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-278">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="eb6a4-279">若要指定页路由、添加路由段或向路由添加参数，请使用页的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-279">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="eb6a4-280">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-280">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="eb6a4-281">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-281">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="eb6a4-282">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-282">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="eb6a4-283">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="eb6a4-283">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="eb6a4-284">场景</span><span class="sxs-lookup"><span data-stu-id="eb6a4-284">Scenario</span></span> | <span data-ttu-id="eb6a4-285">示例演示...</span><span class="sxs-lookup"><span data-stu-id="eb6a4-285">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="eb6a4-286">模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-286">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="eb6a4-287">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="eb6a4-287">Conventions.Add</span></span><ul><li><span data-ttu-id="eb6a4-288">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-288">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-289">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-289">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-290">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-290">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="eb6a4-291">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-291">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="eb6a4-292">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-292">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="eb6a4-293">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-293">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-294">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-294">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-295">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="eb6a4-295">AddPageRoute</span></span></li></ul> | <span data-ttu-id="eb6a4-296">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-296">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="eb6a4-297">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-297">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="eb6a4-298">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-298">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-299">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-299">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-300">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="eb6a4-300">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="eb6a4-301">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-301">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="eb6a4-302">使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法添加和配置 Razor Pages 约定，以 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` 类中的服务集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-302">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="eb6a4-303">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-303">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="eb6a4-304">路由顺序</span><span class="sxs-lookup"><span data-stu-id="eb6a4-304">Route order</span></span>

<span data-ttu-id="eb6a4-305">路由指定了用于处理的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> （路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-305">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="eb6a4-306">Order</span><span class="sxs-lookup"><span data-stu-id="eb6a4-306">Order</span></span>            | <span data-ttu-id="eb6a4-307">行为</span><span class="sxs-lookup"><span data-stu-id="eb6a4-307">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="eb6a4-308">-1</span><span class="sxs-lookup"><span data-stu-id="eb6a4-308">-1</span></span>               | <span data-ttu-id="eb6a4-309">在处理其他路由之前处理路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-309">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="eb6a4-310">0</span><span class="sxs-lookup"><span data-stu-id="eb6a4-310">0</span></span>                | <span data-ttu-id="eb6a4-311">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-311">Order isn't specified (default value).</span></span> <span data-ttu-id="eb6a4-312">不分配 `Order` （`Order = null`）默认情况下，路由 `Order` 为0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-312">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="eb6a4-313">1，2，&hellip; n</span><span class="sxs-lookup"><span data-stu-id="eb6a4-313">1, 2, &hellip; n</span></span> | <span data-ttu-id="eb6a4-314">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-314">Specifies the route processing order.</span></span> |

<span data-ttu-id="eb6a4-315">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-315">Route processing is established by convention:</span></span>

* <span data-ttu-id="eb6a4-316">按顺序处理路由（-1、0、1、2、&hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-316">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="eb6a4-317">当路由具有相同的 `Order`时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-317">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="eb6a4-318">当具有相同 `Order` 和相同数量参数的路由匹配请求 URL 时，路由将按照它们添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>的顺序进行处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-318">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="eb6a4-319">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-319">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="eb6a4-320">通常，路由将选择 URL 匹配的正确路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-320">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="eb6a4-321">如果必须将路由 `Order` 属性设置为正确路由请求，则应用的路由方案可能会使客户端混乱，并使其保持脆弱。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-321">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="eb6a4-322">设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-322">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="eb6a4-323">该示例应用需要显式路由处理顺序才能使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-323">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="eb6a4-324">但是，你应尝试避免在生产应用中设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-324">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="eb6a4-325">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-325">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="eb6a4-326">有关 MVC 主题中的路由顺序的信息，请[参阅路由到控制器操作：排序属性路由](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-326">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="eb6a4-327">模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-327">Model conventions</span></span>

<span data-ttu-id="eb6a4-328">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加一个委托，以便添加适用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-328">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-329">向所有页面添加路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-329">Add a route model convention to all pages</span></span>

<span data-ttu-id="eb6a4-330">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，并将其添加到在页路由模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-330">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="eb6a4-331">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-331">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-332"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-332">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="eb6a4-333">这可确保示例应用中的以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-333">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="eb6a4-334">本主题稍后会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-334">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="eb6a4-335">联系人页路由的默认顺序为 `null` （`Order = 0`），因此它在 `{globalTemplate?}` 路由模板之前匹配。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-335">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="eb6a4-336">本主题稍后会添加一个 `{aboutTemplate?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-336">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="eb6a4-337">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-337">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="eb6a4-338">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `RouteData.Values["globalTemplate"]` 属性，“RouteDataValue”会加载到 `Order = 1` (`RouteData.Values["aboutTemplate"]`) 而不是 `Order = 2` (`Order`) 中。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-338">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="eb6a4-339">本主题稍后会添加一个 `{otherPagesTemplate?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-339">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="eb6a4-340">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-340">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="eb6a4-341">当使用路由参数（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的任何页面时，将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-341">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-342">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-342">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-343">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-343">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-344">将 MVC 添加到 `Startup.ConfigureServices`中的服务集合时，会添加 Razor Pages 选项，如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-344">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb6a4-345">有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-345">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="eb6a4-346">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-346">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-349">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="eb6a4-349">Add an app model convention to all pages</span></span>

<span data-ttu-id="eb6a4-350">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，并将其添加到在页面应用模式构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-350">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="eb6a4-351">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-351">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="eb6a4-352">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-352">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="eb6a4-353">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-353">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="eb6a4-354">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-354">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="eb6a4-355">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-355">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-356">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-356">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="eb6a4-357">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-357">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-359">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="eb6a4-359">Add a handler model convention to all pages</span></span>

<span data-ttu-id="eb6a4-360">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>，并将其添加到在页处理程序模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-360">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-361">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-361">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="eb6a4-362">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-362">Page route action conventions</span></span>

<span data-ttu-id="eb6a4-363">派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 的默认路由模型提供程序调用用于提供扩展点以配置页面路由的约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-363">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="eb6a4-364">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-364">Folder route model convention</span></span>

<span data-ttu-id="eb6a4-365">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 来创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该可对指定文件夹下的所有页面调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上的操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-365">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="eb6a4-366">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-366">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="eb6a4-367"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-367">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="eb6a4-368">这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-368">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="eb6a4-369">如果使用路由参数值（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-369">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-370">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-370">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-371">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-371">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-372">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-372">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="eb6a4-375">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-375">Page route model convention</span></span>

<span data-ttu-id="eb6a4-376">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-376">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="eb6a4-377">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-377">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="eb6a4-378"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-378">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="eb6a4-379">这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-379">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="eb6a4-380">如果使用 `/About/RouteDataValue`的路由参数值请求 "关于" 页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["aboutTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-380">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-381">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-381">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-382">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-382">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-383">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-383">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="eb6a4-386">使用参数转换器自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="eb6a4-386">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="eb6a4-387">可以使用参数转换器自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-387">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="eb6a4-388">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-388">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="eb6a4-389">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-389">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="eb6a4-390">`PageRouteTransformerConvention` 页路由模型约定将参数变压器应用到应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-390">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="eb6a4-391">例如，位于 */Pages/SubscriptionManagement/ViewAll.cshtml*的 Razor Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写到 `/subscription-management/view-all`中。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-391">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="eb6a4-392">`PageRouteTransformerConvention` 仅转换来自 Razor Pages 文件夹和文件名的自动生成的页面路由段。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-392">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="eb6a4-393">它不会转换添加了 `@page` 指令的路由段。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-393">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="eb6a4-394">该约定还不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>添加的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-394">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="eb6a4-395">`PageRouteTransformerConvention` 注册为 `Startup.ConfigureServices`中的一个选项：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-395">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a><span data-ttu-id="eb6a4-396">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="eb6a4-396">Configure a page route</span></span>

<span data-ttu-id="eb6a4-397">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置指向指定页面路径中的页面的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-397">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="eb6a4-398">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-398">Generated links to the page use your specified route.</span></span> <span data-ttu-id="eb6a4-399">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-399">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="eb6a4-400">示例应用为 `/TheContactPage`Contact.cshtml*创建指向* 的路由：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-400">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="eb6a4-401">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-401">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="eb6a4-402">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-402">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="eb6a4-403">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-403">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="eb6a4-404">请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-404">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="eb6a4-407">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-407">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="eb6a4-408">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-408">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="eb6a4-411">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-411">Page model action conventions</span></span>

<span data-ttu-id="eb6a4-412">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序调用用于为配置页面模型提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-412">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="eb6a4-413">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-413">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="eb6a4-414">对于本部分中的示例，示例应用使用 `AddHeaderAttribute` 类，该类是一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>，它应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-414">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="eb6a4-415">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-415">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="eb6a4-416">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-416">**Folder app model convention**</span></span>

<span data-ttu-id="eb6a4-417">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在指定文件夹下的所有页面的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-417">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="eb6a4-418">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-418">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="eb6a4-419">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-419">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="eb6a4-421">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-421">**Page app model convention**</span></span>

<span data-ttu-id="eb6a4-422">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-422">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="eb6a4-423">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-423">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="eb6a4-424">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-424">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="eb6a4-426">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-426">**Configure a filter**</span></span>

<span data-ttu-id="eb6a4-427"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-427"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="eb6a4-428">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-428">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="eb6a4-429">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-429">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="eb6a4-430">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-430">If the condition passes, a header is added.</span></span> <span data-ttu-id="eb6a4-431">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-431">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="eb6a4-432">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-432">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="eb6a4-433">由于 Razor Pages 忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，则 `EmptyFilter` 不起作用。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-433">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="eb6a4-434">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-434">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="eb6a4-436">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-436">**Configure a filter factory**</span></span>

<span data-ttu-id="eb6a4-437"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-437"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="eb6a4-438">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-438">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="eb6a4-439">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-439">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="eb6a4-440">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-440">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="eb6a4-442">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="eb6a4-442">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="eb6a4-443">Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-443">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="eb6a4-444">可使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-444">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="eb6a4-445">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-445">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="eb6a4-446">页面筛选器（<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>）是应用于 Razor Pages 的筛选器。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-446">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="eb6a4-447">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-447">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb6a4-448">其他资源</span><span class="sxs-lookup"><span data-stu-id="eb6a4-448">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="eb6a4-449">了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-449">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="eb6a4-450">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-450">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="eb6a4-451">若要指定页路由、添加路由段或向路由添加参数，请使用页的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-451">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="eb6a4-452">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-452">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="eb6a4-453">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-453">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="eb6a4-454">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-454">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="eb6a4-455">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="eb6a4-455">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="eb6a4-456">场景</span><span class="sxs-lookup"><span data-stu-id="eb6a4-456">Scenario</span></span> | <span data-ttu-id="eb6a4-457">示例演示...</span><span class="sxs-lookup"><span data-stu-id="eb6a4-457">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="eb6a4-458">模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-458">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="eb6a4-459">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="eb6a4-459">Conventions.Add</span></span><ul><li><span data-ttu-id="eb6a4-460">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-460">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-461">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-461">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-462">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-462">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="eb6a4-463">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-463">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="eb6a4-464">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-464">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="eb6a4-465">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-465">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-466">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-466">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="eb6a4-467">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="eb6a4-467">AddPageRoute</span></span></li></ul> | <span data-ttu-id="eb6a4-468">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-468">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="eb6a4-469">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-469">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="eb6a4-470">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-470">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-471">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="eb6a4-471">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="eb6a4-472">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="eb6a4-472">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="eb6a4-473">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-473">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="eb6a4-474">使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法添加和配置 Razor Pages 约定，以 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` 类中的服务集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-474">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="eb6a4-475">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-475">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="eb6a4-476">路由顺序</span><span class="sxs-lookup"><span data-stu-id="eb6a4-476">Route order</span></span>

<span data-ttu-id="eb6a4-477">路由指定了用于处理的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> （路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-477">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="eb6a4-478">Order</span><span class="sxs-lookup"><span data-stu-id="eb6a4-478">Order</span></span>            | <span data-ttu-id="eb6a4-479">行为</span><span class="sxs-lookup"><span data-stu-id="eb6a4-479">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="eb6a4-480">-1</span><span class="sxs-lookup"><span data-stu-id="eb6a4-480">-1</span></span>               | <span data-ttu-id="eb6a4-481">在处理其他路由之前处理路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-481">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="eb6a4-482">0</span><span class="sxs-lookup"><span data-stu-id="eb6a4-482">0</span></span>                | <span data-ttu-id="eb6a4-483">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-483">Order isn't specified (default value).</span></span> <span data-ttu-id="eb6a4-484">不分配 `Order` （`Order = null`）默认情况下，路由 `Order` 为0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-484">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="eb6a4-485">1，2，&hellip; n</span><span class="sxs-lookup"><span data-stu-id="eb6a4-485">1, 2, &hellip; n</span></span> | <span data-ttu-id="eb6a4-486">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-486">Specifies the route processing order.</span></span> |

<span data-ttu-id="eb6a4-487">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-487">Route processing is established by convention:</span></span>

* <span data-ttu-id="eb6a4-488">按顺序处理路由（-1、0、1、2、&hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-488">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="eb6a4-489">当路由具有相同的 `Order`时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-489">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="eb6a4-490">当具有相同 `Order` 和相同数量参数的路由匹配请求 URL 时，路由将按照它们添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>的顺序进行处理。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-490">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="eb6a4-491">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-491">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="eb6a4-492">通常，路由将选择 URL 匹配的正确路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-492">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="eb6a4-493">如果必须将路由 `Order` 属性设置为正确路由请求，则应用的路由方案可能会使客户端混乱，并使其保持脆弱。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-493">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="eb6a4-494">设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-494">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="eb6a4-495">该示例应用需要显式路由处理顺序才能使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-495">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="eb6a4-496">但是，你应尝试避免在生产应用中设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-496">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="eb6a4-497">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-497">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="eb6a4-498">有关 MVC 主题中的路由顺序的信息，请[参阅路由到控制器操作：排序属性路由](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-498">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="eb6a4-499">模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-499">Model conventions</span></span>

<span data-ttu-id="eb6a4-500">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加一个委托，以便添加适用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-500">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-501">向所有页面添加路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-501">Add a route model convention to all pages</span></span>

<span data-ttu-id="eb6a4-502">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，并将其添加到在页路由模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-502">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="eb6a4-503">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-503">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-504"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-504">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="eb6a4-505">这可确保示例应用中的以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-505">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="eb6a4-506">本主题稍后会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-506">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="eb6a4-507">联系人页路由的默认顺序为 `null` （`Order = 0`），因此它在 `{globalTemplate?}` 路由模板之前匹配。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-507">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="eb6a4-508">本主题稍后会添加一个 `{aboutTemplate?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-508">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="eb6a4-509">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-509">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="eb6a4-510">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `RouteData.Values["globalTemplate"]` 属性，“RouteDataValue”会加载到 `Order = 1` (`RouteData.Values["aboutTemplate"]`) 而不是 `Order = 2` (`Order`) 中。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-510">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="eb6a4-511">本主题稍后会添加一个 `{otherPagesTemplate?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-511">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="eb6a4-512">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-512">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="eb6a4-513">当使用路由参数（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的任何页面时，将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-513">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-514">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-514">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-515">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-515">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-516">将 MVC 添加到 `Startup.ConfigureServices`中的服务集合时，会添加 Razor Pages 选项，如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-516">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb6a4-517">有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-517">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="eb6a4-518">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-518">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-521">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="eb6a4-521">Add an app model convention to all pages</span></span>

<span data-ttu-id="eb6a4-522">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，并将其添加到在页面应用模式构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-522">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="eb6a4-523">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-523">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="eb6a4-524">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-524">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="eb6a4-525">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-525">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="eb6a4-526">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-526">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="eb6a4-527">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-527">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-528">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-528">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="eb6a4-529">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-529">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="eb6a4-531">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="eb6a4-531">Add a handler model convention to all pages</span></span>

<span data-ttu-id="eb6a4-532">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>，并将其添加到在页处理程序模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-532">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="eb6a4-533">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-533">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="eb6a4-534">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-534">Page route action conventions</span></span>

<span data-ttu-id="eb6a4-535">派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 的默认路由模型提供程序调用用于提供扩展点以配置页面路由的约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-535">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="eb6a4-536">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-536">Folder route model convention</span></span>

<span data-ttu-id="eb6a4-537">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 来创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该可对指定文件夹下的所有页面调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上的操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-537">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="eb6a4-538">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-538">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="eb6a4-539"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-539">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="eb6a4-540">这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-540">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="eb6a4-541">如果使用路由参数值（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-541">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-542">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-542">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-543">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-543">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-544">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-544">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="eb6a4-547">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-547">Page route model convention</span></span>

<span data-ttu-id="eb6a4-548">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-548">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="eb6a4-549">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-549">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="eb6a4-550"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-550">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="eb6a4-551">这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-551">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="eb6a4-552">如果使用 `/About/RouteDataValue`的路由参数值请求 "关于" 页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["aboutTemplate"]` （`Order = 2`），因为设置 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-552">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="eb6a4-553">请尽可能不要将 `Order`设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-553">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="eb6a4-554">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-554">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="eb6a4-555">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-555">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="eb6a4-558">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="eb6a4-558">Configure a page route</span></span>

<span data-ttu-id="eb6a4-559">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置指向指定页面路径中的页面的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-559">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="eb6a4-560">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-560">Generated links to the page use your specified route.</span></span> <span data-ttu-id="eb6a4-561">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-561">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="eb6a4-562">示例应用为 `/TheContactPage`Contact.cshtml*创建指向* 的路由：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-562">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="eb6a4-563">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-563">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="eb6a4-564">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-564">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="eb6a4-565">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-565">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="eb6a4-566">请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-566">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="eb6a4-569">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-569">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="eb6a4-570">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-570">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="eb6a4-573">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="eb6a4-573">Page model action conventions</span></span>

<span data-ttu-id="eb6a4-574">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序调用用于为配置页面模型提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-574">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="eb6a4-575">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-575">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="eb6a4-576">对于本部分中的示例，示例应用使用 `AddHeaderAttribute` 类，该类是一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>，它应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-576">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="eb6a4-577">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-577">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="eb6a4-578">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-578">**Folder app model convention**</span></span>

<span data-ttu-id="eb6a4-579">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在指定文件夹下的所有页面的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-579">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="eb6a4-580">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-580">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="eb6a4-581">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-581">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="eb6a4-583">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-583">**Page app model convention**</span></span>

<span data-ttu-id="eb6a4-584">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-584">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="eb6a4-585">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-585">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="eb6a4-586">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-586">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="eb6a4-588">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-588">**Configure a filter**</span></span>

<span data-ttu-id="eb6a4-589"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-589"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="eb6a4-590">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-590">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="eb6a4-591">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-591">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="eb6a4-592">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-592">If the condition passes, a header is added.</span></span> <span data-ttu-id="eb6a4-593">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-593">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="eb6a4-594">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-594">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="eb6a4-595">由于 Razor Pages 忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，则 `EmptyFilter` 不起作用。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-595">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="eb6a4-596">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-596">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="eb6a4-598">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="eb6a4-598">**Configure a filter factory**</span></span>

<span data-ttu-id="eb6a4-599"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-599"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="eb6a4-600">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-600">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="eb6a4-601">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-601">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="eb6a4-602">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="eb6a4-602">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="eb6a4-604">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="eb6a4-604">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="eb6a4-605">Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-605">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="eb6a4-606">可使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-606">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="eb6a4-607">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-607">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="eb6a4-608">页面筛选器（<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>）是应用于 Razor Pages 的筛选器。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-608">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="eb6a4-609">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="eb6a4-609">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb6a4-610">其他资源</span><span class="sxs-lookup"><span data-stu-id="eb6a4-610">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
