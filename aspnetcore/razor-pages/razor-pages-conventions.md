---
title: ASP.NET Core 中的 Razor Pages 路由和应用约定
author: rick-anderson
description: 了解路由和应用模型提供程序约定如何帮助控制页面路由、发现和处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 308ca4401289a55e5dba8d61de50644cb2a53433
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405245"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="f5108-103">ASP.NET Core 中的 Razor Pages 路由和应用约定</span><span class="sxs-lookup"><span data-stu-id="f5108-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5108-104">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="f5108-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="f5108-105">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="f5108-106">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="f5108-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="f5108-107">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="f5108-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="f5108-108">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="f5108-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="f5108-109">有关详细信息，请参阅[路由：保留的路由名称](xref:mvc/controllers/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="f5108-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="f5108-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f5108-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="f5108-111">方案</span><span class="sxs-lookup"><span data-stu-id="f5108-111">Scenario</span></span> | <span data-ttu-id="f5108-112">示例演示...</span><span class="sxs-lookup"><span data-stu-id="f5108-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="f5108-113">模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="f5108-114">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="f5108-114">Conventions.Add</span></span><ul><li><span data-ttu-id="f5108-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="f5108-118">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="f5108-119">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="f5108-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="f5108-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="f5108-123">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="f5108-124">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="f5108-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-127">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="f5108-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="f5108-128">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="f5108-129">使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法向 `Startup` 类中服务集合的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 添加和配置 Razor Pages 约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-129">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="f5108-130">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="f5108-130">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="f5108-131">路由顺序</span><span class="sxs-lookup"><span data-stu-id="f5108-131">Route order</span></span>

<span data-ttu-id="f5108-132">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="f5108-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="f5108-133">顺序</span><span class="sxs-lookup"><span data-stu-id="f5108-133">Order</span></span>            | <span data-ttu-id="f5108-134">行为</span><span class="sxs-lookup"><span data-stu-id="f5108-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="f5108-135">-1</span><span class="sxs-lookup"><span data-stu-id="f5108-135">-1</span></span>               | <span data-ttu-id="f5108-136">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="f5108-137">0</span><span class="sxs-lookup"><span data-stu-id="f5108-137">0</span></span>                | <span data-ttu-id="f5108-138">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="f5108-138">Order isn't specified (default value).</span></span> <span data-ttu-id="f5108-139">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="f5108-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="f5108-140">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="f5108-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="f5108-141">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="f5108-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="f5108-142">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="f5108-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="f5108-143">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="f5108-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="f5108-144">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="f5108-145">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="f5108-146">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="f5108-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="f5108-147">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="f5108-148">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="f5108-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="f5108-149">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="f5108-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="f5108-150">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="f5108-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="f5108-151">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="f5108-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="f5108-152"> Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="f5108-152"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="f5108-153">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="f5108-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="f5108-154">模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-154">Model conventions</span></span>

<span data-ttu-id="f5108-155">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="f5108-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="f5108-156">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="f5108-157">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="f5108-158">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-159"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="f5108-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="f5108-160">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="f5108-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="f5108-161">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="f5108-162">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="f5108-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="f5108-163">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f5108-164">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f5108-165">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="f5108-166">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f5108-167">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f5108-168">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-169">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-170">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-171">将 MVC 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 Razor Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="f5108-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5108-172">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="f5108-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f5108-173">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="f5108-176">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="f5108-177">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="f5108-178">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="f5108-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="f5108-179">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="f5108-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="f5108-180">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="f5108-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="f5108-181">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="f5108-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="f5108-182">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-183">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f5108-184">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="f5108-186">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="f5108-187">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-188">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="f5108-189">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-189">Page route action conventions</span></span>

<span data-ttu-id="f5108-190">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="f5108-191">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-191">Folder route model convention</span></span>

<span data-ttu-id="f5108-192">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="f5108-193">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f5108-194"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f5108-195">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="f5108-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f5108-196">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-197">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-198">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-199">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="f5108-202">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-202">Page route model convention</span></span>

<span data-ttu-id="f5108-203">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="f5108-204">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f5108-205"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f5108-206">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="f5108-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f5108-207">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-208">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-209">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-210">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="f5108-213">使用参数转换程序自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="f5108-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="f5108-214">可以使用参数转换程序自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="f5108-215">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="f5108-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="f5108-216">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="f5108-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="f5108-217">`PageRouteTransformerConvention` 页面路由模型约定将参数转换程序应用于应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="f5108-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="f5108-218">例如，/Pages/SubscriptionManagement/ViewAll.cshtml 处的 Razor Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写为 `/subscription-management/view-all`。</span><span class="sxs-lookup"><span data-stu-id="f5108-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="f5108-219">`PageRouteTransformerConvention` 仅转换来自 Razor Pages 文件夹和文件名的自动生成页面路由段。</span><span class="sxs-lookup"><span data-stu-id="f5108-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="f5108-220">它不会转换使用 `@page` 指令添加的路由段。</span><span class="sxs-lookup"><span data-stu-id="f5108-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="f5108-221">该约定也不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 添加的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="f5108-222">`PageRouteTransformerConvention` 在 `Startup.ConfigureServices` 中注册为选项：</span><span class="sxs-lookup"><span data-stu-id="f5108-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="f5108-223">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="f5108-223">Configure a page route</span></span>

