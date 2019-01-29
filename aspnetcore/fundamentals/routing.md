---
title: ASP.NET Core 中的路由
author: rick-anderson
description: 了解 ASP.NET Core 路由如何负责将请求 URI 映射到终结点选择器并向终结点调度传入的请求。
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2019
uid: fundamentals/routing
ms.openlocfilehash: c5303ad418660fa31fe9094f0e61ee31f5d988f7
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54890011"
---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="9d867-103">ASP.NET Core 中的路由</span><span class="sxs-lookup"><span data-stu-id="9d867-103">Routing in ASP.NET Core</span></span>

<span data-ttu-id="9d867-104">撰写者：[Ryan Nowak](https://github.com/rynowak)、[Steve Smith](https://ardalis.com/) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9d867-104">By [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="9d867-105">对于本主题的 1.1 版本，请下载 [ASP.NET Core（版本 1.1，PDF）中的路由](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf)。</span><span class="sxs-lookup"><span data-stu-id="9d867-105">For the 1.1 version of this topic, download [Routing in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Routing_1.x.pdf).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9d867-106">路由负责将请求 URI 映射到终结点选择器并向终结点调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-106">Routing is responsible for mapping request URIs to endpoint selectors and dispatching incoming requests to endpoints.</span></span> <span data-ttu-id="9d867-107">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="9d867-107">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="9d867-108">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-108">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="9d867-109">通过使用应用中的路由信息，路由还能生成映射到终结点选择器的 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-109">Using route information from the app, routing is also able to generate URLs that map to endpoint selectors.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9d867-110">要在 ASP.NET Core 2.2 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：</span><span class="sxs-lookup"><span data-stu-id="9d867-110">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="9d867-111">`EnableEndpointRouting` 选项确定路由是应在内部使用 ASP.NET Core 2.1 或更早版本的基于终结点的逻辑还是使用其基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的逻辑。</span><span class="sxs-lookup"><span data-stu-id="9d867-111">The `EnableEndpointRouting` option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="9d867-112">兼容性版本设置为 2.2 或更高版本时，默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="9d867-112">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="9d867-113">将值设置为 `false` 以使用先前的路由逻辑：</span><span class="sxs-lookup"><span data-stu-id="9d867-113">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="9d867-114">有关基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由的详细信息，请参阅本主题的 [ASP.NET Core 2.1 版本](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)。</span><span class="sxs-lookup"><span data-stu-id="9d867-114">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-115">路由负责将请求 URI 映射到路由处理程序和调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-115">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="9d867-116">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="9d867-116">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="9d867-117">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-117">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="9d867-118">如果使用应用中配置的路由，路由能生成映射到路由处理程序的 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-118">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="9d867-119">要在 ASP.NET Core 2.1 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：</span><span class="sxs-lookup"><span data-stu-id="9d867-119">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="9d867-120">本文档介绍较低级别的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-120">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="9d867-121">有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="9d867-121">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="9d867-122">有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="9d867-122">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="9d867-123">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9d867-123">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="9d867-124">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="9d867-124">Routing basics</span></span>

<span data-ttu-id="9d867-125">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="9d867-125">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="9d867-126">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="9d867-126">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="9d867-127">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="9d867-127">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="9d867-128">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="9d867-128">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="9d867-129">开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-129">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="9d867-130">Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="9d867-130">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="9d867-131">也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-131">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="9d867-132">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="9d867-132">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="9d867-133">Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源。</span><span class="sxs-lookup"><span data-stu-id="9d867-133">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="9d867-134">还可以使用其他约定来自定义 Razor Pages 路由行为。</span><span class="sxs-lookup"><span data-stu-id="9d867-134">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="9d867-135">有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="9d867-135">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="9d867-136">借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。</span><span class="sxs-lookup"><span data-stu-id="9d867-136">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="9d867-137">此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-137">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9d867-138">路由使用“终结点”(`Endpoint`) 来表示应用中的逻辑终结点。</span><span class="sxs-lookup"><span data-stu-id="9d867-138">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="9d867-139">终结点定义用于处理请求的委托和任意元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="9d867-139">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="9d867-140">元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。</span><span class="sxs-lookup"><span data-stu-id="9d867-140">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="9d867-141">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="9d867-141">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="9d867-142">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-142">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="9d867-143">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="9d867-143">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="9d867-144">`IRouteConstraint` 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="9d867-144">`IRouteConstraint` is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="9d867-145">应用模型（如 MVC/Razor Pages）注册其所有终结点，这些终结点具有可预测的路由方案实现。</span><span class="sxs-lookup"><span data-stu-id="9d867-145">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="9d867-146">路由实现会在中间件管道中任何所需位置制定路由决策。</span><span class="sxs-lookup"><span data-stu-id="9d867-146">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="9d867-147">路由中间件之后出现的中间件可以检查路由中间件针对给定请求 URI 的终结点决策结果。</span><span class="sxs-lookup"><span data-stu-id="9d867-147">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="9d867-148">可以在中间件管道中的任何位置枚举应用中的所有终结点。</span><span class="sxs-lookup"><span data-stu-id="9d867-148">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="9d867-149">应用可根据终结点信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="9d867-149">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="9d867-150">URL 生成是基于支持任意可扩展性的地址：</span><span class="sxs-lookup"><span data-stu-id="9d867-150">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="9d867-151">可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在任意位置解析链接生成器 API (`LinkGenerator`) 以生成 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-151">The Link Generator API (`LinkGenerator`) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="9d867-152">如果无法通过 DI 获得链接生成器 API，则 `IUrlHelper` 会提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="9d867-152">Where the Link Generator API isn't available via DI, `IUrlHelper` offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="9d867-153">在 ASP.NET Core 2.2 中发布终结点路由后，终结点链接的适用范围限制为 MVC/Razor Pages 操作和页面。</span><span class="sxs-lookup"><span data-stu-id="9d867-153">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="9d867-154">将计划在未来发布的版本中扩展终结点链接的功能。</span><span class="sxs-lookup"><span data-stu-id="9d867-154">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-155">路由使用路由（<xref:Microsoft.AspNetCore.Routing.IRouter> 的实现）：</span><span class="sxs-lookup"><span data-stu-id="9d867-155">Routing uses *routes* (implementations of <xref:Microsoft.AspNetCore.Routing.IRouter>) to:</span></span>

* <span data-ttu-id="9d867-156">将传入请求映射到路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="9d867-156">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="9d867-157">生成响应中使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-157">Generate the URLs used in responses.</span></span>

<span data-ttu-id="9d867-158">默认情况下，应用只有一个路由集合。</span><span class="sxs-lookup"><span data-stu-id="9d867-158">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="9d867-159">当请求到达时，将按照路由在集合中的存在顺序来处理路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-159">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="9d867-160">框架试图通过在集合中的每个路由上调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法，将传入请求的 URL 与集合中的路由进行匹配。</span><span class="sxs-lookup"><span data-stu-id="9d867-160">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="9d867-161">响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="9d867-161">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="9d867-162">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="9d867-162">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="9d867-163">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-163">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="9d867-164">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="9d867-164">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="9d867-165">`IRouteConstraint` 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="9d867-165">`IRouteConstraint` is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="9d867-166">应用模型（如 MVC/Razor Pages）注册了其所有具有可预测的路由方案实现的路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-166">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="9d867-167">响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="9d867-167">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="9d867-168">URL 生成基于支持任意可扩展性的路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-168">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="9d867-169">`IUrlHelper` 提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="9d867-169">`IUrlHelper` offers methods to build URLs.</span></span>

::: moniker-end

<span data-ttu-id="9d867-170">路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。</span><span class="sxs-lookup"><span data-stu-id="9d867-170">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="9d867-171">[ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-171">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="9d867-172">要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。</span><span class="sxs-lookup"><span data-stu-id="9d867-172">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="9d867-173">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="9d867-173">URL matching</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9d867-174">URL 匹配是路由向终结点调度传入请求的过程。</span><span class="sxs-lookup"><span data-stu-id="9d867-174">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="9d867-175">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="9d867-175">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="9d867-176">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="9d867-176">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="9d867-177">终结点路由中的路由系统负责所有的调度决策。</span><span class="sxs-lookup"><span data-stu-id="9d867-177">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="9d867-178">由于中间件是基于所选终结点来应用策略，因此任何可能影响调度或安全策略应用的决策都应在路由系统内部制定，这一点很重要。</span><span class="sxs-lookup"><span data-stu-id="9d867-178">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="9d867-179">执行终结点委托时，将根据迄今执行的请求处理将 `RouteContext.RouteData` 的属性设置为适当的值。</span><span class="sxs-lookup"><span data-stu-id="9d867-179">When the endpoint delegate is executed, the properties of `RouteContext.RouteData` are set to appropriate values based on the request processing performed thus far.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-180">URL 匹配是一个过程，通过该过程，路由可向处理程序调度传入请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-180">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="9d867-181">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="9d867-181">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="9d867-182">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="9d867-182">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="9d867-183">传入请求将进入 `RouterMiddleware`，后者将对序列中的每个路由调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法。</span><span class="sxs-lookup"><span data-stu-id="9d867-183">Incoming requests enter the `RouterMiddleware`, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="9d867-184"><xref:Microsoft.AspNetCore.Routing.IRouter> 实例将选择是否通过将 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) 设置为非 NULL <xref:Microsoft.AspNetCore.Http.RequestDelegate> 来处理请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-184">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="9d867-185">如果路由为请求设置处理程序，将停止路由处理，并调用处理程序来处理该请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-185">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="9d867-186">如果未找到用于处理请求的路由处理程序，中间件会将请求传递给请求管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="9d867-186">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="9d867-187">`RouteAsync` 的主要输入是与当前请求关联的 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)。</span><span class="sxs-lookup"><span data-stu-id="9d867-187">The primary input to `RouteAsync` is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="9d867-188">`RouteContext.Handler` 和 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) 是路由匹配后设置的输出。</span><span class="sxs-lookup"><span data-stu-id="9d867-188">The `RouteContext.Handler` and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="9d867-189">调用 `RouteAsync` 的匹配还可根据迄今执行的请求处理将 `RouteContext.RouteData` 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="9d867-189">A match that calls `RouteAsync` also sets the properties of the `RouteContext.RouteData` to appropriate values based on the request processing performed thus far.</span></span>

