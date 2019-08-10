---
title: ASP.NET Core 中 Razor 页面的路由和应用约定
author: guardrex
description: 了解路由和应用模型提供程序约定如何帮助控制页面路由、发现和处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914979"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="9ec8a-103">ASP.NET Core 中 Razor 页面的路由和应用约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="9ec8a-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9ec8a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9ec8a-105">了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="9ec8a-106">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="9ec8a-107">若要指定页路由、添加路由段或向路由添加参数, 请使用页的`@page`指令。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="9ec8a-108">有关详细信息, 请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="9ec8a-109">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="9ec8a-110">有关详细信息, 请[参阅路由:保留的路由](xref:fundamentals/routing#reserved-routing-names)名称。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="9ec8a-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9ec8a-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="9ec8a-112">应用场景</span><span class="sxs-lookup"><span data-stu-id="9ec8a-112">Scenario</span></span> | <span data-ttu-id="9ec8a-113">示例演示...</span><span class="sxs-lookup"><span data-stu-id="9ec8a-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="9ec8a-114">模型约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="9ec8a-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="9ec8a-115">Conventions.Add</span></span><ul><li><span data-ttu-id="9ec8a-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9ec8a-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="9ec8a-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9ec8a-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="9ec8a-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="9ec8a-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="9ec8a-119">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="9ec8a-120">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="9ec8a-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9ec8a-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="9ec8a-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="9ec8a-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="9ec8a-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="9ec8a-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="9ec8a-124">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="9ec8a-125">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="9ec8a-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9ec8a-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="9ec8a-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="9ec8a-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="9ec8a-128">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="9ec8a-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="9ec8a-129">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="9ec8a-130">使用<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>扩展`Startup`方法在类中的服务集合上添加并配置 Razor Pages 约定。 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*></span><span class="sxs-lookup"><span data-stu-id="9ec8a-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="9ec8a-131">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-131">The following convention examples are explained later in this topic:</span></span>

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

## <a name="route-order"></a><span data-ttu-id="9ec8a-132">路由顺序</span><span class="sxs-lookup"><span data-stu-id="9ec8a-132">Route order</span></span>

<span data-ttu-id="9ec8a-133">路由指定一个<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>用于处理 (路由匹配)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="9ec8a-134">顺序</span><span class="sxs-lookup"><span data-stu-id="9ec8a-134">Order</span></span>            | <span data-ttu-id="9ec8a-135">行为</span><span class="sxs-lookup"><span data-stu-id="9ec8a-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="9ec8a-136">-1</span><span class="sxs-lookup"><span data-stu-id="9ec8a-136">-1</span></span>               | <span data-ttu-id="9ec8a-137">在处理其他路由之前处理路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="9ec8a-138">0</span><span class="sxs-lookup"><span data-stu-id="9ec8a-138">0</span></span>                | <span data-ttu-id="9ec8a-139">未指定顺序 (默认值)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-139">Order isn't specified (default value).</span></span> <span data-ttu-id="9ec8a-140">如果不`Order`分配`Order = null`(), 则`Order`默认路由设置为 0 (零) 进行处理。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="9ec8a-141">1、2、 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="9ec8a-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="9ec8a-142">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="9ec8a-143">按约定建立路由处理:</span><span class="sxs-lookup"><span data-stu-id="9ec8a-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="9ec8a-144">按顺序处理路由 (-1、0、1、2、 &hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="9ec8a-145">当路由具有相同`Order`的时, 将首先匹配最具体的路由, 然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="9ec8a-146">当具有相同`Order`和相同数量的参数的路由匹配请求 URL 时, 将按照它们添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>到中的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="9ec8a-147">如果可能, 请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="9ec8a-148">通常, 路由将选择 URL 匹配的正确路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="9ec8a-149">如果必须将路由`Order`属性设置为正确路由请求, 则应用的路由方案可能会使客户端和脆弱, 使其保持不变。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="9ec8a-150">设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="9ec8a-151">该示例应用需要显式路由处理顺序才能使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="9ec8a-152">但是, 您应尝试避免在生产应用程序中设置`Order`路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="9ec8a-153">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="9ec8a-154">有关 MVC 主题中的路由顺序的信息, [请参阅路由到控制器操作:排序属性路由](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="9ec8a-155">模型约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-155">Model conventions</span></span>