<span data-ttu-id="f5108-224">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="f5108-225">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="f5108-226">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="f5108-227">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="f5108-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="f5108-228">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="f5108-229">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="f5108-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="f5108-230">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="f5108-231">请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="f5108-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="f5108-234">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="f5108-235">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="f5108-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="f5108-238">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-238">Page model action conventions</span></span>

<span data-ttu-id="f5108-239">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="f5108-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="f5108-240">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="f5108-241">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="f5108-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="f5108-242">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="f5108-243">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="f5108-243">**Folder app model convention**</span></span>

<span data-ttu-id="f5108-244">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="f5108-245">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="f5108-246">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="f5108-248">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="f5108-248">**Page app model convention**</span></span>

<span data-ttu-id="f5108-249">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="f5108-250">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="f5108-251">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="f5108-253">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="f5108-253">**Configure a filter**</span></span>

<span data-ttu-id="f5108-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="f5108-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="f5108-255">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="f5108-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="f5108-256">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="f5108-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="f5108-257">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="f5108-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="f5108-258">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="f5108-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="f5108-259">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="f5108-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="f5108-260">由于 Razor Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="f5108-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="f5108-261">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="f5108-263">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="f5108-263">**Configure a filter factory**</span></span>

<span data-ttu-id="f5108-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="f5108-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="f5108-265">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="f5108-266">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="f5108-267">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="f5108-269">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="f5108-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="f5108-270">Razor Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="f5108-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="f5108-271">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="f5108-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="f5108-272">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="f5108-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="f5108-273">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 Razor Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="f5108-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="f5108-274">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="f5108-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5108-275">其他资源</span><span class="sxs-lookup"><span data-stu-id="f5108-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f5108-276">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="f5108-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="f5108-277">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="f5108-278">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="f5108-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="f5108-279">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="f5108-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="f5108-280">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="f5108-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="f5108-281">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="f5108-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="f5108-282">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f5108-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="f5108-283">方案</span><span class="sxs-lookup"><span data-stu-id="f5108-283">Scenario</span></span> | <span data-ttu-id="f5108-284">示例演示...</span><span class="sxs-lookup"><span data-stu-id="f5108-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="f5108-285">模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="f5108-286">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="f5108-286">Conventions.Add</span></span><ul><li><span data-ttu-id="f5108-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="f5108-290">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="f5108-291">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="f5108-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="f5108-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="f5108-295">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="f5108-296">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="f5108-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-299">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="f5108-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="f5108-300">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="f5108-301">使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法向 `Startup` 类中服务集合的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 添加和配置 Razor Pages 约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="f5108-302">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="f5108-302">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="f5108-303">路由顺序</span><span class="sxs-lookup"><span data-stu-id="f5108-303">Route order</span></span>

<span data-ttu-id="f5108-304">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="f5108-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="f5108-305">顺序</span><span class="sxs-lookup"><span data-stu-id="f5108-305">Order</span></span>            | <span data-ttu-id="f5108-306">行为</span><span class="sxs-lookup"><span data-stu-id="f5108-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="f5108-307">-1</span><span class="sxs-lookup"><span data-stu-id="f5108-307">-1</span></span>               | <span data-ttu-id="f5108-308">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="f5108-309">0</span><span class="sxs-lookup"><span data-stu-id="f5108-309">0</span></span>                | <span data-ttu-id="f5108-310">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="f5108-310">Order isn't specified (default value).</span></span> <span data-ttu-id="f5108-311">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="f5108-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="f5108-312">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="f5108-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="f5108-313">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="f5108-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="f5108-314">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="f5108-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="f5108-315">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="f5108-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="f5108-316">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="f5108-317">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="f5108-318">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="f5108-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="f5108-319">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="f5108-320">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="f5108-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="f5108-321">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="f5108-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="f5108-322">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="f5108-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="f5108-323">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="f5108-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="f5108-324"> Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="f5108-324"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="f5108-325">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="f5108-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="f5108-326">模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-326">Model conventions</span></span>