::: moniker-end

<span data-ttu-id="9d867-190">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典。</span><span class="sxs-lookup"><span data-stu-id="9d867-190">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="9d867-191">这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。</span><span class="sxs-lookup"><span data-stu-id="9d867-191">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="9d867-192">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。</span><span class="sxs-lookup"><span data-stu-id="9d867-192">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="9d867-193">提供 `DataTokens` 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。</span><span class="sxs-lookup"><span data-stu-id="9d867-193">`DataTokens` are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="9d867-194">这些值是开发者定义的，不会影响通过任何方式路由的行为。</span><span class="sxs-lookup"><span data-stu-id="9d867-194">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="9d867-195">此外，存储于 `RouteData.DataTokens` 中的值可以属于任何类型，与 `RouteData.Values` 相反，后者必须能够转换为字符串，或从字符串进行转换。</span><span class="sxs-lookup"><span data-stu-id="9d867-195">Additionally, values stashed in `RouteData.DataTokens` can be of any type, in contrast to `RouteData.Values`, which must be convertible to and from strings.</span></span>

<span data-ttu-id="9d867-196">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers*) 是参与成功匹配请求的路由的列表。</span><span class="sxs-lookup"><span data-stu-id="9d867-196">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers*) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="9d867-197">路由可以相互嵌套。</span><span class="sxs-lookup"><span data-stu-id="9d867-197">Routes can be nested inside of one another.</span></span> <span data-ttu-id="9d867-198">`Routers` 属性可以通过导致匹配的逻辑路由树反映该路径。</span><span class="sxs-lookup"><span data-stu-id="9d867-198">The `Routers` property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="9d867-199">通常情况下，`Routers` 中的第一项是路由集合，应该用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-199">Generally, the first item in `Routers` is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="9d867-200">`Routers` 中的最后一项是匹配的路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="9d867-200">The last item in `Routers` is the route handler that matched.</span></span>

### <a name="url-generation"></a><span data-ttu-id="9d867-201">URL 生成</span><span class="sxs-lookup"><span data-stu-id="9d867-201">URL generation</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9d867-202">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="9d867-202">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="9d867-203">这需要考虑终结点与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="9d867-203">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="9d867-204">终结点路由包含链接生成器 API (`LinkGenerator`)。</span><span class="sxs-lookup"><span data-stu-id="9d867-204">Endpoint routing includes the Link Generator API (`LinkGenerator`).</span></span> <span data-ttu-id="9d867-205">`LinkGenerator` 是可从 DI 检索的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="9d867-205">`LinkGenerator` is a singleton service that can be retrieved from DI.</span></span> <span data-ttu-id="9d867-206">该 API 可在执行请求的上下文之外使用。</span><span class="sxs-lookup"><span data-stu-id="9d867-206">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="9d867-207">MVC 的 `IUrlHelper` 和依赖 `IUrlHelper` 的方案（如 [Tag Helpers](xref:mvc/views/tag-helpers/intro)、HTML Helpers 和 [Action Results](xref:mvc/controllers/actions)）使用链接生成器提供链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="9d867-207">MVC's `IUrlHelper` and scenarios that rely on `IUrlHelper`, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="9d867-208">链接生成器基于“地址”和“地址方案”概念。</span><span class="sxs-lookup"><span data-stu-id="9d867-208">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="9d867-209">地址方案是确定哪些终结点用于链接生成的方式。</span><span class="sxs-lookup"><span data-stu-id="9d867-209">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="9d867-210">例如，许多用户熟悉的来自 MVC/Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。</span><span class="sxs-lookup"><span data-stu-id="9d867-210">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="9d867-211">链接生成器可以通过以下扩展方法链接到 MVC/Razor Pages 操作和页面：</span><span class="sxs-lookup"><span data-stu-id="9d867-211">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* `GetPathByAction`
* `GetUriByAction`
* `GetPathByPage`
* `GetUriByPage`

