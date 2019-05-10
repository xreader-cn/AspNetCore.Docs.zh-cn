---
title: ASP.NET Core 中 Razor 页面的路由和应用约定
author: guardrex
description: 了解路由和应用模型提供程序约定如何帮助控制页面路由、发现和处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 4e07b5803adbce94982584212fa65afbfd427b64
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893504"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="d7e51-103">ASP.NET Core 中 Razor 页面的路由和应用约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="d7e51-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d7e51-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d7e51-105">了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="d7e51-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="d7e51-106">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="d7e51-107">若要指定页面路由、 添加路由段，或将参数添加到路由，请使用页面的`@page`指令。</span><span class="sxs-lookup"><span data-stu-id="d7e51-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="d7e51-108">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="d7e51-109">有不能用作路由段或参数名称的保留的字。</span><span class="sxs-lookup"><span data-stu-id="d7e51-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="d7e51-110">有关详细信息，请参阅[路由：保留路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="d7e51-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d7e51-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="d7e51-112">方案</span><span class="sxs-lookup"><span data-stu-id="d7e51-112">Scenario</span></span> | <span data-ttu-id="d7e51-113">示例演示...</span><span class="sxs-lookup"><span data-stu-id="d7e51-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="d7e51-114">模型约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="d7e51-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="d7e51-115">Conventions.Add</span></span><ul><li><span data-ttu-id="d7e51-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="d7e51-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="d7e51-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="d7e51-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="d7e51-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="d7e51-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="d7e51-119">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="d7e51-120">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="d7e51-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="d7e51-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="d7e51-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="d7e51-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="d7e51-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="d7e51-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="d7e51-124">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="d7e51-125">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="d7e51-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="d7e51-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="d7e51-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="d7e51-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="d7e51-128">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="d7e51-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="d7e51-129">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="d7e51-130">Razor 页面约定添加和配置为使用<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>扩展方法<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>中的服务集合上`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="d7e51-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="d7e51-131">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="d7e51-131">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a><span data-ttu-id="d7e51-132">路由顺序</span><span class="sxs-lookup"><span data-stu-id="d7e51-132">Route order</span></span>

<span data-ttu-id="d7e51-133">路由指定<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>用于处理 （路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="d7e51-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="d7e51-134">顺序</span><span class="sxs-lookup"><span data-stu-id="d7e51-134">Order</span></span>            | <span data-ttu-id="d7e51-135">行为</span><span class="sxs-lookup"><span data-stu-id="d7e51-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="d7e51-136">-1</span><span class="sxs-lookup"><span data-stu-id="d7e51-136">-1</span></span>               | <span data-ttu-id="d7e51-137">处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="d7e51-138">0</span><span class="sxs-lookup"><span data-stu-id="d7e51-138">0</span></span>                | <span data-ttu-id="d7e51-139">未指定顺序 （默认值）。</span><span class="sxs-lookup"><span data-stu-id="d7e51-139">Order isn't specified (default value).</span></span> <span data-ttu-id="d7e51-140">不分配`Order`(`Order = null`) 将默认路由`Order`为 0 （零） 进行处理。</span><span class="sxs-lookup"><span data-stu-id="d7e51-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="d7e51-141">1、 2、 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="d7e51-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="d7e51-142">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="d7e51-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="d7e51-143">按照约定来建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="d7e51-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="d7e51-144">按顺序处理的路由 (-1、 0、 1、 2、 &hellip; n)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="d7e51-145">当路由具有相同`Order`、 最具体的路由匹配跟有更具体的路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="d7e51-146">当具有相同的路由`Order`和相同数量的参数匹配请求 URL，将它们添加到顺序处理路由<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>。</span><span class="sxs-lookup"><span data-stu-id="d7e51-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="d7e51-147">如果可能，避免具体取决于已建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="d7e51-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="d7e51-148">通常情况下，路由选择与 URL 匹配的正确路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="d7e51-149">如果必须设置路由`Order`属性路由请求是否正确，应用程序的路由方案，可能令人困惑，向客户端和脆弱来维护。</span><span class="sxs-lookup"><span data-stu-id="d7e51-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="d7e51-150">查找以简化应用程序的路由方案。</span><span class="sxs-lookup"><span data-stu-id="d7e51-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="d7e51-151">示例应用程序需要处理订单，若要演示几个路由方案使用的单个应用程序的显式路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="d7e51-152">但是，您应尝试避免设置路由的做法`Order`生产应用中。</span><span class="sxs-lookup"><span data-stu-id="d7e51-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="d7e51-153">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="d7e51-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="d7e51-154">路由顺序 MVC 主题中的信息位于[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="d7e51-155">模型约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-155">Model conventions</span></span>

<span data-ttu-id="d7e51-156">添加的委托<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>以添加[建模约定](xref:mvc/controllers/application-model#conventions)适用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="d7e51-157">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="d7e51-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="d7e51-158">使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>到的集合<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>在页面路由过程中应用的实例模型构造。</span><span class="sxs-lookup"><span data-stu-id="d7e51-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="d7e51-159">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="d7e51-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="d7e51-160"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="d7e51-161">这可确保匹配行为示例应用程序中的以下路由：</span><span class="sxs-lookup"><span data-stu-id="d7e51-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="d7e51-162">路由模板`TheContactPage/{text?}`添加本主题后面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="d7e51-163">联系人页面路由的默认顺序`null`(`Order = 0`)，因此它匹配之前`{globalTemplate?}`路由模板。</span><span class="sxs-lookup"><span data-stu-id="d7e51-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="d7e51-164">`{aboutTemplate?}`路由模板添加本主题后面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="d7e51-165">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="d7e51-166">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="d7e51-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="d7e51-167">`{otherPagesTemplate?}`路由模板添加本主题后面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="d7e51-168">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="d7e51-169">中的任何页时*Pages/OtherPages*文件夹请求与路由参数 (例如， `/OtherPages/Page1/RouteDataValue`)，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 1`)，而不`RouteData.Values["otherPagesTemplate"]`(`Order = 2`)由于设置`Order`属性。</span><span class="sxs-lookup"><span data-stu-id="d7e51-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="d7e51-170">如有可能，未设置`Order`，这会导致`Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="d7e51-171">依赖于路由，以选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="d7e51-172">Razor 页面选项，例如，添加<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>，将添加到服务集合中添加 MVC 时`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d7e51-173">有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="d7e51-174">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="d7e51-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="d7e51-177">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="d7e51-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="d7e51-178">使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>到的集合<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>期间应用的实例页上的应用程序模型构建。</span><span class="sxs-lookup"><span data-stu-id="d7e51-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="d7e51-179">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="d7e51-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="d7e51-180">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="d7e51-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="d7e51-181">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="d7e51-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="d7e51-182">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="d7e51-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="d7e51-183">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="d7e51-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="d7e51-184">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="d7e51-184">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="d7e51-185">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="d7e51-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="d7e51-187">将处理程序模型约定添加到的所有页面</span><span class="sxs-lookup"><span data-stu-id="d7e51-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="d7e51-188">使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>到的集合<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>期间应用的实例页处理程序模型构建。</span><span class="sxs-lookup"><span data-stu-id="d7e51-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="d7e51-189">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="d7e51-189">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="d7e51-190">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-190">Page route action conventions</span></span>