<span data-ttu-id="f5108-327">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="f5108-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="f5108-328">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="f5108-329">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="f5108-330">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-331"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="f5108-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="f5108-332">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="f5108-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="f5108-333">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="f5108-334">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="f5108-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="f5108-335">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f5108-336">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f5108-337">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="f5108-338">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f5108-339">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f5108-340">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-341">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-342">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-343">将 MVC 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 Razor Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="f5108-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5108-344">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="f5108-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f5108-345">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="f5108-348">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="f5108-349">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="f5108-350">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="f5108-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="f5108-351">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="f5108-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="f5108-352">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="f5108-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="f5108-353">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="f5108-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="f5108-354">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-355">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f5108-356">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="f5108-358">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="f5108-359">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-360">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="f5108-361">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-361">Page route action conventions</span></span>

<span data-ttu-id="f5108-362">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="f5108-363">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-363">Folder route model convention</span></span>

<span data-ttu-id="f5108-364">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="f5108-365">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f5108-366"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f5108-367">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="f5108-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f5108-368">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-369">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-370">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-371">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="f5108-374">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-374">Page route model convention</span></span>

<span data-ttu-id="f5108-375">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="f5108-376">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f5108-377"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f5108-378">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="f5108-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f5108-379">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-380">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-381">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-382">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="f5108-385">使用参数转换程序自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="f5108-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="f5108-386">可以使用参数转换程序自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="f5108-387">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="f5108-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="f5108-388">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="f5108-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="f5108-389">`PageRouteTransformerConvention` 页面路由模型约定将参数转换程序应用于应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="f5108-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="f5108-390">例如，/Pages/SubscriptionManagement/ViewAll.cshtml 处的 Razor Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写为 `/subscription-management/view-all`。</span><span class="sxs-lookup"><span data-stu-id="f5108-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="f5108-391">`PageRouteTransformerConvention` 仅转换来自 Razor Pages 文件夹和文件名的自动生成页面路由段。</span><span class="sxs-lookup"><span data-stu-id="f5108-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="f5108-392">它不会转换使用 `@page` 指令添加的路由段。</span><span class="sxs-lookup"><span data-stu-id="f5108-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="f5108-393">该约定也不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 添加的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="f5108-394">`PageRouteTransformerConvention` 在 `Startup.ConfigureServices` 中注册为选项：</span><span class="sxs-lookup"><span data-stu-id="f5108-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="f5108-395">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="f5108-395">Configure a page route</span></span>

<span data-ttu-id="f5108-396">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="f5108-397">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="f5108-398">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="f5108-399">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="f5108-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="f5108-400">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="f5108-401">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="f5108-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="f5108-402">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="f5108-403">请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="f5108-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="f5108-406">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="f5108-407">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="f5108-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="f5108-410">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-410">Page model action conventions</span></span>

<span data-ttu-id="f5108-411">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="f5108-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="f5108-412">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="f5108-413">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="f5108-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="f5108-414">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="f5108-415">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="f5108-415">**Folder app model convention**</span></span>

<span data-ttu-id="f5108-416">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="f5108-417">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="f5108-418">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="f5108-420">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="f5108-420">**Page app model convention**</span></span>

<span data-ttu-id="f5108-421">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="f5108-422">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="f5108-423">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="f5108-425">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="f5108-425">**Configure a filter**</span></span>

<span data-ttu-id="f5108-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="f5108-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="f5108-427">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="f5108-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="f5108-428">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="f5108-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="f5108-429">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="f5108-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="f5108-430">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="f5108-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="f5108-431">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="f5108-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="f5108-432">由于 Razor Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="f5108-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="f5108-433">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="f5108-435">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="f5108-435">**Configure a filter factory**</span></span>

<span data-ttu-id="f5108-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="f5108-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="f5108-437">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="f5108-438">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="f5108-439">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="f5108-441">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="f5108-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="f5108-442">Razor Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="f5108-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="f5108-443">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="f5108-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="f5108-444">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="f5108-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="f5108-445">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 Razor Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="f5108-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="f5108-446">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="f5108-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5108-447">其他资源</span><span class="sxs-lookup"><span data-stu-id="f5108-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f5108-448">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="f5108-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="f5108-449">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="f5108-450">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="f5108-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="f5108-451">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="f5108-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="f5108-452">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="f5108-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="f5108-453">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="f5108-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="f5108-454">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f5108-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="f5108-455">方案</span><span class="sxs-lookup"><span data-stu-id="f5108-455">Scenario</span></span> | <span data-ttu-id="f5108-456">示例演示...</span><span class="sxs-lookup"><span data-stu-id="f5108-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="f5108-457">模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="f5108-458">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="f5108-458">Conventions.Add</span></span><ul><li><span data-ttu-id="f5108-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="f5108-462">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="f5108-463">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="f5108-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="f5108-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="f5108-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="f5108-467">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="f5108-468">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="f5108-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="f5108-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="f5108-471">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="f5108-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="f5108-472">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="f5108-473">使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法向 `Startup` 类中服务集合的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 添加和配置 Razor Pages 约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="f5108-474">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="f5108-474">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="f5108-475">路由顺序</span><span class="sxs-lookup"><span data-stu-id="f5108-475">Route order</span></span>