<span data-ttu-id="9ec8a-156">为添加委托<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> , 以添加适用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="9ec8a-157">向所有页面添加路由模型约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="9ec8a-158">使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建和<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>添加到在页路由模型构造<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>期间应用的实例集合。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="9ec8a-159">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9ec8a-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="9ec8a-161">这可确保示例应用中的以下路由匹配行为:</span><span class="sxs-lookup"><span data-stu-id="9ec8a-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="9ec8a-162">本主题后面添加`TheContactPage/{text?}`了的路由模板。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="9ec8a-163">联系人页路由的默认顺序为`null` (`Order = 0`), `{globalTemplate?}`因此它在路由模板之前匹配。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="9ec8a-164">稍后会在主题中添加路由模板。`{aboutTemplate?}`</span><span class="sxs-lookup"><span data-stu-id="9ec8a-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="9ec8a-165">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="9ec8a-166">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="9ec8a-167">稍后会在主题中添加路由模板。`{otherPagesTemplate?}`</span><span class="sxs-lookup"><span data-stu-id="9ec8a-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="9ec8a-168">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="9ec8a-169">当使用路由参数请求*Pages/OtherPages*文件夹中`/OtherPages/Page1/RouteDataValue`的任何页面 (例如,) 时, 会将 "RouteDataValue" 加载到`Order = 1` `RouteData.Values["globalTemplate"]` () 而不`RouteData.Values["otherPagesTemplate"]`是 (`Order = 2`), 因为设置`Order`属性。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9ec8a-170">如果可能, 请不要设置`Order`, 这会`Order = 0`导致。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9ec8a-171">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9ec8a-172">当 MVC 添加到中<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> `Startup.ConfigureServices`的服务集合时, 将添加 Razor Pages 选项 (如添加)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9ec8a-173">有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9ec8a-174">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="9ec8a-177">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="9ec8a-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="9ec8a-178">用于<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建并<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>将添加到在页面应用模型<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>构造期间应用的实例集合。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="9ec8a-179">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="9ec8a-180">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="9ec8a-181">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="9ec8a-182">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="9ec8a-183">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9ec8a-184">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-184">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="9ec8a-185">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="9ec8a-187">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="9ec8a-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="9ec8a-188">用于<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建并<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>将添加到在页处理程序<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>模型构造期间应用的实例集合。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9ec8a-189">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-189">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a><span data-ttu-id="9ec8a-190">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-190">Page route action conventions</span></span>

<span data-ttu-id="9ec8a-191">派生自<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>的默认路由模型提供程序调用约定, 这些约定旨在提供扩展点来配置页路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="9ec8a-192">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-192">Folder route model convention</span></span>

<span data-ttu-id="9ec8a-193">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>创建并添加一个<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , 它对指定文件夹下的<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>所有页面调用上的操作。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="9ec8a-194">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="9ec8a-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="9ec8a-196">这可确保在提供单个`{globalTemplate?}`路由值时, 将的模板`1`(在主题中设置为) 的优先级指定为第一个路由数据值位置的优先级。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="9ec8a-197">如果使用路由参数值*请求 Pages/OtherPages*文件夹中的页 (例如, `/OtherPages/Page1/RouteDataValue`), 则将 "RouteDataValue" 加载到`RouteData.Values["globalTemplate"]` (`Order = 1`) 而不`RouteData.Values["otherPagesTemplate"]`是 (`Order = 2`), 因为设置`Order`属性。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9ec8a-198">如果可能, 请不要设置`Order`, 这会`Order = 0`导致。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9ec8a-199">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9ec8a-200">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="9ec8a-203">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-203">Page route model convention</span></span>