<span data-ttu-id="d7e51-191">默认路由模型提供程序派生自<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="d7e51-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="d7e51-192">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-192">Folder route model convention</span></span>

<span data-ttu-id="d7e51-193">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它在调用操作<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>所有指定的文件夹下的页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="d7e51-194">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="d7e51-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="d7e51-195"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="d7e51-196">这可以保证的模板`{globalTemplate?}`(设置到主题中前面`1`) 提供单个路由值时，第一个路由数据值位置分配优先级。</span><span class="sxs-lookup"><span data-stu-id="d7e51-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="d7e51-197">如果在页*Pages/OtherPages*文件夹请求与路由参数值 (例如， `/OtherPages/Page1/RouteDataValue`)，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 1`)，而不`RouteData.Values["otherPagesTemplate"]`(`Order = 2`)由于设置`Order`属性。</span><span class="sxs-lookup"><span data-stu-id="d7e51-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="d7e51-198">如有可能，未设置`Order`，这会导致`Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="d7e51-199">依赖于路由，以选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="d7e51-200">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="d7e51-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="d7e51-203">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-203">Page route model convention</span></span>

<span data-ttu-id="d7e51-204">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它在调用操作<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>具有指定名称的页。</span><span class="sxs-lookup"><span data-stu-id="d7e51-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="d7e51-205">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="d7e51-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="d7e51-206"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="d7e51-207">这可以保证的模板`{globalTemplate?}`(设置到主题中前面`1`) 提供单个路由值时，第一个路由数据值位置分配优先级。</span><span class="sxs-lookup"><span data-stu-id="d7e51-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="d7e51-208">如果使用路由参数值在请求关于页面`/About/RouteDataValue`，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 1`)，而不`RouteData.Values["aboutTemplate"]`(`Order = 2`) 因设置而`Order`属性。</span><span class="sxs-lookup"><span data-stu-id="d7e51-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="d7e51-209">如有可能，未设置`Order`，这会导致`Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="d7e51-210">依赖于路由，以选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="d7e51-211">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="d7e51-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="d7e51-214">参数转换器用于自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="d7e51-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="d7e51-215">使用参数转换器可以自定义页面路由生成的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="d7e51-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="d7e51-216">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="d7e51-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="d7e51-217">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="d7e51-218">`PageRouteTransformerConvention`页面路由模型约定适用于在应用中自动生成的页路由文件夹和文件名称段参数转换器。</span><span class="sxs-lookup"><span data-stu-id="d7e51-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="d7e51-219">例如，Razor 页面文件位于 */Pages/SubscriptionManagement/ViewAll.cshtml*必须重写中其路由`/SubscriptionManagement/ViewAll`到`/subscription-management/view-all`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="d7e51-220">`PageRouteTransformerConvention` 仅将转换的页面路由来自 Razor 页面文件夹和文件名称自动生成的段。</span><span class="sxs-lookup"><span data-stu-id="d7e51-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="d7e51-221">它不会转换与添加的路由段`@page`指令。</span><span class="sxs-lookup"><span data-stu-id="d7e51-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="d7e51-222">约定也不会转换添加的路由<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>。</span><span class="sxs-lookup"><span data-stu-id="d7e51-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="d7e51-223">`PageRouteTransformerConvention`注册中的一个选项为`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d7e51-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

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

