---
title: 'ASP.NET Core 中的 :::no-loc(Razor)::: Pages 路由和应用约定'
author: rick-anderson
description: 了解路由和应用模型提供程序约定如何帮助控制页面路由、发现和处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 2947bf0b697ca01f17d260b9f31aa3cc79d457b6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059866"
---
# <a name="no-locrazor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="841df-103">ASP.NET Core 中的 :::no-loc(Razor)::: Pages 路由和应用约定</span><span class="sxs-lookup"><span data-stu-id="841df-103">:::no-loc(Razor)::: Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="841df-104">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 :::no-loc(Razor)::: Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="841df-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in :::no-loc(Razor)::: Pages apps.</span></span>

<span data-ttu-id="841df-105">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="841df-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="841df-106">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="841df-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="841df-107">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="841df-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="841df-108">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="841df-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="841df-109">有关详细信息，请参阅[路由：保留的路由名称](xref:mvc/controllers/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="841df-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="841df-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="841df-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="841df-111">方案</span><span class="sxs-lookup"><span data-stu-id="841df-111">Scenario</span></span> | <span data-ttu-id="841df-112">示例演示...</span><span class="sxs-lookup"><span data-stu-id="841df-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="841df-113">模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="841df-114">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="841df-114">Conventions.Add</span></span><ul><li><span data-ttu-id="841df-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="841df-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="841df-118">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="841df-119">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="841df-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="841df-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="841df-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="841df-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="841df-123">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="841df-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="841df-124">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="841df-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-127">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="841df-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="841df-128">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="841df-129">:::no-loc(Razor)::: Pages 约定是使用在 `Startup.ConfigureServices` 中配置 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions> 的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.Add:::no-loc(Razor):::Pages%2A> 重载配置的。</span><span class="sxs-lookup"><span data-stu-id="841df-129">:::no-loc(Razor)::: Pages conventions are configured using an <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.Add:::no-loc(Razor):::Pages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="841df-130">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="841df-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Razor):::Pages(options =>
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

## <a name="route-order"></a><span data-ttu-id="841df-131">路由顺序</span><span class="sxs-lookup"><span data-stu-id="841df-131">Route order</span></span>

<span data-ttu-id="841df-132">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="841df-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="841df-133">顺序</span><span class="sxs-lookup"><span data-stu-id="841df-133">Order</span></span>            | <span data-ttu-id="841df-134">行为</span><span class="sxs-lookup"><span data-stu-id="841df-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="841df-135">-1</span><span class="sxs-lookup"><span data-stu-id="841df-135">-1</span></span>               | <span data-ttu-id="841df-136">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="841df-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="841df-137">0</span><span class="sxs-lookup"><span data-stu-id="841df-137">0</span></span>                | <span data-ttu-id="841df-138">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="841df-138">Order isn't specified (default value).</span></span> <span data-ttu-id="841df-139">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="841df-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="841df-140">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="841df-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="841df-141">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="841df-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="841df-142">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="841df-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="841df-143">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="841df-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="841df-144">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="841df-145">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="841df-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="841df-146">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="841df-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="841df-147">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="841df-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="841df-148">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="841df-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="841df-149">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="841df-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="841df-150">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="841df-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="841df-151">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="841df-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="841df-152">:::no-loc(Razor)::: Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="841df-152">:::no-loc(Razor)::: Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="841df-153">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="841df-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="841df-154">模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-154">Model conventions</span></span>

<span data-ttu-id="841df-155">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 :::no-loc(Razor)::: Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="841df-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to :::no-loc(Razor)::: Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="841df-156">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="841df-157">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-157">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="841df-158">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="841df-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-159"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="841df-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="841df-160">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="841df-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="841df-161">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="841df-162">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="841df-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="841df-163">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="841df-164">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="841df-165">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="841df-166">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="841df-167">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="841df-168">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-169">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-170">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-171">将 :::no-loc(Razor)::: Pages 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 :::no-loc(Razor)::: Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="841df-171">:::no-loc(Razor)::: Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions>, are added when :::no-loc(Razor)::: Pages is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="841df-172">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="841df-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="841df-173">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="841df-176">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="841df-177">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-177">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="841df-178">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="841df-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="841df-179">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="841df-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="841df-180">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="841df-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="841df-181">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="841df-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="841df-182">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="841df-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-183">*Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-183">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="841df-184">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="841df-186">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="841df-187">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-187">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-188">*Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-188">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="841df-189">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-189">Page route action conventions</span></span>

<span data-ttu-id="841df-190">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="841df-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="841df-191">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-191">Folder route model convention</span></span>

<span data-ttu-id="841df-192">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="841df-193">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="841df-194"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="841df-195">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="841df-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="841df-196">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-197">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-198">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-199">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="841df-202">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-202">Page route model convention</span></span>

<span data-ttu-id="841df-203">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="841df-204">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="841df-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="841df-205"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="841df-206">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="841df-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="841df-207">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-208">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-209">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-210">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="841df-213">使用参数转换程序自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="841df-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="841df-214">可以使用参数转换程序自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="841df-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="841df-215">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="841df-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="841df-216">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="841df-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="841df-217">`PageRouteTransformerConvention` 页面路由模型约定将参数转换程序应用于应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="841df-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="841df-218">例如，/Pages/SubscriptionManagement/ViewAll.cshtml 处的 :::no-loc(Razor)::: Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写为 `/subscription-management/view-all`。</span><span class="sxs-lookup"><span data-stu-id="841df-218">For example, the :::no-loc(Razor)::: Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="841df-219">`PageRouteTransformerConvention` 仅转换来自 :::no-loc(Razor)::: Pages 文件夹和文件名的自动生成页面路由段。</span><span class="sxs-lookup"><span data-stu-id="841df-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the :::no-loc(Razor)::: Pages folder and file name.</span></span> <span data-ttu-id="841df-220">它不会转换使用 `@page` 指令添加的路由段。</span><span class="sxs-lookup"><span data-stu-id="841df-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="841df-221">该约定也不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 添加的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="841df-222">`PageRouteTransformerConvention` 在 `Startup.ConfigureServices` 中注册为选项：</span><span class="sxs-lookup"><span data-stu-id="841df-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Razor):::Pages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="841df-223">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="841df-223">Configure a page route</span></span>

<span data-ttu-id="841df-224">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="841df-225">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="841df-226">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="841df-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="841df-227">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="841df-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="841df-228">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="841df-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="841df-229">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="841df-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="841df-230">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="841df-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="841df-231">请注意，在呈现的页面中，为 **联系人** 链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="841df-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="841df-234">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="841df-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="841df-235">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="841df-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="841df-238">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-238">Page model action conventions</span></span>

<span data-ttu-id="841df-239">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="841df-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="841df-240">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="841df-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="841df-241">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="841df-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="841df-242">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="841df-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="841df-243">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="841df-243">**Folder app model convention**</span></span>

<span data-ttu-id="841df-244">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="841df-245">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="841df-246">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="841df-248">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="841df-248">**Page app model convention**</span></span>

<span data-ttu-id="841df-249">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="841df-250">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="841df-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="841df-251">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="841df-253">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="841df-253">**Configure a filter**</span></span>

<span data-ttu-id="841df-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="841df-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="841df-255">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="841df-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="841df-256">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="841df-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="841df-257">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="841df-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="841df-258">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="841df-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="841df-259">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="841df-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="841df-260">由于 :::no-loc(Razor)::: Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="841df-260">Since Action filters are ignored by :::no-loc(Razor)::: Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="841df-261">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="841df-263">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="841df-263">**Configure a filter factory**</span></span>

<span data-ttu-id="841df-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 :::no-loc(Razor)::: Pages。</span><span class="sxs-lookup"><span data-stu-id="841df-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="841df-265">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="841df-266">*AddHeaderWithFactory.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-266">*AddHeaderWithFactory.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="841df-267">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="841df-269">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="841df-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="841df-270">:::no-loc(Razor)::: Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 :::no-loc(Razor)::: Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="841df-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by :::no-loc(Razor)::: Pages, since :::no-loc(Razor)::: Pages use handler methods.</span></span> <span data-ttu-id="841df-271">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="841df-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="841df-272">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="841df-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="841df-273">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 :::no-loc(Razor)::: Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="841df-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="841df-274">有关详细信息，请参阅 [:::no-loc(Razor)::: Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="841df-274">For more information, see [Filter methods for :::no-loc(Razor)::: Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="841df-275">其他资源</span><span class="sxs-lookup"><span data-stu-id="841df-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="841df-276">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 :::no-loc(Razor)::: Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="841df-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in :::no-loc(Razor)::: Pages apps.</span></span>

<span data-ttu-id="841df-277">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="841df-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="841df-278">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="841df-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="841df-279">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="841df-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="841df-280">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="841df-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="841df-281">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="841df-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="841df-282">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="841df-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="841df-283">方案</span><span class="sxs-lookup"><span data-stu-id="841df-283">Scenario</span></span> | <span data-ttu-id="841df-284">示例演示...</span><span class="sxs-lookup"><span data-stu-id="841df-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="841df-285">模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="841df-286">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="841df-286">Conventions.Add</span></span><ul><li><span data-ttu-id="841df-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="841df-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="841df-290">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="841df-291">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="841df-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="841df-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="841df-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="841df-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="841df-295">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="841df-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="841df-296">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="841df-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-299">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="841df-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="841df-300">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="841df-301">使用 <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 扩展方法向 `Startup` 类中服务集合的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 添加和配置 :::no-loc(Razor)::: Pages 约定。</span><span class="sxs-lookup"><span data-stu-id="841df-301">:::no-loc(Razor)::: Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="841df-302">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="841df-302">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .Add:::no-loc(Razor):::PagesOptions(options =>
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

## <a name="route-order"></a><span data-ttu-id="841df-303">路由顺序</span><span class="sxs-lookup"><span data-stu-id="841df-303">Route order</span></span>

<span data-ttu-id="841df-304">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="841df-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="841df-305">顺序</span><span class="sxs-lookup"><span data-stu-id="841df-305">Order</span></span>            | <span data-ttu-id="841df-306">行为</span><span class="sxs-lookup"><span data-stu-id="841df-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="841df-307">-1</span><span class="sxs-lookup"><span data-stu-id="841df-307">-1</span></span>               | <span data-ttu-id="841df-308">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="841df-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="841df-309">0</span><span class="sxs-lookup"><span data-stu-id="841df-309">0</span></span>                | <span data-ttu-id="841df-310">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="841df-310">Order isn't specified (default value).</span></span> <span data-ttu-id="841df-311">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="841df-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="841df-312">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="841df-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="841df-313">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="841df-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="841df-314">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="841df-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="841df-315">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="841df-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="841df-316">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="841df-317">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="841df-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="841df-318">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="841df-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="841df-319">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="841df-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="841df-320">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="841df-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="841df-321">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="841df-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="841df-322">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="841df-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="841df-323">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="841df-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="841df-324">:::no-loc(Razor)::: Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="841df-324">:::no-loc(Razor)::: Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="841df-325">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="841df-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="841df-326">模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-326">Model conventions</span></span>

<span data-ttu-id="841df-327">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 :::no-loc(Razor)::: Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="841df-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to :::no-loc(Razor)::: Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="841df-328">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="841df-329">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-329">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="841df-330">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="841df-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-331"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="841df-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="841df-332">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="841df-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="841df-333">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="841df-334">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="841df-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="841df-335">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="841df-336">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="841df-337">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="841df-338">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="841df-339">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="841df-340">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-341">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-342">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-343">将 MVC 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 :::no-loc(Razor)::: Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="841df-343">:::no-loc(Razor)::: Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="841df-344">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="841df-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="841df-345">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="841df-348">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="841df-349">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-349">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="841df-350">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="841df-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="841df-351">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="841df-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="841df-352">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="841df-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="841df-353">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="841df-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="841df-354">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="841df-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-355">*Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-355">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="841df-356">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="841df-358">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="841df-359">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-359">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-360">*Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-360">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="841df-361">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-361">Page route action conventions</span></span>

<span data-ttu-id="841df-362">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="841df-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="841df-363">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-363">Folder route model convention</span></span>

<span data-ttu-id="841df-364">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="841df-365">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="841df-366"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="841df-367">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="841df-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="841df-368">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-369">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-370">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-371">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="841df-374">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-374">Page route model convention</span></span>

<span data-ttu-id="841df-375">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="841df-376">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="841df-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="841df-377"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="841df-378">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="841df-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="841df-379">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-380">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-381">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-382">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="841df-385">使用参数转换程序自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="841df-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="841df-386">可以使用参数转换程序自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="841df-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="841df-387">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="841df-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="841df-388">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="841df-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="841df-389">`PageRouteTransformerConvention` 页面路由模型约定将参数转换程序应用于应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="841df-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="841df-390">例如，/Pages/SubscriptionManagement/ViewAll.cshtml 处的 :::no-loc(Razor)::: Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写为 `/subscription-management/view-all`。</span><span class="sxs-lookup"><span data-stu-id="841df-390">For example, the :::no-loc(Razor)::: Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="841df-391">`PageRouteTransformerConvention` 仅转换来自 :::no-loc(Razor)::: Pages 文件夹和文件名的自动生成页面路由段。</span><span class="sxs-lookup"><span data-stu-id="841df-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the :::no-loc(Razor)::: Pages folder and file name.</span></span> <span data-ttu-id="841df-392">它不会转换使用 `@page` 指令添加的路由段。</span><span class="sxs-lookup"><span data-stu-id="841df-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="841df-393">该约定也不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 添加的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="841df-394">`PageRouteTransformerConvention` 在 `Startup.ConfigureServices` 中注册为选项：</span><span class="sxs-lookup"><span data-stu-id="841df-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .Add:::no-loc(Razor):::PagesOptions(options =>
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

## <a name="configure-a-page-route"></a><span data-ttu-id="841df-395">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="841df-395">Configure a page route</span></span>

<span data-ttu-id="841df-396">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="841df-397">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="841df-398">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="841df-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="841df-399">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="841df-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="841df-400">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="841df-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="841df-401">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="841df-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="841df-402">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="841df-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="841df-403">请注意，在呈现的页面中，为 **联系人** 链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="841df-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="841df-406">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="841df-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="841df-407">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="841df-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="841df-410">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-410">Page model action conventions</span></span>

<span data-ttu-id="841df-411">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="841df-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="841df-412">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="841df-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="841df-413">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="841df-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="841df-414">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="841df-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="841df-415">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="841df-415">**Folder app model convention**</span></span>

<span data-ttu-id="841df-416">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="841df-417">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="841df-418">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="841df-420">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="841df-420">**Page app model convention**</span></span>

<span data-ttu-id="841df-421">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="841df-422">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="841df-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="841df-423">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="841df-425">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="841df-425">**Configure a filter**</span></span>

<span data-ttu-id="841df-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="841df-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="841df-427">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="841df-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="841df-428">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="841df-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="841df-429">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="841df-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="841df-430">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="841df-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="841df-431">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="841df-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="841df-432">由于 :::no-loc(Razor)::: Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="841df-432">Since Action filters are ignored by :::no-loc(Razor)::: Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="841df-433">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="841df-435">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="841df-435">**Configure a filter factory**</span></span>

<span data-ttu-id="841df-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 :::no-loc(Razor)::: Pages。</span><span class="sxs-lookup"><span data-stu-id="841df-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="841df-437">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="841df-438">*AddHeaderWithFactory.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-438">*AddHeaderWithFactory.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="841df-439">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="841df-441">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="841df-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="841df-442">:::no-loc(Razor)::: Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 :::no-loc(Razor)::: Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="841df-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by :::no-loc(Razor)::: Pages, since :::no-loc(Razor)::: Pages use handler methods.</span></span> <span data-ttu-id="841df-443">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="841df-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="841df-444">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="841df-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="841df-445">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 :::no-loc(Razor)::: Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="841df-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="841df-446">有关详细信息，请参阅 [:::no-loc(Razor)::: Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="841df-446">For more information, see [Filter methods for :::no-loc(Razor)::: Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="841df-447">其他资源</span><span class="sxs-lookup"><span data-stu-id="841df-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="841df-448">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 :::no-loc(Razor)::: Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="841df-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in :::no-loc(Razor)::: Pages apps.</span></span>

<span data-ttu-id="841df-449">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="841df-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="841df-450">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="841df-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="841df-451">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="841df-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="841df-452">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="841df-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="841df-453">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="841df-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="841df-454">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="841df-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="841df-455">方案</span><span class="sxs-lookup"><span data-stu-id="841df-455">Scenario</span></span> | <span data-ttu-id="841df-456">示例演示...</span><span class="sxs-lookup"><span data-stu-id="841df-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="841df-457">模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="841df-458">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="841df-458">Conventions.Add</span></span><ul><li><span data-ttu-id="841df-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="841df-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="841df-462">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="841df-463">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="841df-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="841df-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="841df-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="841df-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="841df-467">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="841df-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="841df-468">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="841df-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="841df-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="841df-471">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="841df-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="841df-472">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="841df-473">使用 <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 扩展方法向 `Startup` 类中服务集合的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 添加和配置 :::no-loc(Razor)::: Pages 约定。</span><span class="sxs-lookup"><span data-stu-id="841df-473">:::no-loc(Razor)::: Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="841df-474">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="841df-474">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .Add:::no-loc(Razor):::PagesOptions(options =>
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

## <a name="route-order"></a><span data-ttu-id="841df-475">路由顺序</span><span class="sxs-lookup"><span data-stu-id="841df-475">Route order</span></span>

<span data-ttu-id="841df-476">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="841df-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="841df-477">顺序</span><span class="sxs-lookup"><span data-stu-id="841df-477">Order</span></span>            | <span data-ttu-id="841df-478">行为</span><span class="sxs-lookup"><span data-stu-id="841df-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="841df-479">-1</span><span class="sxs-lookup"><span data-stu-id="841df-479">-1</span></span>               | <span data-ttu-id="841df-480">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="841df-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="841df-481">0</span><span class="sxs-lookup"><span data-stu-id="841df-481">0</span></span>                | <span data-ttu-id="841df-482">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="841df-482">Order isn't specified (default value).</span></span> <span data-ttu-id="841df-483">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="841df-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="841df-484">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="841df-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="841df-485">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="841df-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="841df-486">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="841df-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="841df-487">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="841df-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="841df-488">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="841df-489">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="841df-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="841df-490">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="841df-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="841df-491">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="841df-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="841df-492">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="841df-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="841df-493">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="841df-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="841df-494">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="841df-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="841df-495">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="841df-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="841df-496">:::no-loc(Razor)::: Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="841df-496">:::no-loc(Razor)::: Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="841df-497">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="841df-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="841df-498">模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-498">Model conventions</span></span>

<span data-ttu-id="841df-499">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 :::no-loc(Razor)::: Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="841df-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to :::no-loc(Razor)::: Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="841df-500">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="841df-501">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-501">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="841df-502">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="841df-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-503"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="841df-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="841df-504">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="841df-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="841df-505">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="841df-506">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="841df-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="841df-507">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="841df-508">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="841df-509">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="841df-510">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="841df-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="841df-511">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="841df-512">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-513">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-514">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-515">将 MVC 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 :::no-loc(Razor)::: Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="841df-515">:::no-loc(Razor)::: Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="841df-516">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="841df-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="841df-517">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="841df-520">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="841df-521">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-521">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="841df-522">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="841df-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="841df-523">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="841df-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="841df-524">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="841df-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="841df-525">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="841df-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="841df-526">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="841df-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-527">*Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-527">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="841df-528">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="841df-530">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="841df-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="841df-531">使用 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="841df-531">Use <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.:::no-loc(Razor):::PagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="841df-532">*Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-532">*Startup.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="841df-533">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-533">Page route action conventions</span></span>

<span data-ttu-id="841df-534">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="841df-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="841df-535">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-535">Folder route model convention</span></span>

<span data-ttu-id="841df-536">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="841df-537">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="841df-538"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="841df-539">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="841df-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="841df-540">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-541">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-542">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-543">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="841df-546">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="841df-546">Page route model convention</span></span>

<span data-ttu-id="841df-547">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="841df-548">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="841df-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="841df-549"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="841df-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="841df-550">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="841df-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="841df-551">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="841df-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="841df-552">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="841df-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="841df-553">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="841df-554">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="841df-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="841df-557">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="841df-557">Configure a page route</span></span>

<span data-ttu-id="841df-558">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="841df-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="841df-559">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="841df-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="841df-560">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="841df-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="841df-561">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="841df-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="841df-562">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="841df-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="841df-563">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="841df-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="841df-564">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="841df-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="841df-565">请注意，在呈现的页面中，为 **联系人** 链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="841df-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="841df-568">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="841df-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="841df-569">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="841df-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="841df-572">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="841df-572">Page model action conventions</span></span>

<span data-ttu-id="841df-573">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="841df-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="841df-574">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="841df-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="841df-575">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="841df-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="841df-576">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="841df-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="841df-577">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="841df-577">**Folder app model convention**</span></span>

<span data-ttu-id="841df-578">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="841df-579">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="841df-580">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="841df-582">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="841df-582">**Page app model convention**</span></span>

<span data-ttu-id="841df-583">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="841df-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="841df-584">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="841df-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="841df-585">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="841df-587">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="841df-587">**Configure a filter**</span></span>

<span data-ttu-id="841df-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="841df-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="841df-589">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="841df-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="841df-590">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="841df-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="841df-591">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="841df-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="841df-592">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="841df-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="841df-593">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="841df-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="841df-594">由于 :::no-loc(Razor)::: Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="841df-594">Since Action filters are ignored by :::no-loc(Razor)::: Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="841df-595">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="841df-597">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="841df-597">**Configure a filter factory**</span></span>

<span data-ttu-id="841df-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 :::no-loc(Razor)::: Pages。</span><span class="sxs-lookup"><span data-stu-id="841df-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="841df-599">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="841df-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="841df-600">*AddHeaderWithFactory.cs* ：</span><span class="sxs-lookup"><span data-stu-id="841df-600">*AddHeaderWithFactory.cs* :</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="841df-601">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="841df-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="841df-603">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="841df-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="841df-604">:::no-loc(Razor)::: Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 :::no-loc(Razor)::: Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="841df-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by :::no-loc(Razor)::: Pages, since :::no-loc(Razor)::: Pages use handler methods.</span></span> <span data-ttu-id="841df-605">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="841df-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="841df-606">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="841df-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="841df-607">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 :::no-loc(Razor)::: Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="841df-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="841df-608">有关详细信息，请参阅 [:::no-loc(Razor)::: Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="841df-608">For more information, see [Filter methods for :::no-loc(Razor)::: Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="841df-609">其他资源</span><span class="sxs-lookup"><span data-stu-id="841df-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