<span data-ttu-id="9ec8a-204">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>创建并添加一个<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , 它在具有指定名称的<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>页上调用的操作。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="9ec8a-205">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<span data-ttu-id="9ec8a-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="9ec8a-207">这可确保在提供单个`{globalTemplate?}`路由值时, 将的模板`1`(在主题中设置为) 的优先级指定为第一个路由数据值位置的优先级。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="9ec8a-208">`/About/RouteDataValue`如果使用中的路由参数值请求 "关于" 页, 则将 "RouteDataValue" 加载`RouteData.Values["globalTemplate"]`到`Order = 1`() 而`RouteData.Values["aboutTemplate"]`不`Order = 2`是 (), 因为`Order`设置了属性。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="9ec8a-209">如果可能, 请不要设置`Order`, 这会`Order = 0`导致。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="9ec8a-210">依赖 "路由" 选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="9ec8a-211">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="9ec8a-214">使用参数转换器自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="9ec8a-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="9ec8a-215">可以使用参数转换器自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="9ec8a-216">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="9ec8a-217">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="9ec8a-218">`PageRouteTransformerConvention`页面路由模型约定将参数变压器应用到应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="9ec8a-219">例如, 位于 */Pages/SubscriptionManagement/ViewAll.cshtml*的 Razor Pages 文件会将其路由从`/SubscriptionManagement/ViewAll`重写到。 `/subscription-management/view-all`</span><span class="sxs-lookup"><span data-stu-id="9ec8a-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="9ec8a-220">`PageRouteTransformerConvention`仅转换来自 Razor Pages 文件夹和文件名的页面路由的自动生成段。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="9ec8a-221">它不会转换添加到指令中`@page`的路由段。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="9ec8a-222">该约定还不会转换添加的<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="9ec8a-223">注册为中`Startup.ConfigureServices`的一个选项: `PageRouteTransformerConvention`</span><span class="sxs-lookup"><span data-stu-id="9ec8a-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

::: moniker-end

## <a name="configure-a-page-route"></a><span data-ttu-id="9ec8a-224">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="9ec8a-224">Configure a page route</span></span>

<span data-ttu-id="9ec8a-225">用于<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>配置指向指定页面路径中的页面的路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="9ec8a-226">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="9ec8a-227">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="9ec8a-228">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

<span data-ttu-id="9ec8a-229">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="9ec8a-230">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="9ec8a-231">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

<span data-ttu-id="9ec8a-232">请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="9ec8a-235">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="9ec8a-236">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="9ec8a-239">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="9ec8a-239">Page model action conventions</span></span>

<span data-ttu-id="9ec8a-240">实现<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>的默认页面模型提供程序调用约定, 这些约定旨在提供扩展点来配置页面模型。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="9ec8a-241">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="9ec8a-242">对于本部分中的示例, 示例应用使用`AddHeaderAttribute`类, 该类是一个<xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, 它应用响应标头:</span><span class="sxs-lookup"><span data-stu-id="9ec8a-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9ec8a-243">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="9ec8a-244">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="9ec8a-244">**Folder app model convention**</span></span>

<span data-ttu-id="9ec8a-245">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>创建并添加一个<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , 它对指定文件夹下<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>的所有页面调用实例上的操作。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="9ec8a-246">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

<span data-ttu-id="9ec8a-247">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="9ec8a-249">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="9ec8a-249">**Page app model convention**</span></span>

<span data-ttu-id="9ec8a-250">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>创建并添加一个<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , 它在具有指定名称的<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>页上调用的操作。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="9ec8a-251">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

<span data-ttu-id="9ec8a-252">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="9ec8a-254">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="9ec8a-254">**Configure a filter**</span></span>

<span data-ttu-id="9ec8a-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="9ec8a-256">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

<span data-ttu-id="9ec8a-257">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="9ec8a-258">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="9ec8a-259">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="9ec8a-260">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="9ec8a-261">由于 Razor Pages 忽略操作筛选器, 因此, `EmptyFilter`如果路径不包含`OtherPages/Page2`, 则不会产生任何效果。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="9ec8a-262">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="9ec8a-264">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="9ec8a-264">**Configure a filter factory**</span></span>

<span data-ttu-id="9ec8a-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>配置指定的工厂以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="9ec8a-266">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

<span data-ttu-id="9ec8a-267">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-267">*AddHeaderWithFactory.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="9ec8a-268">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="9ec8a-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="9ec8a-270">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="9ec8a-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="9ec8a-271">Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="9ec8a-272">可以使用其他类型的 MVC 筛选器:[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="9ec8a-273">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="9ec8a-274">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 Razor Pages 的筛选器。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="9ec8a-275">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="9ec8a-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ec8a-276">其他资源</span><span class="sxs-lookup"><span data-stu-id="9ec8a-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