## <a name="configure-a-page-route"></a><span data-ttu-id="d7e51-224">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="d7e51-224">Configure a page route</span></span>

<span data-ttu-id="d7e51-225">使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>到页面的路由配置在指定的页的路径。</span><span class="sxs-lookup"><span data-stu-id="d7e51-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="d7e51-226">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="d7e51-227">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="d7e51-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="d7e51-228">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="d7e51-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="d7e51-229">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="d7e51-230">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="d7e51-231">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="d7e51-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="d7e51-232">请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="d7e51-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="d7e51-235">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="d7e51-236">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="d7e51-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="d7e51-239">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="d7e51-239">Page model action conventions</span></span>

<span data-ttu-id="d7e51-240">默认页面模型提供程序实现<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>调用旨在为页面模型配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="d7e51-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="d7e51-241">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="d7e51-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="d7e51-242">在本部分中的示例，示例应用使用`AddHeaderAttribute`类，该类是<xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>，应用的响应标头：</span><span class="sxs-lookup"><span data-stu-id="d7e51-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="d7e51-243">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="d7e51-244">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="d7e51-244">**Folder app model convention**</span></span>

<span data-ttu-id="d7e51-245">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它在调用操作<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>实例指定的文件夹下的所有页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="d7e51-246">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="d7e51-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="d7e51-247">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="d7e51-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="d7e51-249">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="d7e51-249">**Page app model convention**</span></span>

<span data-ttu-id="d7e51-250">使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它在调用操作<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>具有指定名称的页。</span><span class="sxs-lookup"><span data-stu-id="d7e51-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="d7e51-251">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="d7e51-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="d7e51-252">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="d7e51-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="d7e51-254">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="d7e51-254">**Configure a filter**</span></span>

<span data-ttu-id="d7e51-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定的筛选器应用。</span><span class="sxs-lookup"><span data-stu-id="d7e51-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="d7e51-256">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="d7e51-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="d7e51-257">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="d7e51-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="d7e51-258">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="d7e51-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="d7e51-259">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="d7e51-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="d7e51-260">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="d7e51-261">由于 Razor 页面会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 会按预期发出空操作指令。</span><span class="sxs-lookup"><span data-stu-id="d7e51-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` no-ops as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="d7e51-262">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="d7e51-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="d7e51-264">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="d7e51-264">**Configure a filter factory**</span></span>

<span data-ttu-id="d7e51-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定的工厂应用[筛选器](xref:mvc/controllers/filters)所有 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="d7e51-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="d7e51-266">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="d7e51-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="d7e51-267">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="d7e51-267">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="d7e51-268">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="d7e51-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="d7e51-270">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="d7e51-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="d7e51-271">Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="d7e51-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="d7e51-272">其他类型的 MVC 筛选器是可供你使用：[授权](xref:mvc/controllers/filters#authorization-filters)，[异常](xref:mvc/controllers/filters#exception-filters)，[资源](xref:mvc/controllers/filters#resource-filters)，并且[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="d7e51-273">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="d7e51-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="d7e51-274">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是适用于 Razor 页面的筛选器。</span><span class="sxs-lookup"><span data-stu-id="d7e51-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="d7e51-275">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="d7e51-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d7e51-276">其他资源</span><span class="sxs-lookup"><span data-stu-id="d7e51-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