<span data-ttu-id="9d867-212">这些方法的重载接受包含 `HttpContext` 的参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-212">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="9d867-213">这些方法在功能上等同于 `Url.Action` 和 `Url.Page`，但提供了更大的灵活性和更多的选项。</span><span class="sxs-lookup"><span data-stu-id="9d867-213">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="9d867-214">`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="9d867-214">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="9d867-215">`GetUri*` 方法始终生成包含方案和主机的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="9d867-215">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="9d867-216">接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。</span><span class="sxs-lookup"><span data-stu-id="9d867-216">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="9d867-217">除非重写，否则将使用来自执行请求的环境路由值、URL 基本路径、方案和主机。</span><span class="sxs-lookup"><span data-stu-id="9d867-217">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="9d867-218">使用地址调用 `LinkGenerator`。</span><span class="sxs-lookup"><span data-stu-id="9d867-218">`LinkGenerator` is called with an address.</span></span> <span data-ttu-id="9d867-219">生成 URI 的过程分两步进行：</span><span class="sxs-lookup"><span data-stu-id="9d867-219">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="9d867-220">将地址绑定到与地址匹配的终结点列表。</span><span class="sxs-lookup"><span data-stu-id="9d867-220">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="9d867-221">计算每个终结点的 `RoutePattern`，直到找到与提供的值匹配的路由模式。</span><span class="sxs-lookup"><span data-stu-id="9d867-221">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="9d867-222">输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。</span><span class="sxs-lookup"><span data-stu-id="9d867-222">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="9d867-223">对任何类型的地址，`LinkGenerator` 提供的方法均支持标准链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="9d867-223">The methods provided by `LinkGenerator` support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="9d867-224">使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作。</span><span class="sxs-lookup"><span data-stu-id="9d867-224">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="9d867-225">扩展方法</span><span class="sxs-lookup"><span data-stu-id="9d867-225">Extension Method</span></span>   | <span data-ttu-id="9d867-226">说明</span><span class="sxs-lookup"><span data-stu-id="9d867-226">Description</span></span>                                                         |
| ------------------ | ------------------------------------------------------------------- |
| `GetPathByAddress` | <span data-ttu-id="9d867-227">根据提供的值生成具有绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="9d867-227">Generates a URI with an absolute path based on the provided values.</span></span> |
| `GetUriByAddress`  | <span data-ttu-id="9d867-228">根据提供的值生成绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="9d867-228">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="9d867-229">请注意有关调用 `LinkGenerator` 方法的下列含义：</span><span class="sxs-lookup"><span data-stu-id="9d867-229">Pay attention to the following implications of calling `LinkGenerator` methods:</span></span>
>
> * <span data-ttu-id="9d867-230">对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9d867-230">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="9d867-231">如果未验证传入请求的 `Host`标头，则可能以视图/页面中 URI 的形式将不受信任的请求输入发送回客户端。</span><span class="sxs-lookup"><span data-stu-id="9d867-231">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="9d867-232">建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="9d867-232">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="9d867-233">在中间件中将 `LinkGenerator` 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="9d867-233">Use `LinkGenerator` with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="9d867-234">`Map*` 会更改执行请求的基路径，这会影响链接生成的输出。</span><span class="sxs-lookup"><span data-stu-id="9d867-234">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="9d867-235">所有 `LinkGenerator` API 都允许指定基路径。</span><span class="sxs-lookup"><span data-stu-id="9d867-235">All of the `LinkGenerator` APIs allow specifying a base path.</span></span> <span data-ttu-id="9d867-236">始终指定一个空的基路径来撤消 `Map*` 对链接生成的影响。</span><span class="sxs-lookup"><span data-stu-id="9d867-236">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="9d867-237">与早期版本路由的差异</span><span class="sxs-lookup"><span data-stu-id="9d867-237">Differences from earlier versions of routing</span></span>

<span data-ttu-id="9d867-238">ASP.NET Core 2.2 或更高版本中的终结点路由与 ASP.NET Core 中早期版本的路由之间存在一些差异：</span><span class="sxs-lookup"><span data-stu-id="9d867-238">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="9d867-239">终结点路由系统不支持基于 `IRouter` 的可扩展性，包括从 `Route` 继承。</span><span class="sxs-lookup"><span data-stu-id="9d867-239">The endpoint routing system doesn't support `IRouter`-based extensibility, including inheriting from `Route`.</span></span>