<span data-ttu-id="f5108-476">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="f5108-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="f5108-477">顺序</span><span class="sxs-lookup"><span data-stu-id="f5108-477">Order</span></span>            | <span data-ttu-id="f5108-478">行为</span><span class="sxs-lookup"><span data-stu-id="f5108-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="f5108-479">-1</span><span class="sxs-lookup"><span data-stu-id="f5108-479">-1</span></span>               | <span data-ttu-id="f5108-480">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="f5108-481">0</span><span class="sxs-lookup"><span data-stu-id="f5108-481">0</span></span>                | <span data-ttu-id="f5108-482">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="f5108-482">Order isn't specified (default value).</span></span> <span data-ttu-id="f5108-483">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="f5108-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="f5108-484">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="f5108-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="f5108-485">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="f5108-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="f5108-486">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="f5108-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="f5108-487">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="f5108-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="f5108-488">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="f5108-489">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="f5108-490">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="f5108-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="f5108-491">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="f5108-492">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="f5108-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="f5108-493">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="f5108-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="f5108-494">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="f5108-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="f5108-495">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="f5108-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

Razor<span data-ttu-id="f5108-496"> Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="f5108-496"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="f5108-497">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="f5108-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="f5108-498">模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-498">Model conventions</span></span>

<span data-ttu-id="f5108-499">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="f5108-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="f5108-500">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="f5108-501">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="f5108-502">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-503"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="f5108-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="f5108-504">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="f5108-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="f5108-505">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="f5108-506">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="f5108-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="f5108-507">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f5108-508">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f5108-509">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="f5108-510">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="f5108-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="f5108-511">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="f5108-512">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-513">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-514">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-515">将 MVC 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 Razor Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="f5108-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5108-516">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="f5108-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="f5108-517">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="f5108-520">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="f5108-521">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="f5108-522">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="f5108-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="f5108-523">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="f5108-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="f5108-524">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="f5108-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="f5108-525">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="f5108-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="f5108-526">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-527">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="f5108-528">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="f5108-530">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="f5108-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="f5108-531">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="f5108-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="f5108-532">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="f5108-533">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-533">Page route action conventions</span></span>

<span data-ttu-id="f5108-534">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="f5108-535">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-535">Folder route model convention</span></span>

<span data-ttu-id="f5108-536">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="f5108-537">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="f5108-538"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f5108-539">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="f5108-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f5108-540">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-541">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-542">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-543">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="f5108-546">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="f5108-546">Page route model convention</span></span>

<span data-ttu-id="f5108-547">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="f5108-548">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="f5108-549"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="f5108-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="f5108-550">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="f5108-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="f5108-551">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="f5108-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="f5108-552">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="f5108-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="f5108-553">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="f5108-554">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="f5108-557">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="f5108-557">Configure a page route</span></span>

<span data-ttu-id="f5108-558">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="f5108-559">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="f5108-560">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="f5108-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="f5108-561">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="f5108-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="f5108-562">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="f5108-563">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="f5108-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="f5108-564">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="f5108-565">请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="f5108-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="f5108-568">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="f5108-569">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="f5108-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="f5108-572">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="f5108-572">Page model action conventions</span></span>

<span data-ttu-id="f5108-573">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="f5108-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="f5108-574">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="f5108-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="f5108-575">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="f5108-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="f5108-576">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="f5108-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="f5108-577">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="f5108-577">**Folder app model convention**</span></span>

<span data-ttu-id="f5108-578">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="f5108-579">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="f5108-580">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="f5108-582">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="f5108-582">**Page app model convention**</span></span>

<span data-ttu-id="f5108-583">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="f5108-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="f5108-584">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="f5108-585">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="f5108-587">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="f5108-587">**Configure a filter**</span></span>

<span data-ttu-id="f5108-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="f5108-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="f5108-589">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="f5108-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="f5108-590">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="f5108-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="f5108-591">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="f5108-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="f5108-592">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="f5108-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="f5108-593">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="f5108-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="f5108-594">由于 Razor Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="f5108-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="f5108-595">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="f5108-597">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="f5108-597">**Configure a filter factory**</span></span>

<span data-ttu-id="f5108-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="f5108-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="f5108-599">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="f5108-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="f5108-600">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="f5108-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="f5108-601">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="f5108-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="f5108-603">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="f5108-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="f5108-604">Razor Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="f5108-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="f5108-605">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="f5108-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="f5108-606">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="f5108-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="f5108-607">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 Razor Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="f5108-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="f5108-608">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="f5108-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5108-609">其他资源</span><span class="sxs-lookup"><span data-stu-id="f5108-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