* <span data-ttu-id="9d867-240">终结点路由不支持 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。</span><span class="sxs-lookup"><span data-stu-id="9d867-240">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="9d867-241">使用 2.1 [兼容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 以继续使用兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="9d867-241">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="9d867-242">在使用传统路由时，终结点路由对生成的 URI 的大小写具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="9d867-242">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="9d867-243">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="9d867-243">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="9d867-244">假设使用以下路由生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="9d867-244">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="9d867-245">如果使用基于 `IRouter` 的路由，此代码生成 URI `/blog/ReadPost/17`，该 URI 遵循所提供路由值的大小写。</span><span class="sxs-lookup"><span data-stu-id="9d867-245">With `IRouter`-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="9d867-246">ASP.NET Core 2.2 或更高版本中的终结点路由生成 `/Blog/ReadPost/17`（“Blog”大写）。</span><span class="sxs-lookup"><span data-stu-id="9d867-246">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="9d867-247">终结点路由提供 `IOutboundParameterTransformer` 接口，可用于在全局范围自定义此行为或应用不同的约定来映射 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-247">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="9d867-248">有关详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="9d867-248">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="9d867-249">在试图链接到不存在的控制器/操作或页面时，MVC/Razor Pages 通过传统路由执行的链接生成，其操作具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="9d867-249">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="9d867-250">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="9d867-250">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="9d867-251">假设使用以下路由通过默认模板生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="9d867-251">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="9d867-252">如果使用基于 `IRouter` 的路由，即使 `BlogController` 不存在或没有 `ReadPost` 操作方法，结果也始终为 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="9d867-252">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="9d867-253">正如所料，如果操作方法存在，ASP.NET Core 2.2 或更高版本中的终结点路由会生成 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="9d867-253">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="9d867-254">但是，如果操作不存在，终结点路由会生成空字符串。</span><span class="sxs-lookup"><span data-stu-id="9d867-254">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="9d867-255">从概念上讲，如果操作不存在，终结点路由不会假定终结点存在。</span><span class="sxs-lookup"><span data-stu-id="9d867-255">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="9d867-256">与终结点路由一起使用时，链接生成环境值失效算法的行为会有所不同。</span><span class="sxs-lookup"><span data-stu-id="9d867-256">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="9d867-257">*环境值失效*是一种算法，用于决定当前正在执行的请求（环境值）中的哪些路由值可用于链接生成操作。</span><span class="sxs-lookup"><span data-stu-id="9d867-257">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="9d867-258">链接到不同操作时，传统路由会使额外的路由值失效。</span><span class="sxs-lookup"><span data-stu-id="9d867-258">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="9d867-259">ASP.NET Core 2.2 之前的版本中，属性路由不具有此行为。</span><span class="sxs-lookup"><span data-stu-id="9d867-259">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="9d867-260">在 ASP.NET Core 的早期版本中，如果有另一个操作使用同一路由参数名称，则该操作的链接会导致发生链接生成错误。</span><span class="sxs-lookup"><span data-stu-id="9d867-260">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="9d867-261">在 ASP.NET Core 2.2 或更高版本中，链接到另一个操作时，这两种路由形式都会使值失效。</span><span class="sxs-lookup"><span data-stu-id="9d867-261">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="9d867-262">请考虑 ASP.NET Core2.1 或更高版本中的以下示例。</span><span class="sxs-lookup"><span data-stu-id="9d867-262">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="9d867-263">链接到另一个操作（或另一页面）时，路由值可能会按非预期的方式被重用。</span><span class="sxs-lookup"><span data-stu-id="9d867-263">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="9d867-264">在 /Pages/Store/Product.cshtml 中：</span><span class="sxs-lookup"><span data-stu-id="9d867-264">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="9d867-265">在 /Pages/Login.cshtml 中：</span><span class="sxs-lookup"><span data-stu-id="9d867-265">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="9d867-266">如果 ASP.NET Core 2.1 或更早版本中的 URI 为 `/Store/Product/18`，则由 `@Url.Page("/Login")` 在 Store/Info 页面上生成的链接为 `/Login/18`。</span><span class="sxs-lookup"><span data-stu-id="9d867-266">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="9d867-267">即使链接目标完全是应用的其他部分，仍会重用 `id` 值 18。</span><span class="sxs-lookup"><span data-stu-id="9d867-267">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="9d867-268">`/Login` 页面上下文中的 `id` 路由值可能是用户 ID 值，而非存储产品 ID 值。</span><span class="sxs-lookup"><span data-stu-id="9d867-268">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="9d867-269">在 ASP.NET Core 2.2 或更高版本的终结点路由中，结果为 `/Login`。</span><span class="sxs-lookup"><span data-stu-id="9d867-269">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="9d867-270">当链接的目标是另一个操作或页面时，不会重复使用环境值。</span><span class="sxs-lookup"><span data-stu-id="9d867-270">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="9d867-271">往返路由参数语法：使用双星号 (`**`) catch-all 参数语法时，不对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="9d867-271">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="9d867-272">在链接生成期间，路由系统对双星号 (`**`) catch-all 参数（例如，`{**myparametername}`）中捕获的除正斜杠外的值进行编码。</span><span class="sxs-lookup"><span data-stu-id="9d867-272">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="9d867-273">在 ASP.NET Core 2.2 或更高版本中，基于 `IRouter` 的路由支持双星号 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-273">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="9d867-274">ASP.NET Core (`{*myparametername}`) 早期版本中的单星号 catch-all 参数语法仍然受支持，并对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="9d867-274">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="9d867-275">路由</span><span class="sxs-lookup"><span data-stu-id="9d867-275">Route</span></span>              | <span data-ttu-id="9d867-276">链接生成方式为</span><span class="sxs-lookup"><span data-stu-id="9d867-276">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | <span data-ttu-id="9d867-277">`/search/admin%2Fproducts`（对正斜杠进行编码）</span><span class="sxs-lookup"><span data-stu-id="9d867-277">`/search/admin%2Fproducts` (the forward slash is encoded)</span></span>             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a><span data-ttu-id="9d867-278">中间件示例</span><span class="sxs-lookup"><span data-stu-id="9d867-278">Middleware example</span></span>

<span data-ttu-id="9d867-279">在以下示例中，中间件使用 `LinkGenerator` API 创建列出存储产品的操作方法的链接。</span><span class="sxs-lookup"><span data-stu-id="9d867-279">In the following example, a middleware uses the `LinkGenerator` API to create link to an action method that lists store products.</span></span> <span data-ttu-id="9d867-280">应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器。</span><span class="sxs-lookup"><span data-stu-id="9d867-280">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-281">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="9d867-281">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="9d867-282">这需要考虑处理程序与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="9d867-282">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="9d867-283">URL 遵循类似的迭代过程，但开头是将用户或框架代码调用到路由集合的 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法中。</span><span class="sxs-lookup"><span data-stu-id="9d867-283">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="9d867-284">每个路由按顺序调用其 `GetVirtualPath` 方法，直到返回非 NULL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。</span><span class="sxs-lookup"><span data-stu-id="9d867-284">Each *route* has its `GetVirtualPath` method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="9d867-285">`GetVirtualPath` 的主输入有：</span><span class="sxs-lookup"><span data-stu-id="9d867-285">The primary inputs to `GetVirtualPath` are:</span></span>

* [<span data-ttu-id="9d867-286">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="9d867-286">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext*)
* [<span data-ttu-id="9d867-287">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="9d867-287">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*)
* [<span data-ttu-id="9d867-288">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="9d867-288">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*)

<span data-ttu-id="9d867-289">路由主要使用 `Values` 和 `AmbientValues` 提供的路由值来确定是否可能生成 URL 以及要包括哪些值。</span><span class="sxs-lookup"><span data-stu-id="9d867-289">Routes primarily use the route values provided by `Values` and `AmbientValues` to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="9d867-290">`AmbientValues` 是通过匹配当前请求而生成的路由值集。</span><span class="sxs-lookup"><span data-stu-id="9d867-290">The `AmbientValues` are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="9d867-291">与此相反，`Values` 是指定如何为当前操作生成所需 URL 的路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-291">In contrast, `Values` are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="9d867-292">如果路由应获取与当前上下文关联的服务或其他数据时，则提供 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="9d867-292">The `HttpContext` is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="9d867-293">将 [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) 视为 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) 的一组替代。</span><span class="sxs-lookup"><span data-stu-id="9d867-293">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="9d867-294">URL 生成尝试重复使用当前请求中的路由值，以便使用相同路由或路由值生成链接的 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-294">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="9d867-295">`GetVirtualPath` 的输出是 `VirtualPathData`。</span><span class="sxs-lookup"><span data-stu-id="9d867-295">The output of `GetVirtualPath` is a `VirtualPathData`.</span></span> <span data-ttu-id="9d867-296">`VirtualPathData` 是 `RouteData` 的并行值。</span><span class="sxs-lookup"><span data-stu-id="9d867-296">`VirtualPathData` is a parallel of `RouteData`.</span></span> <span data-ttu-id="9d867-297">`VirtualPathData` 包含输出 URL 的 `VirtualPath` 以及路由应该设置的某些其他属性。</span><span class="sxs-lookup"><span data-stu-id="9d867-297">`VirtualPathData` contains the `VirtualPath` for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="9d867-298">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 属性包含路由生成的虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="9d867-298">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="9d867-299">你可能需要进一步处理路径，具体取决于你的需求。</span><span class="sxs-lookup"><span data-stu-id="9d867-299">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="9d867-300">如果要在 HTML 中呈现生成的 URL，请预置应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="9d867-300">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="9d867-301">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) 是对已成功生成 URL 的路由的引用。</span><span class="sxs-lookup"><span data-stu-id="9d867-301">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="9d867-302">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 属性是生成 URL 的路由的其他相关数据的字典。</span><span class="sxs-lookup"><span data-stu-id="9d867-302">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="9d867-303">这是 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 的并性值。</span><span class="sxs-lookup"><span data-stu-id="9d867-303">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

::: moniker-end

### <a name="create-routes"></a><span data-ttu-id="9d867-304">创建路由</span><span class="sxs-lookup"><span data-stu-id="9d867-304">Create routes</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-305">路由提供 <xref:Microsoft.AspNetCore.Routing.Route> 类，作为 <xref:Microsoft.AspNetCore.Routing.IRouter> 的标准实现。</span><span class="sxs-lookup"><span data-stu-id="9d867-305">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="9d867-306">`Route` 使用 route template 语法来定义模式，以便在调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 时匹配 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="9d867-306">`Route` uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="9d867-307">调用 `GetVirtualPath` 时，`Route` 使用同一路由模板生成 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-307">`Route` uses the same route template to generate a URL when `GetVirtualPath` is called.</span></span>

::: moniker-end

<span data-ttu-id="9d867-308">大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-308">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="9d867-309">任何 `IRouteBuilder` 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。</span><span class="sxs-lookup"><span data-stu-id="9d867-309">Any of the `IRouteBuilder` extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9d867-310">`MapRoute` 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-310">`MapRoute` doesn't accept a route handler parameter.</span></span> <span data-ttu-id="9d867-311">`MapRoute` 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-311">`MapRoute` only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="9d867-312">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="9d867-312">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-313">`MapRoute` 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-313">`MapRoute` doesn't accept a route handler parameter.</span></span> <span data-ttu-id="9d867-314">`MapRoute` 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-314">`MapRoute` only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="9d867-315">默认处理程序是 `IRouter`，该处理程序可能无法处理该请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-315">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="9d867-316">例如，ASP.NET Core MVC 通常被配置为默认处理程序，仅处理与可用控制器和操作匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-316">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="9d867-317">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="9d867-317">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

::: moniker-end

<span data-ttu-id="9d867-318">以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 `MapRoute` 调用示例：</span><span class="sxs-lookup"><span data-stu-id="9d867-318">The following code example is an example of a `MapRoute` call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9d867-319">此模板与 URL 路径相匹配，并且提取路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-319">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="9d867-320">例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="9d867-320">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="9d867-321">路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的。</span><span class="sxs-lookup"><span data-stu-id="9d867-321">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="9d867-322">路由参数已命名。</span><span class="sxs-lookup"><span data-stu-id="9d867-322">Route parameters are named.</span></span> <span data-ttu-id="9d867-323">参数通过将参数名称括在大括号 `{ ... }` 中来定义。</span><span class="sxs-lookup"><span data-stu-id="9d867-323">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="9d867-324">上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="9d867-324">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="9d867-325">这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="9d867-325">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="9d867-326">路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。</span><span class="sxs-lookup"><span data-stu-id="9d867-326">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="9d867-327">路由参数名称后面的问号 (`?`) 定义了可选参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-327">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="9d867-328">路由匹配时，具有默认值的路由参数始终会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-328">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="9d867-329">如果没有相应的 URL 路径段，则可选参数不会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-329">Optional parameters don't produce a route value if there was no corresponding URL path segment.</span></span> <span data-ttu-id="9d867-330">有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="9d867-330">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="9d867-331">在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="9d867-331">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="9d867-332">此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。</span><span class="sxs-lookup"><span data-stu-id="9d867-332">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="9d867-333">路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。</span><span class="sxs-lookup"><span data-stu-id="9d867-333">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="9d867-334">在此示例中，路由值 `id` 必须可转换为整数。</span><span class="sxs-lookup"><span data-stu-id="9d867-334">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="9d867-335">有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="9d867-335">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="9d867-336">其他 `MapRoute` 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="9d867-336">Additional overloads of `MapRoute` accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="9d867-337">这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="9d867-337">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="9d867-338">以下 `MapRoute` 示例可创建等效路由：</span><span class="sxs-lookup"><span data-stu-id="9d867-338">The following `MapRoute` examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="9d867-339">定义约束和默认值的内联语法对于简单路由很方便。</span><span class="sxs-lookup"><span data-stu-id="9d867-339">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="9d867-340">但是，存在内联语法不支持的方案，例如数据令牌。</span><span class="sxs-lookup"><span data-stu-id="9d867-340">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="9d867-341">以下示例演示了一些其他方案：</span><span class="sxs-lookup"><span data-stu-id="9d867-341">The following example demonstrates a few additional scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="9d867-342">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="9d867-342">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="9d867-343">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-343">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="9d867-344">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="9d867-344">Default values can be specified in the route template.</span></span> <span data-ttu-id="9d867-345">根据路由参数名称前的双星号 (`**`) 外观，`article` 路由参数被定义为 catch-all。</span><span class="sxs-lookup"><span data-stu-id="9d867-345">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="9d867-346">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="9d867-346">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="9d867-347">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="9d867-347">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="9d867-348">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-348">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="9d867-349">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="9d867-349">Default values can be specified in the route template.</span></span> <span data-ttu-id="9d867-350">根据路由参数名称前的星号 (`*`) 外观，`article` 路由参数被定义为 catch-all。</span><span class="sxs-lookup"><span data-stu-id="9d867-350">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="9d867-351">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="9d867-351">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

::: moniker-end

<span data-ttu-id="9d867-352">以下示例添加了路由约束和数据令牌：</span><span class="sxs-lookup"><span data-stu-id="9d867-352">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="9d867-353">上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="9d867-353">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="9d867-355">路由类 URL 生成</span><span class="sxs-lookup"><span data-stu-id="9d867-355">Route class URL generation</span></span>

<span data-ttu-id="9d867-356">`Route` 类还可通过将一组路由值与其路由模板组合来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-356">The `Route` class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="9d867-357">从逻辑上来说，这是匹配 URL 路径的反向过程。</span><span class="sxs-lookup"><span data-stu-id="9d867-357">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="9d867-358">为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-358">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="9d867-359">将生成什么值？</span><span class="sxs-lookup"><span data-stu-id="9d867-359">What values would be produced?</span></span> <span data-ttu-id="9d867-360">这大致相当于 URL 在 `Route` 类中的生成方式。</span><span class="sxs-lookup"><span data-stu-id="9d867-360">This is the rough equivalent of how URL generation works in the `Route` class.</span></span>

<span data-ttu-id="9d867-361">以下示例使用常规 ASP.NET Core MVC 默认路由：</span><span class="sxs-lookup"><span data-stu-id="9d867-361">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9d867-362">使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="9d867-362">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="9d867-363">路由值将替换为相应的路由参数，以形成 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="9d867-363">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="9d867-364">由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。</span><span class="sxs-lookup"><span data-stu-id="9d867-364">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="9d867-365">使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="9d867-365">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="9d867-366">提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。</span><span class="sxs-lookup"><span data-stu-id="9d867-366">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="9d867-367">已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-367">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="9d867-368">使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-368">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="9d867-369">有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="9d867-369">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="9d867-370">使用路由中间件</span><span class="sxs-lookup"><span data-stu-id="9d867-370">Use Routing Middleware</span></span>

<span data-ttu-id="9d867-371">引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="9d867-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="9d867-372">将路由添加到 `Startup.ConfigureServices` 中的服务容器：</span><span class="sxs-lookup"><span data-stu-id="9d867-372">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="9d867-373">必须在 `Startup.Configure` 方法中配置路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-373">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="9d867-374">该示例应用使用以下 API：</span><span class="sxs-lookup"><span data-stu-id="9d867-374">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="9d867-375"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; 仅匹配 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-375"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="9d867-376">下表显示了具有给定 URI 的响应。</span><span class="sxs-lookup"><span data-stu-id="9d867-376">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="9d867-377">URI</span><span class="sxs-lookup"><span data-stu-id="9d867-377">URI</span></span>                    | <span data-ttu-id="9d867-378">响应</span><span class="sxs-lookup"><span data-stu-id="9d867-378">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="9d867-379">Hello!</span><span class="sxs-lookup"><span data-stu-id="9d867-379">Hello!</span></span> <span data-ttu-id="9d867-380">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="9d867-380">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="9d867-381">Hello!</span><span class="sxs-lookup"><span data-stu-id="9d867-381">Hello!</span></span> <span data-ttu-id="9d867-382">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="9d867-382">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="9d867-383">Hello!</span><span class="sxs-lookup"><span data-stu-id="9d867-383">Hello!</span></span> <span data-ttu-id="9d867-384">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="9d867-384">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="9d867-385">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="9d867-385">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="9d867-386">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="9d867-386">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="9d867-387">请求失败，仅匹配 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="9d867-387">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="9d867-388">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="9d867-388">The request falls through, no match.</span></span>              |

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-389">如果要配置单个路由，请在 `IRouter` 实例中调用 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> 传入。</span><span class="sxs-lookup"><span data-stu-id="9d867-389">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="9d867-390">无需使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder>。</span><span class="sxs-lookup"><span data-stu-id="9d867-390">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

::: moniker-end

<span data-ttu-id="9d867-391">框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9d867-391">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* `MapDelete`
* `MapGet`
* `MapMiddlewareDelete`
* `MapMiddlewareGet`
* `MapMiddlewarePost`
* `MapMiddlewarePut`
* `MapMiddlewareRoute`
* `MapMiddlewareVerb`
* `MapPost`
* `MapPut`
* `MapRoute`
* `MapVerb`

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-392">一些列出的方法（如 `MapGet`）需要 `RequestDelegate`。</span><span class="sxs-lookup"><span data-stu-id="9d867-392">Some of listed methods, such as `MapGet`, require a `RequestDelegate`.</span></span> <span data-ttu-id="9d867-393">路由匹配时，`RequestDelegate` 会用作路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="9d867-393">The `RequestDelegate` is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="9d867-394">此系列中的其他方法允许配置中间件管道，将其用作路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="9d867-394">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="9d867-395">如果 `Map*` 方法不接受处理程序（例如 `MapRoute`），则它将使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>。</span><span class="sxs-lookup"><span data-stu-id="9d867-395">If the `Map*` method doesn't accept a handler, such as `MapRoute`, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

::: moniker-end

<span data-ttu-id="9d867-396">`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="9d867-396">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="9d867-397">有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="9d867-397">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="9d867-398">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="9d867-398">Route template reference</span></span>

<span data-ttu-id="9d867-399">如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-399">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="9d867-400">可在路由段中定义多个路由参数，但必须由文本值隔开。</span><span class="sxs-lookup"><span data-stu-id="9d867-400">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="9d867-401">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="9d867-401">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="9d867-402">这些路由参数必须具有名称，且可能指定了其他属性。</span><span class="sxs-lookup"><span data-stu-id="9d867-402">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="9d867-403">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="9d867-403">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="9d867-404">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="9d867-404">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="9d867-405">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="9d867-405">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="9d867-406">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="9d867-406">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="9d867-407">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="9d867-407">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="9d867-408">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="9d867-408">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="9d867-409">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。</span><span class="sxs-lookup"><span data-stu-id="9d867-409">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="9d867-410">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="9d867-410">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9d867-411">可以使用星号 (`*`) 或双星号 (`**`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="9d867-411">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="9d867-412">这些称为 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-412">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="9d867-413">例如，`blog/{**slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="9d867-413">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="9d867-414">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="9d867-414">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="9d867-415">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="9d867-415">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="9d867-416">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="9d867-416">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="9d867-417">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="9d867-417">Note the escaped forward slash.</span></span> <span data-ttu-id="9d867-418">要往返路径分隔符，请使用 `**` 路由参数前缀。</span><span class="sxs-lookup"><span data-stu-id="9d867-418">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="9d867-419">`{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="9d867-419">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9d867-420">可以使用星号 (`*`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="9d867-420">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="9d867-421">这称为 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-421">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="9d867-422">例如，`blog/{*slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="9d867-422">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="9d867-423">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="9d867-423">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="9d867-424">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="9d867-424">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="9d867-425">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="9d867-425">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="9d867-426">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="9d867-426">Note the escaped forward slash.</span></span>

::: moniker-end

<span data-ttu-id="9d867-427">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值。</span><span class="sxs-lookup"><span data-stu-id="9d867-427">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="9d867-428">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="9d867-428">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="9d867-429">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="9d867-429">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="9d867-430">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="9d867-430">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="9d867-431">可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。</span><span class="sxs-lookup"><span data-stu-id="9d867-431">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="9d867-432">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="9d867-432">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="9d867-433">在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束。</span><span class="sxs-lookup"><span data-stu-id="9d867-433">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="9d867-434">如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="9d867-434">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="9d867-435">通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="9d867-435">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="9d867-436">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="9d867-436">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="9d867-437">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="9d867-437">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="9d867-438">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="9d867-438">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9d867-439">路由参数还可以具有参数转换器，用于在生成链接以及将操作和页面匹配到 URI 时转换参数的值。</span><span class="sxs-lookup"><span data-stu-id="9d867-439">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="9d867-440">与约束类似，可以通过在路由参数名称后面添加冒号 (`:`) 和转换器名称，将参数变换器内联添加到路径参数。</span><span class="sxs-lookup"><span data-stu-id="9d867-440">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="9d867-441">例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。</span><span class="sxs-lookup"><span data-stu-id="9d867-441">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="9d867-442">有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="9d867-442">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

::: moniker-end

<span data-ttu-id="9d867-443">下表演示了示例路由模板及其行为。</span><span class="sxs-lookup"><span data-stu-id="9d867-443">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="9d867-444">路由模板</span><span class="sxs-lookup"><span data-stu-id="9d867-444">Route Template</span></span>                           | <span data-ttu-id="9d867-445">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="9d867-445">Example Matching URI</span></span>    | <span data-ttu-id="9d867-446">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="9d867-446">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="9d867-447">仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="9d867-447">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="9d867-448">匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="9d867-448">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="9d867-449">匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="9d867-449">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="9d867-450">映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="9d867-450">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="9d867-451">映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。</span><span class="sxs-lookup"><span data-stu-id="9d867-451">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| <span data-ttu-id="9d867-452">`{controller=Home}/{action=Index}/{id?`}</span><span class="sxs-lookup"><span data-stu-id="9d867-452">`{controller=Home}/{action=Index}/{id?`}</span></span> | `/`                     | <span data-ttu-id="9d867-453">映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。</span><span class="sxs-lookup"><span data-stu-id="9d867-453">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="9d867-454">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="9d867-454">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="9d867-455">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="9d867-455">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="9d867-456">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 `Route`）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-456">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as `Route`, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="9d867-457">保留的路由名称</span><span class="sxs-lookup"><span data-stu-id="9d867-457">Reserved routing names</span></span>

<span data-ttu-id="9d867-458">以下关键字是保留的名称，它们不能用作路由名称或参数：</span><span class="sxs-lookup"><span data-stu-id="9d867-458">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="9d867-459">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="9d867-459">Route constraint reference</span></span>

<span data-ttu-id="9d867-460">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-460">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="9d867-461">路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。</span><span class="sxs-lookup"><span data-stu-id="9d867-461">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="9d867-462">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-462">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="9d867-463">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="9d867-463">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="9d867-464">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="9d867-464">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="9d867-465">请勿将约束用于“输入验证”。</span><span class="sxs-lookup"><span data-stu-id="9d867-465">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="9d867-466">如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求”。</span><span class="sxs-lookup"><span data-stu-id="9d867-466">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="9d867-467">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入。</span><span class="sxs-lookup"><span data-stu-id="9d867-467">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="9d867-468">下表演示示例路由约束及其预期行为。</span><span class="sxs-lookup"><span data-stu-id="9d867-468">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="9d867-469">约束</span><span class="sxs-lookup"><span data-stu-id="9d867-469">constraint</span></span> | <span data-ttu-id="9d867-470">示例</span><span class="sxs-lookup"><span data-stu-id="9d867-470">Example</span></span> | <span data-ttu-id="9d867-471">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="9d867-471">Example Matches</span></span> | <span data-ttu-id="9d867-472">说明</span><span class="sxs-lookup"><span data-stu-id="9d867-472">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="9d867-473">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9d867-473">`123456789`, `-123456789`</span></span> | <span data-ttu-id="9d867-474">匹配任何整数</span><span class="sxs-lookup"><span data-stu-id="9d867-474">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="9d867-475">`true`， `FALSE`</span><span class="sxs-lookup"><span data-stu-id="9d867-475">`true`, `FALSE`</span></span> | <span data-ttu-id="9d867-476">匹配 `true`或 `false`（区分大小写）</span><span class="sxs-lookup"><span data-stu-id="9d867-476">Matches `true` or `false` (case-insensitive)</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="9d867-477">`2016-12-31`， `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="9d867-477">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="9d867-478">匹配有效的 `DateTime` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="9d867-478">Matches a valid `DateTime` value (in the invariant culture - see warning)</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="9d867-479">`49.99`， `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="9d867-479">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="9d867-480">匹配有效的 `decimal` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="9d867-480">Matches a valid `decimal` value (in the invariant culture - see warning)</span></span> |
| `double` | `{weight:double}` | <span data-ttu-id="9d867-481">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9d867-481">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="9d867-482">匹配有效的 `double` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="9d867-482">Matches a valid `double` value (in the invariant culture - see warning)</span></span> |
| `float` | `{weight:float}` | <span data-ttu-id="9d867-483">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9d867-483">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="9d867-484">匹配有效的 `float` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="9d867-484">Matches a valid `float` value (in the invariant culture - see warning)</span></span> |
| `guid` | `{id:guid}` | <span data-ttu-id="9d867-485">`CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="9d867-485">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="9d867-486">匹配有效的 `Guid` 值</span><span class="sxs-lookup"><span data-stu-id="9d867-486">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="9d867-487">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="9d867-487">`123456789`, `-123456789`</span></span> | <span data-ttu-id="9d867-488">匹配有效的 `long` 值</span><span class="sxs-lookup"><span data-stu-id="9d867-488">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="9d867-489">字符串必须至少为 4 个字符</span><span class="sxs-lookup"><span data-stu-id="9d867-489">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="9d867-490">字符串不得超过 8 个字符</span><span class="sxs-lookup"><span data-stu-id="9d867-490">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="9d867-491">字符串必须正好为 12 个字符</span><span class="sxs-lookup"><span data-stu-id="9d867-491">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="9d867-492">字符串必须至少为 8 个字符，且不得超过 16 个字符</span><span class="sxs-lookup"><span data-stu-id="9d867-492">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="9d867-493">整数值必须至少为 18</span><span class="sxs-lookup"><span data-stu-id="9d867-493">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="9d867-494">整数值不得超过 120</span><span class="sxs-lookup"><span data-stu-id="9d867-494">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="9d867-495">整数值必须至少为 18，且不得超过 120</span><span class="sxs-lookup"><span data-stu-id="9d867-495">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="9d867-496">字符串必须由一个或多个字母字符（`a`-`z`，区分大小写）组成</span><span class="sxs-lookup"><span data-stu-id="9d867-496">String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive)</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="9d867-497">字符串必须匹配正则表达式（参见有关定义正则表达式的提示）</span><span class="sxs-lookup"><span data-stu-id="9d867-497">String must match the regular expression (see tips about defining a regular expression)</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="9d867-498">用于强制在 URL 生成过程中存在非参数值</span><span class="sxs-lookup"><span data-stu-id="9d867-498">Used to enforce that a non-parameter value is present during URL generation</span></span> |

<span data-ttu-id="9d867-499">可向单个参数应用多个由冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="9d867-499">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="9d867-500">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="9d867-500">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="9d867-501">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="9d867-501">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="9d867-502">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="9d867-502">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="9d867-503">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="9d867-503">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="9d867-504">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="9d867-504">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="9d867-505">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="9d867-505">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="9d867-506">正则表达式</span><span class="sxs-lookup"><span data-stu-id="9d867-506">Regular expressions</span></span>

<span data-ttu-id="9d867-507">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="9d867-507">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="9d867-508">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="9d867-508">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="9d867-509">正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="9d867-509">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="9d867-510">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="9d867-510">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="9d867-511">要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。</span><span class="sxs-lookup"><span data-stu-id="9d867-511">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="9d867-512">要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="9d867-512">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="9d867-513">下表展示了正则表达式和转义版本。</span><span class="sxs-lookup"><span data-stu-id="9d867-513">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="9d867-514">正则表达式</span><span class="sxs-lookup"><span data-stu-id="9d867-514">Regular Expression</span></span>    | <span data-ttu-id="9d867-515">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="9d867-515">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="9d867-516">路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="9d867-516">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="9d867-517">表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="9d867-517">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="9d867-518">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="9d867-518">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="9d867-519">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="9d867-519">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="9d867-520">下表提供了示例并说明了它们匹配或匹配失败的原因。</span><span class="sxs-lookup"><span data-stu-id="9d867-520">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="9d867-521">表达式</span><span class="sxs-lookup"><span data-stu-id="9d867-521">Expression</span></span>   | <span data-ttu-id="9d867-522">String</span><span class="sxs-lookup"><span data-stu-id="9d867-522">String</span></span>    | <span data-ttu-id="9d867-523">匹配</span><span class="sxs-lookup"><span data-stu-id="9d867-523">Match</span></span> | <span data-ttu-id="9d867-524">注释</span><span class="sxs-lookup"><span data-stu-id="9d867-524">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="9d867-525">hello</span><span class="sxs-lookup"><span data-stu-id="9d867-525">hello</span></span>     | <span data-ttu-id="9d867-526">是</span><span class="sxs-lookup"><span data-stu-id="9d867-526">Yes</span></span>   | <span data-ttu-id="9d867-527">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="9d867-527">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="9d867-528">123abc456</span><span class="sxs-lookup"><span data-stu-id="9d867-528">123abc456</span></span> | <span data-ttu-id="9d867-529">是</span><span class="sxs-lookup"><span data-stu-id="9d867-529">Yes</span></span>   | <span data-ttu-id="9d867-530">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="9d867-530">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="9d867-531">mz</span><span class="sxs-lookup"><span data-stu-id="9d867-531">mz</span></span>        | <span data-ttu-id="9d867-532">是</span><span class="sxs-lookup"><span data-stu-id="9d867-532">Yes</span></span>   | <span data-ttu-id="9d867-533">匹配表达式</span><span class="sxs-lookup"><span data-stu-id="9d867-533">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="9d867-534">MZ</span><span class="sxs-lookup"><span data-stu-id="9d867-534">MZ</span></span>        | <span data-ttu-id="9d867-535">是</span><span class="sxs-lookup"><span data-stu-id="9d867-535">Yes</span></span>   | <span data-ttu-id="9d867-536">不区分大小写</span><span class="sxs-lookup"><span data-stu-id="9d867-536">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="9d867-537">hello</span><span class="sxs-lookup"><span data-stu-id="9d867-537">hello</span></span>     | <span data-ttu-id="9d867-538">No</span><span class="sxs-lookup"><span data-stu-id="9d867-538">No</span></span>    | <span data-ttu-id="9d867-539">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="9d867-539">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="9d867-540">123abc456</span><span class="sxs-lookup"><span data-stu-id="9d867-540">123abc456</span></span> | <span data-ttu-id="9d867-541">No</span><span class="sxs-lookup"><span data-stu-id="9d867-541">No</span></span>    | <span data-ttu-id="9d867-542">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="9d867-542">See `^` and `$` above</span></span> |

<span data-ttu-id="9d867-543">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="9d867-543">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="9d867-544">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="9d867-544">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="9d867-545">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="9d867-545">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="9d867-546">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="9d867-546">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="9d867-547">已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="9d867-547">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="9d867-548">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="9d867-548">Custom Route Constraints</span></span>

<span data-ttu-id="9d867-549">除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="9d867-549">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="9d867-550">`IRouteConstraint` 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="9d867-550">The `IRouteConstraint` interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="9d867-551">若要使用自定义 `IRouteConstraint`，必须在应用的服务容器中使用应用的 `RouteOptions.ConstraintMap` 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="9d867-551">To use a custom `IRouteConstraint`, the route constraint type must be registered with the app's `RouteOptions.ConstraintMap` in the app's service container.</span></span> <span data-ttu-id="9d867-552"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 `IRouteConstraint` 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="9d867-552">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to `IRouteConstraint` implementations that validate those constraints.</span></span> <span data-ttu-id="9d867-553">应用的 `RouteOptions.ConstraintMap` 可作为 `services.AddRouting` 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 `RouteOptions` 进行更新。</span><span class="sxs-lookup"><span data-stu-id="9d867-553">An app's `RouteOptions.ConstraintMap` can be updated in `Startup.ConfigureServices` either as part of a `services.AddRouting` call or by configuring `RouteOptions` directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="9d867-554">例如:</span><span class="sxs-lookup"><span data-stu-id="9d867-554">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="9d867-555">然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-555">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="9d867-556">例如:</span><span class="sxs-lookup"><span data-stu-id="9d867-556">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

::: moniker range=">= aspnetcore-2.2"

## <a name="parameter-transformer-reference"></a><span data-ttu-id="9d867-557">参数转换器参考</span><span class="sxs-lookup"><span data-stu-id="9d867-557">Parameter transformer reference</span></span>

<span data-ttu-id="9d867-558">参数转换器：</span><span class="sxs-lookup"><span data-stu-id="9d867-558">Parameter transformers:</span></span>

* <span data-ttu-id="9d867-559">在为 `Route` 生成链接时执行。</span><span class="sxs-lookup"><span data-stu-id="9d867-559">Execute when generating a link for a `Route`.</span></span>
* <span data-ttu-id="9d867-560">实现 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。</span><span class="sxs-lookup"><span data-stu-id="9d867-560">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="9d867-561">使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="9d867-561">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="9d867-562">获取参数的路由值并将其转换为新的字符串值。</span><span class="sxs-lookup"><span data-stu-id="9d867-562">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="9d867-563">在生成的链接中使用转换后的值的结果。</span><span class="sxs-lookup"><span data-stu-id="9d867-563">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="9d867-564">例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="9d867-564">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="9d867-565">若要在路由模式中使用参数转换器，请先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：</span><span class="sxs-lookup"><span data-stu-id="9d867-565">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="9d867-566">框架使用参数转化器来转换进行终结点解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="9d867-566">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="9d867-567">例如，ASP.NET Core MVC 使用参数转换器来转换用于匹配 `area``controller``action` 和 `page` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-567">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="9d867-568">使用上述路由，操作 `SubscriptionManagementController.GetAll()` 与 URI `/subscription-management/get-all` 匹配。</span><span class="sxs-lookup"><span data-stu-id="9d867-568">With the preceding route, the action `SubscriptionManagementController.GetAll()` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="9d867-569">参数转换器不会更改用于生成链接的路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-569">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="9d867-570">例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="9d867-570">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="9d867-571">对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：</span><span class="sxs-lookup"><span data-stu-id="9d867-571">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="9d867-572">ASP.NET Core MVC 还具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="9d867-572">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="9d867-573">该约定将指定的参数转换器应用于应用中的所有属性路由。</span><span class="sxs-lookup"><span data-stu-id="9d867-573">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="9d867-574">在替换属性路径令牌时，参数转换器将转换这些令牌。</span><span class="sxs-lookup"><span data-stu-id="9d867-574">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="9d867-575">有关详细信息，请参阅[使用参数转换器自定义标记替换](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="9d867-575">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="9d867-576">Razor Pages 具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="9d867-576">Razor Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="9d867-577">此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="9d867-577">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="9d867-578">参数转换器转换 Razor Pages.路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="9d867-578">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="9d867-579">有关详细信息，请参阅[使用参数转换器自定义页面路由](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="9d867-579">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

::: moniker-end

## <a name="url-generation-reference"></a><span data-ttu-id="9d867-580">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="9d867-580">URL generation reference</span></span>

<span data-ttu-id="9d867-581">以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。</span><span class="sxs-lookup"><span data-stu-id="9d867-581">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="9d867-582">上述示例末尾生成的 `VirtualPath` 为 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="9d867-582">The `VirtualPath` generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="9d867-583">字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="9d867-583">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="9d867-584">有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。</span><span class="sxs-lookup"><span data-stu-id="9d867-584">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="9d867-585">`VirtualPathContext` 构造函数的第二个参数是环境值的集合。</span><span class="sxs-lookup"><span data-stu-id="9d867-585">The second parameter to the `VirtualPathContext` constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="9d867-586">由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。</span><span class="sxs-lookup"><span data-stu-id="9d867-586">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="9d867-587">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="9d867-587">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="9d867-588">在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。</span><span class="sxs-lookup"><span data-stu-id="9d867-588">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="9d867-589">忽略与参数不匹配的环境值。</span><span class="sxs-lookup"><span data-stu-id="9d867-589">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="9d867-590">在显式提供的值会覆盖环境值的情况下，也会忽略环境值。</span><span class="sxs-lookup"><span data-stu-id="9d867-590">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="9d867-591">在 URL 中将从左到右进行匹配。</span><span class="sxs-lookup"><span data-stu-id="9d867-591">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="9d867-592">显式提供但与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="9d867-592">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="9d867-593">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="9d867-593">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="9d867-594">环境值</span><span class="sxs-lookup"><span data-stu-id="9d867-594">Ambient Values</span></span>                     | <span data-ttu-id="9d867-595">显式值</span><span class="sxs-lookup"><span data-stu-id="9d867-595">Explicit Values</span></span>                        | <span data-ttu-id="9d867-596">结果</span><span class="sxs-lookup"><span data-stu-id="9d867-596">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="9d867-597">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="9d867-597">controller = "Home"</span></span>                | <span data-ttu-id="9d867-598">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="9d867-598">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="9d867-599">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="9d867-599">controller = "Home"</span></span>                | <span data-ttu-id="9d867-600">控制器 =“Order”，操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="9d867-600">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="9d867-601">控制器 = “Home”，颜色 = “Red”</span><span class="sxs-lookup"><span data-stu-id="9d867-601">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="9d867-602">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="9d867-602">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="9d867-603">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="9d867-603">controller = "Home"</span></span>                | <span data-ttu-id="9d867-604">操作 =“About”，颜色 =“Red”</span><span class="sxs-lookup"><span data-stu-id="9d867-604">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="9d867-605">如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：</span><span class="sxs-lookup"><span data-stu-id="9d867-605">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="9d867-606">当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。</span><span class="sxs-lookup"><span data-stu-id="9d867-606">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="9d867-607">复杂段</span><span class="sxs-lookup"><span data-stu-id="9d867-607">Complex segments</span></span>

<span data-ttu-id="9d867-608">复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="9d867-608">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="9d867-609">请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。</span><span class="sxs-lookup"><span data-stu-id="9d867-609">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="9d867-610">ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。</span><span class="sxs-lookup"><span data-stu-id="9d867-610">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->
