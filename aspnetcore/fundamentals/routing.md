---
title: ASP.NET Core 中的路由
author: rick-anderson
description: 了解 ASP.NET Core 路由如何负责将请求 URI 映射到终结点选择器并向终结点调度传入的请求。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/24/2019
uid: fundamentals/routing
ms.openlocfilehash: 8b4da4e1e262ec82225413d0338b3492d0b5e152
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190501"
---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="05e65-103">ASP.NET Core 中的路由</span><span class="sxs-lookup"><span data-stu-id="05e65-103">Routing in ASP.NET Core</span></span>

<span data-ttu-id="05e65-104">撰写者：[Ryan Nowak](https://github.com/rynowak)、[Steve Smith](https://ardalis.com/) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="05e65-104">By [Ryan Nowak](https://github.com/rynowak), [Steve Smith](https://ardalis.com/), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="05e65-105">路由负责将请求 URI 映射到终结点并向这些终结点调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-105">Routing is responsible for mapping request URIs to endpoints and dispatching incoming requests to those endpoints.</span></span> <span data-ttu-id="05e65-106">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-106">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="05e65-107">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-107">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="05e65-108">通过使用应用中的路由信息，路由还能生成映射到终结点的 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-108">Using route information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="05e65-109">本文档介绍较低级别的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-109">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="05e65-110">有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="05e65-110">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="05e65-111">有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-111">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="05e65-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="05e65-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="05e65-113">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="05e65-113">Routing basics</span></span>

<span data-ttu-id="05e65-114">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="05e65-114">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="05e65-115">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="05e65-115">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="05e65-116">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="05e65-116">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="05e65-117">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="05e65-117">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="05e65-118">开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-118">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="05e65-119">Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="05e65-119">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="05e65-120">也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-120">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="05e65-121">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="05e65-121">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="05e65-122">Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源  。</span><span class="sxs-lookup"><span data-stu-id="05e65-122">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="05e65-123">还可以使用其他约定来自定义 Razor Pages 路由行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-123">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="05e65-124">有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-124">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="05e65-125">借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。</span><span class="sxs-lookup"><span data-stu-id="05e65-125">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="05e65-126">此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-126">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="05e65-127">路由使用“终结点”(`Endpoint`) 来表示应用中的逻辑终结点  。</span><span class="sxs-lookup"><span data-stu-id="05e65-127">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="05e65-128">终结点定义用于处理请求的委托和任意元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="05e65-128">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="05e65-129">元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-129">The metadata is used to implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="05e65-130">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="05e65-130">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="05e65-131">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-131">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="05e65-132">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-132">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="05e65-133"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="05e65-133"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="05e65-134">应用模型（如 MVC/Razor Pages）注册其所有终结点，这些终结点具有可预测的路由方案实现。</span><span class="sxs-lookup"><span data-stu-id="05e65-134">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="05e65-135">路由实现会在中间件管道中任何所需位置制定路由决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-135">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="05e65-136">路由中间件之后出现的中间件可以检查路由中间件针对给定请求 URI 的终结点决策结果。</span><span class="sxs-lookup"><span data-stu-id="05e65-136">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="05e65-137">可以在中间件管道中的任何位置枚举应用中的所有终结点。</span><span class="sxs-lookup"><span data-stu-id="05e65-137">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="05e65-138">应用可根据终结点信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="05e65-138">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="05e65-139">URL 生成是基于支持任意可扩展性的地址：</span><span class="sxs-lookup"><span data-stu-id="05e65-139">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="05e65-140">可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在任意位置解析链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) 以生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-140">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="05e65-141">如果无法通过 DI 获得链接生成器 API，则 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 会提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-141">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="05e65-142">终结点链接仅限于 MVC/Razor Pages 操作和页。</span><span class="sxs-lookup"><span data-stu-id="05e65-142">Endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="05e65-143">将计划在未来发布的版本中扩展终结点链接的功能。</span><span class="sxs-lookup"><span data-stu-id="05e65-143">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

<span data-ttu-id="05e65-144">路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。</span><span class="sxs-lookup"><span data-stu-id="05e65-144">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="05e65-145">[ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-145">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="05e65-146">要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-146">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="05e65-147">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-147">URL matching</span></span>

<span data-ttu-id="05e65-148">URL 匹配是路由向终结点调度传入请求的过程  。</span><span class="sxs-lookup"><span data-stu-id="05e65-148">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="05e65-149">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="05e65-149">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="05e65-150">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="05e65-150">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="05e65-151">当路由中间件执行时，它会设置终结点 (`Endpoint`) 并将值路由到 <xref:Microsoft.AspNetCore.Http.HttpContext> 上的功能。</span><span class="sxs-lookup"><span data-stu-id="05e65-151">When a Routing Middleware executes, it sets an endpoint (`Endpoint`) and route values to a feature on the <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="05e65-152">对于当前请求：</span><span class="sxs-lookup"><span data-stu-id="05e65-152">For the current request:</span></span>

* <span data-ttu-id="05e65-153">调用 `HttpContext.GetEndpoint` 将获取终结点。</span><span class="sxs-lookup"><span data-stu-id="05e65-153">Calling `HttpContext.GetEndpoint` gets the endpoint.</span></span>
* <span data-ttu-id="05e65-154">`HttpRequest.RouteValues` 将获取路由值的集合。</span><span class="sxs-lookup"><span data-stu-id="05e65-154">`HttpRequest.RouteValues` gets the collection of route values.</span></span>

<span data-ttu-id="05e65-155">在路由中间件之后运行的中间件可以看到终结点并采取措施。</span><span class="sxs-lookup"><span data-stu-id="05e65-155">Middleware running after the Routing Middleware can see the endpoint and take action.</span></span> <span data-ttu-id="05e65-156">例如，授权中间件可以在终结点的元数据集合中询问授权策略。</span><span class="sxs-lookup"><span data-stu-id="05e65-156">For example, an Authorization Middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span> <span data-ttu-id="05e65-157">请求处理管道中的所有中间件执行后，将调用所选终结点的委托。</span><span class="sxs-lookup"><span data-stu-id="05e65-157">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>

<span data-ttu-id="05e65-158">终结点路由中的路由系统负责所有的调度决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-158">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="05e65-159">由于中间件是基于所选终结点来应用策略，因此任何可能影响调度或安全策略应用的决策都应在路由系统内部制定，这一点很重要。</span><span class="sxs-lookup"><span data-stu-id="05e65-159">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="05e65-160">执行终结点委托时，将根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-160">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="05e65-161">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典  。</span><span class="sxs-lookup"><span data-stu-id="05e65-161">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="05e65-162">这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-162">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="05e65-163">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。</span><span class="sxs-lookup"><span data-stu-id="05e65-163">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="05e65-164">提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-164"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="05e65-165">这些值是开发者定义的，不会影响通过任何方式路由的行为  。</span><span class="sxs-lookup"><span data-stu-id="05e65-165">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="05e65-166">此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。</span><span class="sxs-lookup"><span data-stu-id="05e65-166">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="05e65-167">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。</span><span class="sxs-lookup"><span data-stu-id="05e65-167">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="05e65-168">路由可以相互嵌套。</span><span class="sxs-lookup"><span data-stu-id="05e65-168">Routes can be nested inside of one another.</span></span> <span data-ttu-id="05e65-169"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-169">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="05e65-170">通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-170">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="05e65-171"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="05e65-171">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="05e65-172">使用 LinkGenerator 的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="05e65-172">URL generation with LinkGenerator</span></span>

<span data-ttu-id="05e65-173">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="05e65-173">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="05e65-174">这需要考虑终结点与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="05e65-174">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="05e65-175">终结点路由包含链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)。</span><span class="sxs-lookup"><span data-stu-id="05e65-175">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="05e65-176"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 是可从 DI 检索的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="05e65-176"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from DI.</span></span> <span data-ttu-id="05e65-177">该 API 可在执行请求的上下文之外使用。</span><span class="sxs-lookup"><span data-stu-id="05e65-177">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="05e65-178">MVC 的 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 和依赖 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的方案（如 [Tag Helpers](xref:mvc/views/tag-helpers/intro)、HTML Helpers 和 [Action Results](xref:mvc/controllers/actions)）使用链接生成器提供链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="05e65-178">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="05e65-179">链接生成器基于“地址”和“地址方案”概念   。</span><span class="sxs-lookup"><span data-stu-id="05e65-179">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="05e65-180">地址方案是确定哪些终结点用于链接生成的方式。</span><span class="sxs-lookup"><span data-stu-id="05e65-180">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="05e65-181">例如，许多用户熟悉的来自 MVC/Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。</span><span class="sxs-lookup"><span data-stu-id="05e65-181">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="05e65-182">链接生成器可以通过以下扩展方法链接到 MVC/Razor Pages 操作和页面：</span><span class="sxs-lookup"><span data-stu-id="05e65-182">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="05e65-183">这些方法的重载接受包含 `HttpContext` 的参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-183">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="05e65-184">这些方法在功能上等同于 `Url.Action` 和 `Url.Page`，但提供了更大的灵活性和更多的选项。</span><span class="sxs-lookup"><span data-stu-id="05e65-184">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="05e65-185">`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-185">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="05e65-186">`GetUri*` 方法始终生成包含方案和主机的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-186">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="05e65-187">接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-187">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="05e65-188">除非重写，否则将使用来自执行请求的环境路由值、URL 基本路径、方案和主机。</span><span class="sxs-lookup"><span data-stu-id="05e65-188">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="05e65-189">使用地址调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。</span><span class="sxs-lookup"><span data-stu-id="05e65-189"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="05e65-190">生成 URI 的过程分两步进行：</span><span class="sxs-lookup"><span data-stu-id="05e65-190">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="05e65-191">将地址绑定到与地址匹配的终结点列表。</span><span class="sxs-lookup"><span data-stu-id="05e65-191">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="05e65-192">计算每个终结点的 `RoutePattern`，直到找到与提供的值匹配的路由模式。</span><span class="sxs-lookup"><span data-stu-id="05e65-192">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="05e65-193">输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。</span><span class="sxs-lookup"><span data-stu-id="05e65-193">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="05e65-194">对任何类型的地址，<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法均支持标准链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="05e65-194">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="05e65-195">使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作。</span><span class="sxs-lookup"><span data-stu-id="05e65-195">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="05e65-196">扩展方法</span><span class="sxs-lookup"><span data-stu-id="05e65-196">Extension Method</span></span> | <span data-ttu-id="05e65-197">说明</span><span class="sxs-lookup"><span data-stu-id="05e65-197">Description</span></span> |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="05e65-198">根据提供的值生成具有绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-198">Generates a URI with an absolute path based on the provided values.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="05e65-199">根据提供的值生成绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-199">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="05e65-200">请注意有关调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列含义：</span><span class="sxs-lookup"><span data-stu-id="05e65-200">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="05e65-201">对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-201">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="05e65-202">如果未验证传入请求的 `Host`标头，则可能以视图/页面中 URI 的形式将不受信任的请求输入发送回客户端。</span><span class="sxs-lookup"><span data-stu-id="05e65-202">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="05e65-203">建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="05e65-203">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="05e65-204">在中间件中将 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="05e65-204">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="05e65-205">`Map*` 会更改执行请求的基路径，这会影响链接生成的输出。</span><span class="sxs-lookup"><span data-stu-id="05e65-205">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="05e65-206">所有 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允许指定基路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-206">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="05e65-207">始终指定一个空的基路径来撤消 `Map*` 对链接生成的影响。</span><span class="sxs-lookup"><span data-stu-id="05e65-207">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="05e65-208">与早期版本路由的差异</span><span class="sxs-lookup"><span data-stu-id="05e65-208">Differences from earlier versions of routing</span></span>

<span data-ttu-id="05e65-209">比 ASP.NET Core 2.2 更早的版本中的终结点路由与版本路由之间存在一些差异：</span><span class="sxs-lookup"><span data-stu-id="05e65-209">A few differences exist between endpoint routing and versions of routing earlier than in ASP.NET Core 2.2:</span></span>

* <span data-ttu-id="05e65-210">终结点路由系统不支持基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的可扩展性，包括从 <xref:Microsoft.AspNetCore.Routing.Route> 继承。</span><span class="sxs-lookup"><span data-stu-id="05e65-210">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="05e65-211">终结点路由不支持 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。</span><span class="sxs-lookup"><span data-stu-id="05e65-211">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="05e65-212">使用 2.1 [兼容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 以继续使用兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="05e65-212">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="05e65-213">在使用传统路由时，终结点路由对生成的 URI 的大小写具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-213">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="05e65-214">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="05e65-214">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="05e65-215">假设使用以下路由生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="05e65-215">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="05e65-216">如果使用基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由，此代码生成 URI `/blog/ReadPost/17`，该 URI 遵循所提供路由值的大小写。</span><span class="sxs-lookup"><span data-stu-id="05e65-216">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="05e65-217">ASP.NET Core 2.2 或更高版本中的终结点路由生成 `/Blog/ReadPost/17`（“Blog”大写）。</span><span class="sxs-lookup"><span data-stu-id="05e65-217">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="05e65-218">终结点路由提供 `IOutboundParameterTransformer` 接口，可用于在全局范围自定义此行为或应用不同的约定来映射 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-218">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="05e65-219">有关详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-219">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="05e65-220">在试图链接到不存在的控制器/操作或页面时，MVC/Razor Pages 通过传统路由执行的链接生成，其操作具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-220">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="05e65-221">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="05e65-221">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="05e65-222">假设使用以下路由通过默认模板生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="05e65-222">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="05e65-223">如果使用基于 `IRouter` 的路由，即使 `BlogController` 不存在或没有 `ReadPost` 操作方法，结果也始终为 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="05e65-223">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="05e65-224">正如所料，如果操作方法存在，ASP.NET Core 2.2 或更高版本中的终结点路由会生成 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="05e65-224">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="05e65-225">但是，如果操作不存在，终结点路由会生成空字符串。 </span><span class="sxs-lookup"><span data-stu-id="05e65-225">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="05e65-226">从概念上讲，如果操作不存在，终结点路由不会假定终结点存在。</span><span class="sxs-lookup"><span data-stu-id="05e65-226">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="05e65-227">与终结点路由一起使用时，链接生成环境值失效算法的行为会有所不同  。</span><span class="sxs-lookup"><span data-stu-id="05e65-227">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="05e65-228">*环境值失效*是一种算法，用于决定当前正在执行的请求（环境值）中的哪些路由值可用于链接生成操作。</span><span class="sxs-lookup"><span data-stu-id="05e65-228">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="05e65-229">链接到不同操作时，传统路由会使额外的路由值失效。</span><span class="sxs-lookup"><span data-stu-id="05e65-229">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="05e65-230">ASP.NET Core 2.2 之前的版本中，属性路由不具有此行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-230">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="05e65-231">在 ASP.NET Core 的早期版本中，如果有另一个操作使用同一路由参数名称，则该操作的链接会导致发生链接生成错误。</span><span class="sxs-lookup"><span data-stu-id="05e65-231">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="05e65-232">在 ASP.NET Core 2.2 或更高版本中，链接到另一个操作时，这两种路由形式都会使值失效。</span><span class="sxs-lookup"><span data-stu-id="05e65-232">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="05e65-233">请考虑 ASP.NET Core2.1 或更高版本中的以下示例。</span><span class="sxs-lookup"><span data-stu-id="05e65-233">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="05e65-234">链接到另一个操作（或另一页面）时，路由值可能会按非预期的方式被重用。</span><span class="sxs-lookup"><span data-stu-id="05e65-234">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="05e65-235">在 /Pages/Store/Product.cshtml 中  ：</span><span class="sxs-lookup"><span data-stu-id="05e65-235">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="05e65-236">在 /Pages/Login.cshtml 中  ：</span><span class="sxs-lookup"><span data-stu-id="05e65-236">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="05e65-237">如果 ASP.NET Core 2.1 或更早版本中的 URI 为 `/Store/Product/18`，则由 `@Url.Page("/Login")` 在 Store/Info 页面上生成的链接为 `/Login/18`。</span><span class="sxs-lookup"><span data-stu-id="05e65-237">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="05e65-238">即使链接目标完全是应用的其他部分，仍会重用 `id` 值 18。</span><span class="sxs-lookup"><span data-stu-id="05e65-238">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="05e65-239">`/Login` 页面上下文中的 `id` 路由值可能是用户 ID 值，而非存储产品 ID 值。</span><span class="sxs-lookup"><span data-stu-id="05e65-239">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="05e65-240">在 ASP.NET Core 2.2 或更高版本的终结点路由中，结果为 `/Login`。</span><span class="sxs-lookup"><span data-stu-id="05e65-240">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="05e65-241">当链接的目标是另一个操作或页面时，不会重复使用环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-241">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="05e65-242">往返路由参数语法：使用双星号 (`**`) catch-all 参数语法时，不对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="05e65-242">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="05e65-243">在链接生成期间，路由系统对双星号 (`**`) catch-all 参数（例如，`{**myparametername}`）中捕获的除正斜杠外的值进行编码。</span><span class="sxs-lookup"><span data-stu-id="05e65-243">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="05e65-244">在 ASP.NET Core 2.2 或更高版本中，基于 `IRouter` 的路由支持双星号 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-244">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="05e65-245">ASP.NET Core (`{*myparametername}`) 早期版本中的单星号 catch-all 参数语法仍然受支持，并对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="05e65-245">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="05e65-246">路由</span><span class="sxs-lookup"><span data-stu-id="05e65-246">Route</span></span>              | <span data-ttu-id="05e65-247">链接生成方式为</span><span class="sxs-lookup"><span data-stu-id="05e65-247">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | <span data-ttu-id="05e65-248">`/search/admin%2Fproducts`（对正斜杠进行编码）</span><span class="sxs-lookup"><span data-stu-id="05e65-248">`/search/admin%2Fproducts` (the forward slash is encoded)</span></span>             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a><span data-ttu-id="05e65-249">中间件示例</span><span class="sxs-lookup"><span data-stu-id="05e65-249">Middleware example</span></span>

<span data-ttu-id="05e65-250">在以下示例中，中间件使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 创建列出存储产品的操作方法的链接。</span><span class="sxs-lookup"><span data-stu-id="05e65-250">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="05e65-251">应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器。</span><span class="sxs-lookup"><span data-stu-id="05e65-251">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

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

### <a name="create-routes"></a><span data-ttu-id="05e65-252">创建路由</span><span class="sxs-lookup"><span data-stu-id="05e65-252">Create routes</span></span>

<span data-ttu-id="05e65-253">大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-253">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="05e65-254">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。</span><span class="sxs-lookup"><span data-stu-id="05e65-254">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="05e65-255"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-255"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="05e65-256"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-256"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="05e65-257">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="05e65-257">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="05e65-258">以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：</span><span class="sxs-lookup"><span data-stu-id="05e65-258">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="05e65-259">此模板与 URL 路径相匹配，并且提取路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-259">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="05e65-260">例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-260">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="05e65-261">路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的  。</span><span class="sxs-lookup"><span data-stu-id="05e65-261">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="05e65-262">路由参数已命名。</span><span class="sxs-lookup"><span data-stu-id="05e65-262">Route parameters are named.</span></span> <span data-ttu-id="05e65-263">参数通过将参数名称括在大括号 `{ ... }` 中来定义。</span><span class="sxs-lookup"><span data-stu-id="05e65-263">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="05e65-264">上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-264">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="05e65-265">这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="05e65-265">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="05e65-266">路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。</span><span class="sxs-lookup"><span data-stu-id="05e65-266">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="05e65-267">路由参数名称后面的问号 (`?`) 定义了可选参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-267">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="05e65-268">路由匹配时，具有默认值的路由参数始终会生成路由值  。</span><span class="sxs-lookup"><span data-stu-id="05e65-268">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="05e65-269">如果没有相应的 URL 路径段，则可选参数不会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-269">Optional parameters don't produce a route value if there was no corresponding URL path segment.</span></span> <span data-ttu-id="05e65-270">有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-270">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="05e65-271">在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="05e65-271">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="05e65-272">此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-272">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="05e65-273">路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。</span><span class="sxs-lookup"><span data-stu-id="05e65-273">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="05e65-274">在此示例中，路由值 `id` 必须可转换为整数。</span><span class="sxs-lookup"><span data-stu-id="05e65-274">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="05e65-275">有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="05e65-275">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="05e65-276">其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-276">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="05e65-277">这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="05e65-277">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="05e65-278">以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：</span><span class="sxs-lookup"><span data-stu-id="05e65-278">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

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
> <span data-ttu-id="05e65-279">定义约束和默认值的内联语法对于简单路由很方便。</span><span class="sxs-lookup"><span data-stu-id="05e65-279">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="05e65-280">但是，存在内联语法不支持的方案，例如数据令牌。</span><span class="sxs-lookup"><span data-stu-id="05e65-280">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="05e65-281">以下示例演示了一些其他方案：</span><span class="sxs-lookup"><span data-stu-id="05e65-281">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="05e65-282">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-282">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="05e65-283">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-283">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="05e65-284">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-284">Default values can be specified in the route template.</span></span> <span data-ttu-id="05e65-285">根据路由参数名称前的双星号 (`**`) 外观，`article` 路由参数被定义为 catch-all  。</span><span class="sxs-lookup"><span data-stu-id="05e65-285">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="05e65-286">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-286">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="05e65-287">以下示例添加了路由约束和数据令牌：</span><span class="sxs-lookup"><span data-stu-id="05e65-287">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="05e65-288">上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-288">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="05e65-290">路由类 URL 生成</span><span class="sxs-lookup"><span data-stu-id="05e65-290">Route class URL generation</span></span>

<span data-ttu-id="05e65-291"><xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-291">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="05e65-292">从逻辑上来说，这是匹配 URL 路径的反向过程。</span><span class="sxs-lookup"><span data-stu-id="05e65-292">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="05e65-293">为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-293">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="05e65-294">将生成什么值？</span><span class="sxs-lookup"><span data-stu-id="05e65-294">What values would be produced?</span></span> <span data-ttu-id="05e65-295">这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。</span><span class="sxs-lookup"><span data-stu-id="05e65-295">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="05e65-296">以下示例使用常规 ASP.NET Core MVC 默认路由：</span><span class="sxs-lookup"><span data-stu-id="05e65-296">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="05e65-297">使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="05e65-297">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="05e65-298">路由值将替换为相应的路由参数，以形成 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-298">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="05e65-299">由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-299">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="05e65-300">使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="05e65-300">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="05e65-301">提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。</span><span class="sxs-lookup"><span data-stu-id="05e65-301">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="05e65-302">已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-302">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="05e65-303">使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-303">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="05e65-304">有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-304">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="05e65-305">使用路由中间件</span><span class="sxs-lookup"><span data-stu-id="05e65-305">Use Routing Middleware</span></span>

<span data-ttu-id="05e65-306">引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="05e65-306">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="05e65-307">将路由添加到 `Startup.ConfigureServices` 中的服务容器：</span><span class="sxs-lookup"><span data-stu-id="05e65-307">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="05e65-308">必须在 `Startup.Configure` 方法中配置路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-308">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="05e65-309">该示例应用使用以下 API：</span><span class="sxs-lookup"><span data-stu-id="05e65-309">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="05e65-310"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; 仅匹配 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-310"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="05e65-311">下表显示了具有给定 URI 的响应。</span><span class="sxs-lookup"><span data-stu-id="05e65-311">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="05e65-312">URI</span><span class="sxs-lookup"><span data-stu-id="05e65-312">URI</span></span>                    | <span data-ttu-id="05e65-313">响应</span><span class="sxs-lookup"><span data-stu-id="05e65-313">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="05e65-314">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-314">Hello!</span></span> <span data-ttu-id="05e65-315">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="05e65-315">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="05e65-316">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-316">Hello!</span></span> <span data-ttu-id="05e65-317">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="05e65-317">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="05e65-318">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-318">Hello!</span></span> <span data-ttu-id="05e65-319">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="05e65-319">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="05e65-320">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-320">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="05e65-321">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="05e65-321">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="05e65-322">请求失败，仅匹配 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="05e65-322">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="05e65-323">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-323">The request falls through, no match.</span></span>              |

<span data-ttu-id="05e65-324">框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="05e65-324">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="05e65-325">`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="05e65-325">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="05e65-326">有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="05e65-326">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="05e65-327">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="05e65-327">Route template reference</span></span>

<span data-ttu-id="05e65-328">如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数  。</span><span class="sxs-lookup"><span data-stu-id="05e65-328">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="05e65-329">可在路由段中定义多个路由参数，但必须由文本值隔开。</span><span class="sxs-lookup"><span data-stu-id="05e65-329">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="05e65-330">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="05e65-330">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="05e65-331">这些路由参数必须具有名称，且可能指定了其他属性。</span><span class="sxs-lookup"><span data-stu-id="05e65-331">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="05e65-332">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="05e65-332">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="05e65-333">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="05e65-333">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="05e65-334">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="05e65-334">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="05e65-335">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="05e65-335">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="05e65-336">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="05e65-336">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="05e65-337">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="05e65-337">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="05e65-338">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。</span><span class="sxs-lookup"><span data-stu-id="05e65-338">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="05e65-339">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="05e65-339">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="05e65-340">可以使用星号 (`*`) 或双星号 (`**`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-340">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="05e65-341">这些称为 catch-all 参数  。</span><span class="sxs-lookup"><span data-stu-id="05e65-341">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="05e65-342">例如，`blog/{**slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-342">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="05e65-343">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-343">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="05e65-344">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="05e65-344">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="05e65-345">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="05e65-345">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="05e65-346">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="05e65-346">Note the escaped forward slash.</span></span> <span data-ttu-id="05e65-347">要往返路径分隔符，请使用 `**` 路由参数前缀。</span><span class="sxs-lookup"><span data-stu-id="05e65-347">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="05e65-348">`{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="05e65-348">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="05e65-349">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值  。</span><span class="sxs-lookup"><span data-stu-id="05e65-349">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="05e65-350">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-350">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="05e65-351">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-351">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="05e65-352">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="05e65-352">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="05e65-353">可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。</span><span class="sxs-lookup"><span data-stu-id="05e65-353">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="05e65-354">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-354">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="05e65-355">在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束  。</span><span class="sxs-lookup"><span data-stu-id="05e65-355">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="05e65-356">如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="05e65-356">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="05e65-357">通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-357">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="05e65-358">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-358">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="05e65-359">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-359">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="05e65-360">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-360">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="05e65-361">路由参数还可以具有参数转换器，用于在生成链接以及将操作和页面匹配到 URI 时转换参数的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-361">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="05e65-362">与约束类似，可以通过在路由参数名称后面添加冒号 (`:`) 和转换器名称，将参数变换器内联添加到路径参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-362">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="05e65-363">例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。</span><span class="sxs-lookup"><span data-stu-id="05e65-363">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="05e65-364">有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-364">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="05e65-365">下表演示了示例路由模板及其行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-365">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="05e65-366">路由模板</span><span class="sxs-lookup"><span data-stu-id="05e65-366">Route Template</span></span>                           | <span data-ttu-id="05e65-367">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="05e65-367">Example Matching URI</span></span>    | <span data-ttu-id="05e65-368">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="05e65-368">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="05e65-369">仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="05e65-369">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="05e65-370">匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="05e65-370">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="05e65-371">匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="05e65-371">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="05e65-372">映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="05e65-372">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="05e65-373">映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。</span><span class="sxs-lookup"><span data-stu-id="05e65-373">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="05e65-374">映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。</span><span class="sxs-lookup"><span data-stu-id="05e65-374">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="05e65-375">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-375">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="05e65-376">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-376">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="05e65-377">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-377">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="05e65-378">保留的路由名称</span><span class="sxs-lookup"><span data-stu-id="05e65-378">Reserved routing names</span></span>

<span data-ttu-id="05e65-379">以下关键字是保留的名称，它们不能用作路由名称或参数：</span><span class="sxs-lookup"><span data-stu-id="05e65-379">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="05e65-380">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="05e65-380">Route constraint reference</span></span>

<span data-ttu-id="05e65-381">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-381">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="05e65-382">路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。</span><span class="sxs-lookup"><span data-stu-id="05e65-382">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="05e65-383">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-383">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="05e65-384">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-384">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="05e65-385">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="05e65-385">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="05e65-386">请勿将约束用于“输入验证”  。</span><span class="sxs-lookup"><span data-stu-id="05e65-386">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="05e65-387">如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求”    。</span><span class="sxs-lookup"><span data-stu-id="05e65-387">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="05e65-388">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入  。</span><span class="sxs-lookup"><span data-stu-id="05e65-388">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="05e65-389">下表演示示例路由约束及其预期行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-389">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="05e65-390">约束</span><span class="sxs-lookup"><span data-stu-id="05e65-390">constraint</span></span> | <span data-ttu-id="05e65-391">示例</span><span class="sxs-lookup"><span data-stu-id="05e65-391">Example</span></span> | <span data-ttu-id="05e65-392">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="05e65-392">Example Matches</span></span> | <span data-ttu-id="05e65-393">说明</span><span class="sxs-lookup"><span data-stu-id="05e65-393">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="05e65-394">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="05e65-394">`123456789`, `-123456789`</span></span> | <span data-ttu-id="05e65-395">匹配任何整数</span><span class="sxs-lookup"><span data-stu-id="05e65-395">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="05e65-396">`true`， `FALSE`</span><span class="sxs-lookup"><span data-stu-id="05e65-396">`true`, `FALSE`</span></span> | <span data-ttu-id="05e65-397">匹配 `true`或 `false`（区分大小写）</span><span class="sxs-lookup"><span data-stu-id="05e65-397">Matches `true` or `false` (case-insensitive)</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="05e65-398">`2016-12-31`， `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="05e65-398">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="05e65-399">匹配有效的 `DateTime` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-399">Matches a valid `DateTime` value (in the invariant culture - see warning)</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="05e65-400">`49.99`， `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="05e65-400">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="05e65-401">匹配有效的 `decimal` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-401">Matches a valid `decimal` value (in the invariant culture - see warning)</span></span> |
| `double` | `{weight:double}` | <span data-ttu-id="05e65-402">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="05e65-402">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="05e65-403">匹配有效的 `double` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-403">Matches a valid `double` value (in the invariant culture - see warning)</span></span> |
| `float` | `{weight:float}` | <span data-ttu-id="05e65-404">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="05e65-404">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="05e65-405">匹配有效的 `float` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-405">Matches a valid `float` value (in the invariant culture - see warning)</span></span> |
| `guid` | `{id:guid}` | <span data-ttu-id="05e65-406">`CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="05e65-406">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="05e65-407">匹配有效的 `Guid` 值</span><span class="sxs-lookup"><span data-stu-id="05e65-407">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="05e65-408">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="05e65-408">`123456789`, `-123456789`</span></span> | <span data-ttu-id="05e65-409">匹配有效的 `long` 值</span><span class="sxs-lookup"><span data-stu-id="05e65-409">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="05e65-410">字符串必须至少为 4 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-410">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="05e65-411">字符串不得超过 8 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-411">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="05e65-412">字符串必须正好为 12 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-412">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="05e65-413">字符串必须至少为 8 个字符，且不得超过 16 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-413">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="05e65-414">整数值必须至少为 18</span><span class="sxs-lookup"><span data-stu-id="05e65-414">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="05e65-415">整数值不得超过 120</span><span class="sxs-lookup"><span data-stu-id="05e65-415">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="05e65-416">整数值必须至少为 18，且不得超过 120</span><span class="sxs-lookup"><span data-stu-id="05e65-416">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="05e65-417">字符串必须由一个或多个字母字符（`a`-`z`，区分大小写）组成</span><span class="sxs-lookup"><span data-stu-id="05e65-417">String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive)</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="05e65-418">字符串必须匹配正则表达式（参见有关定义正则表达式的提示）</span><span class="sxs-lookup"><span data-stu-id="05e65-418">String must match the regular expression (see tips about defining a regular expression)</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="05e65-419">用于强制在 URL 生成过程中存在非参数值</span><span class="sxs-lookup"><span data-stu-id="05e65-419">Used to enforce that a non-parameter value is present during URL generation</span></span> |

<span data-ttu-id="05e65-420">可向单个参数应用多个由冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-420">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="05e65-421">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="05e65-421">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="05e65-422">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="05e65-422">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="05e65-423">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="05e65-423">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="05e65-424">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-424">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="05e65-425">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-425">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="05e65-426">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="05e65-426">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="05e65-427">正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-427">Regular expressions</span></span>

<span data-ttu-id="05e65-428">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="05e65-428">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="05e65-429">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-429">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="05e65-430">正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="05e65-430">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="05e65-431">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="05e65-431">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="05e65-432">要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。</span><span class="sxs-lookup"><span data-stu-id="05e65-432">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="05e65-433">要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="05e65-433">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="05e65-434">下表展示了正则表达式和转义版本。</span><span class="sxs-lookup"><span data-stu-id="05e65-434">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="05e65-435">正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-435">Regular Expression</span></span>    | <span data-ttu-id="05e65-436">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-436">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="05e65-437">路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="05e65-437">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="05e65-438">表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="05e65-438">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="05e65-439">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="05e65-439">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="05e65-440">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="05e65-440">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="05e65-441">下表提供了示例并说明了它们匹配或匹配失败的原因。</span><span class="sxs-lookup"><span data-stu-id="05e65-441">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="05e65-442">表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-442">Expression</span></span>   | <span data-ttu-id="05e65-443">String</span><span class="sxs-lookup"><span data-stu-id="05e65-443">String</span></span>    | <span data-ttu-id="05e65-444">匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-444">Match</span></span> | <span data-ttu-id="05e65-445">注释</span><span class="sxs-lookup"><span data-stu-id="05e65-445">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="05e65-446">hello</span><span class="sxs-lookup"><span data-stu-id="05e65-446">hello</span></span>     | <span data-ttu-id="05e65-447">是</span><span class="sxs-lookup"><span data-stu-id="05e65-447">Yes</span></span>   | <span data-ttu-id="05e65-448">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-448">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="05e65-449">123abc456</span><span class="sxs-lookup"><span data-stu-id="05e65-449">123abc456</span></span> | <span data-ttu-id="05e65-450">是</span><span class="sxs-lookup"><span data-stu-id="05e65-450">Yes</span></span>   | <span data-ttu-id="05e65-451">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-451">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="05e65-452">mz</span><span class="sxs-lookup"><span data-stu-id="05e65-452">mz</span></span>        | <span data-ttu-id="05e65-453">是</span><span class="sxs-lookup"><span data-stu-id="05e65-453">Yes</span></span>   | <span data-ttu-id="05e65-454">匹配表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-454">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="05e65-455">MZ</span><span class="sxs-lookup"><span data-stu-id="05e65-455">MZ</span></span>        | <span data-ttu-id="05e65-456">是</span><span class="sxs-lookup"><span data-stu-id="05e65-456">Yes</span></span>   | <span data-ttu-id="05e65-457">不区分大小写</span><span class="sxs-lookup"><span data-stu-id="05e65-457">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="05e65-458">hello</span><span class="sxs-lookup"><span data-stu-id="05e65-458">hello</span></span>     | <span data-ttu-id="05e65-459">No</span><span class="sxs-lookup"><span data-stu-id="05e65-459">No</span></span>    | <span data-ttu-id="05e65-460">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="05e65-460">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="05e65-461">123abc456</span><span class="sxs-lookup"><span data-stu-id="05e65-461">123abc456</span></span> | <span data-ttu-id="05e65-462">No</span><span class="sxs-lookup"><span data-stu-id="05e65-462">No</span></span>    | <span data-ttu-id="05e65-463">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="05e65-463">See `^` and `$` above</span></span> |

<span data-ttu-id="05e65-464">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="05e65-464">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="05e65-465">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="05e65-465">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="05e65-466">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="05e65-466">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="05e65-467">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="05e65-467">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="05e65-468">已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="05e65-468">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="05e65-469">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="05e65-469">Custom Route Constraints</span></span>

<span data-ttu-id="05e65-470">除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-470">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="05e65-471"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="05e65-471">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="05e65-472">若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="05e65-472">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="05e65-473"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="05e65-473">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="05e65-474">应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="05e65-474">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="05e65-475">例如:</span><span class="sxs-lookup"><span data-stu-id="05e65-475">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="05e65-476">然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-476">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="05e65-477">例如:</span><span class="sxs-lookup"><span data-stu-id="05e65-477">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a><span data-ttu-id="05e65-478">参数转换器参考</span><span class="sxs-lookup"><span data-stu-id="05e65-478">Parameter transformer reference</span></span>

<span data-ttu-id="05e65-479">参数转换器：</span><span class="sxs-lookup"><span data-stu-id="05e65-479">Parameter transformers:</span></span>

* <span data-ttu-id="05e65-480">在为 <xref:Microsoft.AspNetCore.Routing.Route> 生成链接时执行。</span><span class="sxs-lookup"><span data-stu-id="05e65-480">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="05e65-481">实现 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。</span><span class="sxs-lookup"><span data-stu-id="05e65-481">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="05e65-482">使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-482">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="05e65-483">获取参数的路由值并将其转换为新的字符串值。</span><span class="sxs-lookup"><span data-stu-id="05e65-483">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="05e65-484">在生成的链接中使用转换后的值的结果。</span><span class="sxs-lookup"><span data-stu-id="05e65-484">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="05e65-485">例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="05e65-485">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="05e65-486">若要在路由模式中使用参数转换器，请先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：</span><span class="sxs-lookup"><span data-stu-id="05e65-486">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="05e65-487">框架使用参数转化器来转换进行终结点解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-487">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="05e65-488">例如，ASP.NET Core MVC 使用参数转换器来转换用于匹配 `area` `controller` `action` 和 `page` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-488">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="05e65-489">使用上述路由，操作 `SubscriptionManagementController.GetAll()` 与 URI `/subscription-management/get-all` 匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-489">With the preceding route, the action `SubscriptionManagementController.GetAll()` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="05e65-490">参数转换器不会更改用于生成链接的路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-490">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="05e65-491">例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="05e65-491">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="05e65-492">对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：</span><span class="sxs-lookup"><span data-stu-id="05e65-492">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="05e65-493">ASP.NET Core MVC 还具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="05e65-493">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="05e65-494">该约定将指定的参数转换器应用于应用中的所有属性路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-494">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="05e65-495">在替换属性路径令牌时，参数转换器将转换这些令牌。</span><span class="sxs-lookup"><span data-stu-id="05e65-495">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="05e65-496">有关详细信息，请参阅[使用参数转换器自定义标记替换](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="05e65-496">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="05e65-497">Razor Pages 具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="05e65-497">Razor Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="05e65-498">此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="05e65-498">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="05e65-499">参数转换器转换 Razor Pages.路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="05e65-499">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="05e65-500">有关详细信息，请参阅[使用参数转换器自定义页面路由](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="05e65-500">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

## <a name="url-generation-reference"></a><span data-ttu-id="05e65-501">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="05e65-501">URL generation reference</span></span>

<span data-ttu-id="05e65-502">以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。</span><span class="sxs-lookup"><span data-stu-id="05e65-502">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="05e65-503">上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="05e65-503">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="05e65-504">字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-504">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="05e65-505">有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。</span><span class="sxs-lookup"><span data-stu-id="05e65-505">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="05e65-506"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合  。</span><span class="sxs-lookup"><span data-stu-id="05e65-506">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="05e65-507">由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。</span><span class="sxs-lookup"><span data-stu-id="05e65-507">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="05e65-508">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-508">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="05e65-509">在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。</span><span class="sxs-lookup"><span data-stu-id="05e65-509">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="05e65-510">忽略与参数不匹配的环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-510">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="05e65-511">在显式提供的值会覆盖环境值的情况下，也会忽略环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-511">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="05e65-512">在 URL 中将从左到右进行匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-512">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="05e65-513">显式提供但与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="05e65-513">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="05e65-514">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="05e65-514">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="05e65-515">环境值</span><span class="sxs-lookup"><span data-stu-id="05e65-515">Ambient Values</span></span>                     | <span data-ttu-id="05e65-516">显式值</span><span class="sxs-lookup"><span data-stu-id="05e65-516">Explicit Values</span></span>                        | <span data-ttu-id="05e65-517">结果</span><span class="sxs-lookup"><span data-stu-id="05e65-517">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="05e65-518">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-518">controller = "Home"</span></span>                | <span data-ttu-id="05e65-519">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-519">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="05e65-520">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-520">controller = "Home"</span></span>                | <span data-ttu-id="05e65-521">控制器 =“Order”，操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-521">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="05e65-522">控制器 = “Home”，颜色 = “Red”</span><span class="sxs-lookup"><span data-stu-id="05e65-522">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="05e65-523">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-523">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="05e65-524">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-524">controller = "Home"</span></span>                | <span data-ttu-id="05e65-525">操作 =“About”，颜色 =“Red”</span><span class="sxs-lookup"><span data-stu-id="05e65-525">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="05e65-526">如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：</span><span class="sxs-lookup"><span data-stu-id="05e65-526">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="05e65-527">当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。</span><span class="sxs-lookup"><span data-stu-id="05e65-527">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="05e65-528">复杂段</span><span class="sxs-lookup"><span data-stu-id="05e65-528">Complex segments</span></span>

<span data-ttu-id="05e65-529">复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="05e65-529">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="05e65-530">请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。</span><span class="sxs-lookup"><span data-stu-id="05e65-530">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="05e65-531">ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。</span><span class="sxs-lookup"><span data-stu-id="05e65-531">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

## <a name="configuring-endpoint-metadata"></a><span data-ttu-id="05e65-532">配置终结点元数据</span><span class="sxs-lookup"><span data-stu-id="05e65-532">Configuring endpoint metadata</span></span>

<span data-ttu-id="05e65-533">以下链接提供有关配置终结点元数据的信息：</span><span class="sxs-lookup"><span data-stu-id="05e65-533">The following links provide information on configuring endpoint metadata:</span></span>

* [<span data-ttu-id="05e65-534">通过终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="05e65-534">Enable Cors with endpoint routing</span></span>](xref:security/cors#enable-cors-with-endpoint-routing)
* <span data-ttu-id="05e65-535">使用自定义 `[MinimumAgeAuthorize]` 属性的 [IAuthorizationPolicyProvider 示例](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)</span><span class="sxs-lookup"><span data-stu-id="05e65-535">[IAuthorizationPolicyProvider sample](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) using a custom `[MinimumAgeAuthorize]` attribute</span></span>
* <span data-ttu-id="05e65-536">[使用 [Authorize] 属性测试身份验证](xref:security/authentication/identity#test-identity)</span><span class="sxs-lookup"><span data-stu-id="05e65-536">[Test authentication with the [Authorize] attribute](xref:security/authentication/identity#test-identity)</span></span>
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* <span data-ttu-id="05e65-537">[使用 [Authorize] 属性选择方案](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="05e65-537">[Selecting the scheme with the [Authorize] attribute](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span></span>
* <span data-ttu-id="05e65-538">[使用 [Authorize] 属性应用策略](xref:security/authorization/policies#applying-policies-to-mvc-controllers)</span><span class="sxs-lookup"><span data-stu-id="05e65-538">[Applying policies using the [Authorize] attribute](xref:security/authorization/policies#applying-policies-to-mvc-controllers)</span></span>
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a><span data-ttu-id="05e65-539">路由中与 RequireHost 匹配的主机</span><span class="sxs-lookup"><span data-stu-id="05e65-539">Host matching in routes with RequireHost</span></span>

<span data-ttu-id="05e65-540">`RequireHost` 将约束应用于需要指定主机的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-540">`RequireHost` applies a constraint to the route which requires the specified host.</span></span> <span data-ttu-id="05e65-541">`RequireHost` 或 `[Host]` 参数可以是：</span><span class="sxs-lookup"><span data-stu-id="05e65-541">The `RequireHost` or `[Host]` parameter can be:</span></span>

* <span data-ttu-id="05e65-542">主机：`www.domain.com`（匹配任何端口的 `www.domain.com`）</span><span class="sxs-lookup"><span data-stu-id="05e65-542">Host: `www.domain.com` (matches `www.domain.com` with any port)</span></span>
* <span data-ttu-id="05e65-543">带有通配符的主机：`*.domain.com`（匹配任何端口上的 `www.domain.com`、`subdomain.domain.com` 或 `www.subdomain.domain.com`）</span><span class="sxs-lookup"><span data-stu-id="05e65-543">Host with wildcard: `*.domain.com` (matches `www.domain.com`, `subdomain.domain.com`, or `www.subdomain.domain.com` on any port)</span></span>
* <span data-ttu-id="05e65-544">端口：`*:5000`（与任何主机的端口 5000 匹配）</span><span class="sxs-lookup"><span data-stu-id="05e65-544">Port: `*:5000` (matches port 5000 with any host)</span></span>
* <span data-ttu-id="05e65-545">主机和端口：`www.domain.com:5000`、`*.domain.com:5000`（匹配主机和端口）</span><span class="sxs-lookup"><span data-stu-id="05e65-545">Host and port: `www.domain.com:5000`, `*.domain.com:5000` (matches host and port)</span></span>

<span data-ttu-id="05e65-546">可以使用 `RequireHost` 或 `[Host]` 指定多个参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-546">Multiple parameters can be specified using `RequireHost` or `[Host]`.</span></span> <span data-ttu-id="05e65-547">约束将匹配对任何参数有效的主机。</span><span class="sxs-lookup"><span data-stu-id="05e65-547">The constraint will match hosts valid for any of the parameters.</span></span> <span data-ttu-id="05e65-548">例如，`[Host("domain.com", "*.domain.com")]` 将匹配 `domain.com`、`www.domain.com` 或 `subdomain.domain.com`。</span><span class="sxs-lookup"><span data-stu-id="05e65-548">For example, `[Host("domain.com", "*.domain.com")]` will match `domain.com`, `www.domain.com`, or `subdomain.domain.com`.</span></span>

<span data-ttu-id="05e65-549">以下代码使用 `RequireHost` 来要求路由上的指定主机：</span><span class="sxs-lookup"><span data-stu-id="05e65-549">The following code uses `RequireHost` to require the specified host on the route:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/", context => context.Response.WriteAsync("Hi Contoso!"))
            .RequireHost("contoso.com");
        endpoints.MapGet("/", context => context.Response.WriteAsync("Hi AdventureWorks!"))
            .RequireHost("adventure-works.com");
        endpoints.MapHealthChecks("/healthz").RequireHost("*:8080");
    });
}
```

<span data-ttu-id="05e65-550">以下代码使用 `[Host]` 属性来要求控制器上的指定主机：</span><span class="sxs-lookup"><span data-stu-id="05e65-550">The following code uses the `[Host]` attribute to require the specified host on the controller:</span></span>

```csharp
[Host("contoso.com", "adventure-works.com")]
public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;

    public HomeController(ILogger<HomeController> logger)
    {
        _logger = logger;
    }

    public IActionResult Index()
    {
        return View();
    }

    [Host("example.com:8080")]
    public IActionResult Privacy()
    {
        return View();
    }

}
```

<span data-ttu-id="05e65-551">当 `[Host]` 属性同时应用于控制器和操作方法时：</span><span class="sxs-lookup"><span data-stu-id="05e65-551">When the `[Host]` attribute is applied to both the controller and action method:</span></span>

* <span data-ttu-id="05e65-552">使用操作上的属性。</span><span class="sxs-lookup"><span data-stu-id="05e65-552">The attribute on the action is used.</span></span>
* <span data-ttu-id="05e65-553">忽略控制器属性。</span><span class="sxs-lookup"><span data-stu-id="05e65-553">The controller attribute is ignored.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="05e65-554">路由负责将请求 URI 映射到终结点并向这些终结点调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-554">Routing is responsible for mapping request URIs to endpoints and dispatching incoming requests to those endpoints.</span></span> <span data-ttu-id="05e65-555">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-555">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="05e65-556">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-556">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="05e65-557">通过使用应用中的路由信息，路由还能生成映射到终结点的 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-557">Using route information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="05e65-558">要在 ASP.NET Core 2.2 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：</span><span class="sxs-lookup"><span data-stu-id="05e65-558">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="05e65-559"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 选项确定路由是应在内部使用 ASP.NET Core 2.1 或更早版本的基于终结点的逻辑还是使用其基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的逻辑。</span><span class="sxs-lookup"><span data-stu-id="05e65-559">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="05e65-560">兼容性版本设置为 2.2 或更高版本时，默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="05e65-560">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="05e65-561">将值设置为 `false` 以使用先前的路由逻辑：</span><span class="sxs-lookup"><span data-stu-id="05e65-561">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="05e65-562">有关基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由的详细信息，请参阅本主题的 [ASP.NET Core 2.1 版本](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)。</span><span class="sxs-lookup"><span data-stu-id="05e65-562">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="05e65-563">本文档介绍较低级别的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-563">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="05e65-564">有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="05e65-564">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="05e65-565">有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-565">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="05e65-566">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="05e65-566">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="05e65-567">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="05e65-567">Routing basics</span></span>

<span data-ttu-id="05e65-568">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="05e65-568">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="05e65-569">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="05e65-569">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="05e65-570">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="05e65-570">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="05e65-571">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="05e65-571">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="05e65-572">开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-572">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="05e65-573">Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="05e65-573">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="05e65-574">也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-574">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="05e65-575">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="05e65-575">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="05e65-576">Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源  。</span><span class="sxs-lookup"><span data-stu-id="05e65-576">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="05e65-577">还可以使用其他约定来自定义 Razor Pages 路由行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-577">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="05e65-578">有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-578">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="05e65-579">借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。</span><span class="sxs-lookup"><span data-stu-id="05e65-579">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="05e65-580">此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-580">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="05e65-581">路由使用“终结点”(`Endpoint`) 来表示应用中的逻辑终结点  。</span><span class="sxs-lookup"><span data-stu-id="05e65-581">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="05e65-582">终结点定义用于处理请求的委托和任意元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="05e65-582">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="05e65-583">元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-583">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="05e65-584">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="05e65-584">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="05e65-585">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-585">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="05e65-586">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-586">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="05e65-587"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="05e65-587"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="05e65-588">应用模型（如 MVC/Razor Pages）注册其所有终结点，这些终结点具有可预测的路由方案实现。</span><span class="sxs-lookup"><span data-stu-id="05e65-588">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="05e65-589">路由实现会在中间件管道中任何所需位置制定路由决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-589">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="05e65-590">路由中间件之后出现的中间件可以检查路由中间件针对给定请求 URI 的终结点决策结果。</span><span class="sxs-lookup"><span data-stu-id="05e65-590">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="05e65-591">可以在中间件管道中的任何位置枚举应用中的所有终结点。</span><span class="sxs-lookup"><span data-stu-id="05e65-591">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="05e65-592">应用可根据终结点信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="05e65-592">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="05e65-593">URL 生成是基于支持任意可扩展性的地址：</span><span class="sxs-lookup"><span data-stu-id="05e65-593">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="05e65-594">可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在任意位置解析链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) 以生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-594">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="05e65-595">如果无法通过 DI 获得链接生成器 API，则 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 会提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-595">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="05e65-596">在 ASP.NET Core 2.2 中发布终结点路由后，终结点链接的适用范围限制为 MVC/Razor Pages 操作和页面。</span><span class="sxs-lookup"><span data-stu-id="05e65-596">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="05e65-597">将计划在未来发布的版本中扩展终结点链接的功能。</span><span class="sxs-lookup"><span data-stu-id="05e65-597">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

<span data-ttu-id="05e65-598">路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。</span><span class="sxs-lookup"><span data-stu-id="05e65-598">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="05e65-599">[ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-599">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="05e65-600">要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-600">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="05e65-601">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-601">URL matching</span></span>

<span data-ttu-id="05e65-602">URL 匹配是路由向终结点调度传入请求的过程  。</span><span class="sxs-lookup"><span data-stu-id="05e65-602">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="05e65-603">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="05e65-603">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="05e65-604">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="05e65-604">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="05e65-605">终结点路由中的路由系统负责所有的调度决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-605">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="05e65-606">由于中间件是基于所选终结点来应用策略，因此任何可能影响调度或安全策略应用的决策都应在路由系统内部制定，这一点很重要。</span><span class="sxs-lookup"><span data-stu-id="05e65-606">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="05e65-607">执行终结点委托时，将根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-607">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="05e65-608">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典  。</span><span class="sxs-lookup"><span data-stu-id="05e65-608">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="05e65-609">这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-609">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="05e65-610">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。</span><span class="sxs-lookup"><span data-stu-id="05e65-610">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="05e65-611">提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-611"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="05e65-612">这些值是开发者定义的，不会影响通过任何方式路由的行为  。</span><span class="sxs-lookup"><span data-stu-id="05e65-612">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="05e65-613">此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。</span><span class="sxs-lookup"><span data-stu-id="05e65-613">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="05e65-614">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。</span><span class="sxs-lookup"><span data-stu-id="05e65-614">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="05e65-615">路由可以相互嵌套。</span><span class="sxs-lookup"><span data-stu-id="05e65-615">Routes can be nested inside of one another.</span></span> <span data-ttu-id="05e65-616"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-616">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="05e65-617">通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-617">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="05e65-618"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="05e65-618">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="05e65-619">使用 LinkGenerator 的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="05e65-619">URL generation with LinkGenerator</span></span>

<span data-ttu-id="05e65-620">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="05e65-620">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="05e65-621">这需要考虑终结点与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="05e65-621">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="05e65-622">终结点路由包含链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)。</span><span class="sxs-lookup"><span data-stu-id="05e65-622">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="05e65-623"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 是可从 DI 检索的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="05e65-623"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from DI.</span></span> <span data-ttu-id="05e65-624">该 API 可在执行请求的上下文之外使用。</span><span class="sxs-lookup"><span data-stu-id="05e65-624">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="05e65-625">MVC 的 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 和依赖 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的方案（如 [Tag Helpers](xref:mvc/views/tag-helpers/intro)、HTML Helpers 和 [Action Results](xref:mvc/controllers/actions)）使用链接生成器提供链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="05e65-625">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="05e65-626">链接生成器基于“地址”和“地址方案”概念   。</span><span class="sxs-lookup"><span data-stu-id="05e65-626">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="05e65-627">地址方案是确定哪些终结点用于链接生成的方式。</span><span class="sxs-lookup"><span data-stu-id="05e65-627">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="05e65-628">例如，许多用户熟悉的来自 MVC/Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。</span><span class="sxs-lookup"><span data-stu-id="05e65-628">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="05e65-629">链接生成器可以通过以下扩展方法链接到 MVC/Razor Pages 操作和页面：</span><span class="sxs-lookup"><span data-stu-id="05e65-629">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="05e65-630">这些方法的重载接受包含 `HttpContext` 的参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-630">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="05e65-631">这些方法在功能上等同于 `Url.Action` 和 `Url.Page`，但提供了更大的灵活性和更多的选项。</span><span class="sxs-lookup"><span data-stu-id="05e65-631">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="05e65-632">`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-632">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="05e65-633">`GetUri*` 方法始终生成包含方案和主机的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-633">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="05e65-634">接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-634">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="05e65-635">除非重写，否则将使用来自执行请求的环境路由值、URL 基本路径、方案和主机。</span><span class="sxs-lookup"><span data-stu-id="05e65-635">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="05e65-636">使用地址调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。</span><span class="sxs-lookup"><span data-stu-id="05e65-636"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="05e65-637">生成 URI 的过程分两步进行：</span><span class="sxs-lookup"><span data-stu-id="05e65-637">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="05e65-638">将地址绑定到与地址匹配的终结点列表。</span><span class="sxs-lookup"><span data-stu-id="05e65-638">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="05e65-639">计算每个终结点的 `RoutePattern`，直到找到与提供的值匹配的路由模式。</span><span class="sxs-lookup"><span data-stu-id="05e65-639">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="05e65-640">输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。</span><span class="sxs-lookup"><span data-stu-id="05e65-640">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="05e65-641">对任何类型的地址，<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法均支持标准链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="05e65-641">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="05e65-642">使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作。</span><span class="sxs-lookup"><span data-stu-id="05e65-642">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="05e65-643">扩展方法</span><span class="sxs-lookup"><span data-stu-id="05e65-643">Extension Method</span></span>   | <span data-ttu-id="05e65-644">说明</span><span class="sxs-lookup"><span data-stu-id="05e65-644">Description</span></span>                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="05e65-645">根据提供的值生成具有绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-645">Generates a URI with an absolute path based on the provided values.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="05e65-646">根据提供的值生成绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-646">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="05e65-647">请注意有关调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列含义：</span><span class="sxs-lookup"><span data-stu-id="05e65-647">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="05e65-648">对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-648">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="05e65-649">如果未验证传入请求的 `Host`标头，则可能以视图/页面中 URI 的形式将不受信任的请求输入发送回客户端。</span><span class="sxs-lookup"><span data-stu-id="05e65-649">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="05e65-650">建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="05e65-650">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="05e65-651">在中间件中将 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="05e65-651">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="05e65-652">`Map*` 会更改执行请求的基路径，这会影响链接生成的输出。</span><span class="sxs-lookup"><span data-stu-id="05e65-652">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="05e65-653">所有 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允许指定基路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-653">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="05e65-654">始终指定一个空的基路径来撤消 `Map*` 对链接生成的影响。</span><span class="sxs-lookup"><span data-stu-id="05e65-654">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="05e65-655">与早期版本路由的差异</span><span class="sxs-lookup"><span data-stu-id="05e65-655">Differences from earlier versions of routing</span></span>

<span data-ttu-id="05e65-656">ASP.NET Core 2.2 或更高版本中的终结点路由与 ASP.NET Core 中早期版本的路由之间存在一些差异：</span><span class="sxs-lookup"><span data-stu-id="05e65-656">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="05e65-657">终结点路由系统不支持基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的可扩展性，包括从 <xref:Microsoft.AspNetCore.Routing.Route> 继承。</span><span class="sxs-lookup"><span data-stu-id="05e65-657">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="05e65-658">终结点路由不支持 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。</span><span class="sxs-lookup"><span data-stu-id="05e65-658">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="05e65-659">使用 2.1 [兼容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 以继续使用兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="05e65-659">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="05e65-660">在使用传统路由时，终结点路由对生成的 URI 的大小写具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-660">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="05e65-661">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="05e65-661">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="05e65-662">假设使用以下路由生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="05e65-662">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="05e65-663">如果使用基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由，此代码生成 URI `/blog/ReadPost/17`，该 URI 遵循所提供路由值的大小写。</span><span class="sxs-lookup"><span data-stu-id="05e65-663">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="05e65-664">ASP.NET Core 2.2 或更高版本中的终结点路由生成 `/Blog/ReadPost/17`（“Blog”大写）。</span><span class="sxs-lookup"><span data-stu-id="05e65-664">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="05e65-665">终结点路由提供 `IOutboundParameterTransformer` 接口，可用于在全局范围自定义此行为或应用不同的约定来映射 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-665">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="05e65-666">有关详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-666">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="05e65-667">在试图链接到不存在的控制器/操作或页面时，MVC/Razor Pages 通过传统路由执行的链接生成，其操作具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-667">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="05e65-668">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="05e65-668">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="05e65-669">假设使用以下路由通过默认模板生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="05e65-669">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="05e65-670">如果使用基于 `IRouter` 的路由，即使 `BlogController` 不存在或没有 `ReadPost` 操作方法，结果也始终为 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="05e65-670">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="05e65-671">正如所料，如果操作方法存在，ASP.NET Core 2.2 或更高版本中的终结点路由会生成 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="05e65-671">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="05e65-672">但是，如果操作不存在，终结点路由会生成空字符串。 </span><span class="sxs-lookup"><span data-stu-id="05e65-672">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="05e65-673">从概念上讲，如果操作不存在，终结点路由不会假定终结点存在。</span><span class="sxs-lookup"><span data-stu-id="05e65-673">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="05e65-674">与终结点路由一起使用时，链接生成环境值失效算法的行为会有所不同  。</span><span class="sxs-lookup"><span data-stu-id="05e65-674">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="05e65-675">*环境值失效*是一种算法，用于决定当前正在执行的请求（环境值）中的哪些路由值可用于链接生成操作。</span><span class="sxs-lookup"><span data-stu-id="05e65-675">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="05e65-676">链接到不同操作时，传统路由会使额外的路由值失效。</span><span class="sxs-lookup"><span data-stu-id="05e65-676">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="05e65-677">ASP.NET Core 2.2 之前的版本中，属性路由不具有此行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-677">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="05e65-678">在 ASP.NET Core 的早期版本中，如果有另一个操作使用同一路由参数名称，则该操作的链接会导致发生链接生成错误。</span><span class="sxs-lookup"><span data-stu-id="05e65-678">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="05e65-679">在 ASP.NET Core 2.2 或更高版本中，链接到另一个操作时，这两种路由形式都会使值失效。</span><span class="sxs-lookup"><span data-stu-id="05e65-679">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="05e65-680">请考虑 ASP.NET Core2.1 或更高版本中的以下示例。</span><span class="sxs-lookup"><span data-stu-id="05e65-680">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="05e65-681">链接到另一个操作（或另一页面）时，路由值可能会按非预期的方式被重用。</span><span class="sxs-lookup"><span data-stu-id="05e65-681">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="05e65-682">在 /Pages/Store/Product.cshtml 中  ：</span><span class="sxs-lookup"><span data-stu-id="05e65-682">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="05e65-683">在 /Pages/Login.cshtml 中  ：</span><span class="sxs-lookup"><span data-stu-id="05e65-683">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="05e65-684">如果 ASP.NET Core 2.1 或更早版本中的 URI 为 `/Store/Product/18`，则由 `@Url.Page("/Login")` 在 Store/Info 页面上生成的链接为 `/Login/18`。</span><span class="sxs-lookup"><span data-stu-id="05e65-684">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="05e65-685">即使链接目标完全是应用的其他部分，仍会重用 `id` 值 18。</span><span class="sxs-lookup"><span data-stu-id="05e65-685">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="05e65-686">`/Login` 页面上下文中的 `id` 路由值可能是用户 ID 值，而非存储产品 ID 值。</span><span class="sxs-lookup"><span data-stu-id="05e65-686">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="05e65-687">在 ASP.NET Core 2.2 或更高版本的终结点路由中，结果为 `/Login`。</span><span class="sxs-lookup"><span data-stu-id="05e65-687">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="05e65-688">当链接的目标是另一个操作或页面时，不会重复使用环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-688">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="05e65-689">往返路由参数语法：使用双星号 (`**`) catch-all 参数语法时，不对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="05e65-689">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="05e65-690">在链接生成期间，路由系统对双星号 (`**`) catch-all 参数（例如，`{**myparametername}`）中捕获的除正斜杠外的值进行编码。</span><span class="sxs-lookup"><span data-stu-id="05e65-690">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="05e65-691">在 ASP.NET Core 2.2 或更高版本中，基于 `IRouter` 的路由支持双星号 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-691">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="05e65-692">ASP.NET Core (`{*myparametername}`) 早期版本中的单星号 catch-all 参数语法仍然受支持，并对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="05e65-692">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="05e65-693">路由</span><span class="sxs-lookup"><span data-stu-id="05e65-693">Route</span></span>              | <span data-ttu-id="05e65-694">链接生成方式为</span><span class="sxs-lookup"><span data-stu-id="05e65-694">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | <span data-ttu-id="05e65-695">`/search/admin%2Fproducts`（对正斜杠进行编码）</span><span class="sxs-lookup"><span data-stu-id="05e65-695">`/search/admin%2Fproducts` (the forward slash is encoded)</span></span>             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a><span data-ttu-id="05e65-696">中间件示例</span><span class="sxs-lookup"><span data-stu-id="05e65-696">Middleware example</span></span>

<span data-ttu-id="05e65-697">在以下示例中，中间件使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 创建列出存储产品的操作方法的链接。</span><span class="sxs-lookup"><span data-stu-id="05e65-697">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="05e65-698">应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器。</span><span class="sxs-lookup"><span data-stu-id="05e65-698">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

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

### <a name="create-routes"></a><span data-ttu-id="05e65-699">创建路由</span><span class="sxs-lookup"><span data-stu-id="05e65-699">Create routes</span></span>

<span data-ttu-id="05e65-700">大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-700">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="05e65-701">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。</span><span class="sxs-lookup"><span data-stu-id="05e65-701">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="05e65-702"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-702"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="05e65-703"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-703"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="05e65-704">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="05e65-704">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="05e65-705">以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：</span><span class="sxs-lookup"><span data-stu-id="05e65-705">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="05e65-706">此模板与 URL 路径相匹配，并且提取路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-706">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="05e65-707">例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-707">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="05e65-708">路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的  。</span><span class="sxs-lookup"><span data-stu-id="05e65-708">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="05e65-709">路由参数已命名。</span><span class="sxs-lookup"><span data-stu-id="05e65-709">Route parameters are named.</span></span> <span data-ttu-id="05e65-710">参数通过将参数名称括在大括号 `{ ... }` 中来定义。</span><span class="sxs-lookup"><span data-stu-id="05e65-710">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="05e65-711">上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-711">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="05e65-712">这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="05e65-712">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="05e65-713">路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。</span><span class="sxs-lookup"><span data-stu-id="05e65-713">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="05e65-714">路由参数名称后面的问号 (`?`) 定义了可选参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-714">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="05e65-715">路由匹配时，具有默认值的路由参数始终会生成路由值  。</span><span class="sxs-lookup"><span data-stu-id="05e65-715">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="05e65-716">如果没有相应的 URL 路径段，则可选参数不会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-716">Optional parameters don't produce a route value if there was no corresponding URL path segment.</span></span> <span data-ttu-id="05e65-717">有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-717">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="05e65-718">在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="05e65-718">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="05e65-719">此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-719">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="05e65-720">路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。</span><span class="sxs-lookup"><span data-stu-id="05e65-720">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="05e65-721">在此示例中，路由值 `id` 必须可转换为整数。</span><span class="sxs-lookup"><span data-stu-id="05e65-721">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="05e65-722">有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="05e65-722">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="05e65-723">其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-723">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="05e65-724">这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="05e65-724">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="05e65-725">以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：</span><span class="sxs-lookup"><span data-stu-id="05e65-725">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

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
> <span data-ttu-id="05e65-726">定义约束和默认值的内联语法对于简单路由很方便。</span><span class="sxs-lookup"><span data-stu-id="05e65-726">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="05e65-727">但是，存在内联语法不支持的方案，例如数据令牌。</span><span class="sxs-lookup"><span data-stu-id="05e65-727">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="05e65-728">以下示例演示了一些其他方案：</span><span class="sxs-lookup"><span data-stu-id="05e65-728">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="05e65-729">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-729">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="05e65-730">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-730">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="05e65-731">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-731">Default values can be specified in the route template.</span></span> <span data-ttu-id="05e65-732">根据路由参数名称前的双星号 (`**`) 外观，`article` 路由参数被定义为 catch-all  。</span><span class="sxs-lookup"><span data-stu-id="05e65-732">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="05e65-733">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-733">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="05e65-734">以下示例添加了路由约束和数据令牌：</span><span class="sxs-lookup"><span data-stu-id="05e65-734">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="05e65-735">上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-735">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="05e65-737">路由类 URL 生成</span><span class="sxs-lookup"><span data-stu-id="05e65-737">Route class URL generation</span></span>

<span data-ttu-id="05e65-738"><xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-738">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="05e65-739">从逻辑上来说，这是匹配 URL 路径的反向过程。</span><span class="sxs-lookup"><span data-stu-id="05e65-739">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="05e65-740">为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-740">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="05e65-741">将生成什么值？</span><span class="sxs-lookup"><span data-stu-id="05e65-741">What values would be produced?</span></span> <span data-ttu-id="05e65-742">这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。</span><span class="sxs-lookup"><span data-stu-id="05e65-742">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="05e65-743">以下示例使用常规 ASP.NET Core MVC 默认路由：</span><span class="sxs-lookup"><span data-stu-id="05e65-743">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="05e65-744">使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="05e65-744">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="05e65-745">路由值将替换为相应的路由参数，以形成 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-745">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="05e65-746">由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-746">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="05e65-747">使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="05e65-747">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="05e65-748">提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。</span><span class="sxs-lookup"><span data-stu-id="05e65-748">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="05e65-749">已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-749">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="05e65-750">使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-750">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="05e65-751">有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-751">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="05e65-752">使用路由中间件</span><span class="sxs-lookup"><span data-stu-id="05e65-752">Use Routing Middleware</span></span>

<span data-ttu-id="05e65-753">引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="05e65-753">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="05e65-754">将路由添加到 `Startup.ConfigureServices` 中的服务容器：</span><span class="sxs-lookup"><span data-stu-id="05e65-754">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="05e65-755">必须在 `Startup.Configure` 方法中配置路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-755">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="05e65-756">该示例应用使用以下 API：</span><span class="sxs-lookup"><span data-stu-id="05e65-756">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="05e65-757"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; 仅匹配 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-757"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="05e65-758">下表显示了具有给定 URI 的响应。</span><span class="sxs-lookup"><span data-stu-id="05e65-758">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="05e65-759">URI</span><span class="sxs-lookup"><span data-stu-id="05e65-759">URI</span></span>                    | <span data-ttu-id="05e65-760">响应</span><span class="sxs-lookup"><span data-stu-id="05e65-760">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="05e65-761">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-761">Hello!</span></span> <span data-ttu-id="05e65-762">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="05e65-762">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="05e65-763">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-763">Hello!</span></span> <span data-ttu-id="05e65-764">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="05e65-764">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="05e65-765">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-765">Hello!</span></span> <span data-ttu-id="05e65-766">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="05e65-766">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="05e65-767">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-767">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="05e65-768">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="05e65-768">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="05e65-769">请求失败，仅匹配 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="05e65-769">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="05e65-770">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-770">The request falls through, no match.</span></span>              |

<span data-ttu-id="05e65-771">框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="05e65-771">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="05e65-772">`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="05e65-772">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="05e65-773">有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="05e65-773">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="05e65-774">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="05e65-774">Route template reference</span></span>

<span data-ttu-id="05e65-775">如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数  。</span><span class="sxs-lookup"><span data-stu-id="05e65-775">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="05e65-776">可在路由段中定义多个路由参数，但必须由文本值隔开。</span><span class="sxs-lookup"><span data-stu-id="05e65-776">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="05e65-777">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="05e65-777">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="05e65-778">这些路由参数必须具有名称，且可能指定了其他属性。</span><span class="sxs-lookup"><span data-stu-id="05e65-778">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="05e65-779">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="05e65-779">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="05e65-780">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="05e65-780">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="05e65-781">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="05e65-781">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="05e65-782">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="05e65-782">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="05e65-783">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="05e65-783">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="05e65-784">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="05e65-784">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="05e65-785">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。</span><span class="sxs-lookup"><span data-stu-id="05e65-785">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="05e65-786">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="05e65-786">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="05e65-787">可以使用星号 (`*`) 或双星号 (`**`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-787">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="05e65-788">这些称为 catch-all 参数  。</span><span class="sxs-lookup"><span data-stu-id="05e65-788">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="05e65-789">例如，`blog/{**slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-789">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="05e65-790">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-790">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="05e65-791">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="05e65-791">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="05e65-792">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="05e65-792">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="05e65-793">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="05e65-793">Note the escaped forward slash.</span></span> <span data-ttu-id="05e65-794">要往返路径分隔符，请使用 `**` 路由参数前缀。</span><span class="sxs-lookup"><span data-stu-id="05e65-794">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="05e65-795">`{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="05e65-795">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="05e65-796">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值  。</span><span class="sxs-lookup"><span data-stu-id="05e65-796">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="05e65-797">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-797">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="05e65-798">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-798">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="05e65-799">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="05e65-799">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="05e65-800">可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。</span><span class="sxs-lookup"><span data-stu-id="05e65-800">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="05e65-801">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-801">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="05e65-802">在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束  。</span><span class="sxs-lookup"><span data-stu-id="05e65-802">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="05e65-803">如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="05e65-803">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="05e65-804">通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-804">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="05e65-805">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-805">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="05e65-806">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-806">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="05e65-807">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-807">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="05e65-808">路由参数还可以具有参数转换器，用于在生成链接以及将操作和页面匹配到 URI 时转换参数的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-808">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="05e65-809">与约束类似，可以通过在路由参数名称后面添加冒号 (`:`) 和转换器名称，将参数变换器内联添加到路径参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-809">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="05e65-810">例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。</span><span class="sxs-lookup"><span data-stu-id="05e65-810">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="05e65-811">有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-811">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="05e65-812">下表演示了示例路由模板及其行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-812">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="05e65-813">路由模板</span><span class="sxs-lookup"><span data-stu-id="05e65-813">Route Template</span></span>                           | <span data-ttu-id="05e65-814">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="05e65-814">Example Matching URI</span></span>    | <span data-ttu-id="05e65-815">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="05e65-815">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="05e65-816">仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="05e65-816">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="05e65-817">匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="05e65-817">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="05e65-818">匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="05e65-818">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="05e65-819">映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="05e65-819">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="05e65-820">映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。</span><span class="sxs-lookup"><span data-stu-id="05e65-820">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="05e65-821">映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。</span><span class="sxs-lookup"><span data-stu-id="05e65-821">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="05e65-822">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-822">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="05e65-823">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-823">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="05e65-824">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-824">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="05e65-825">保留的路由名称</span><span class="sxs-lookup"><span data-stu-id="05e65-825">Reserved routing names</span></span>

<span data-ttu-id="05e65-826">以下关键字是保留的名称，它们不能用作路由名称或参数：</span><span class="sxs-lookup"><span data-stu-id="05e65-826">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="05e65-827">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="05e65-827">Route constraint reference</span></span>

<span data-ttu-id="05e65-828">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-828">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="05e65-829">路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。</span><span class="sxs-lookup"><span data-stu-id="05e65-829">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="05e65-830">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-830">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="05e65-831">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-831">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="05e65-832">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="05e65-832">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="05e65-833">请勿将约束用于“输入验证”  。</span><span class="sxs-lookup"><span data-stu-id="05e65-833">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="05e65-834">如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求”    。</span><span class="sxs-lookup"><span data-stu-id="05e65-834">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="05e65-835">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入  。</span><span class="sxs-lookup"><span data-stu-id="05e65-835">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="05e65-836">下表演示示例路由约束及其预期行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-836">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="05e65-837">约束</span><span class="sxs-lookup"><span data-stu-id="05e65-837">constraint</span></span> | <span data-ttu-id="05e65-838">示例</span><span class="sxs-lookup"><span data-stu-id="05e65-838">Example</span></span> | <span data-ttu-id="05e65-839">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="05e65-839">Example Matches</span></span> | <span data-ttu-id="05e65-840">说明</span><span class="sxs-lookup"><span data-stu-id="05e65-840">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="05e65-841">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="05e65-841">`123456789`, `-123456789`</span></span> | <span data-ttu-id="05e65-842">匹配任何整数</span><span class="sxs-lookup"><span data-stu-id="05e65-842">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="05e65-843">`true`， `FALSE`</span><span class="sxs-lookup"><span data-stu-id="05e65-843">`true`, `FALSE`</span></span> | <span data-ttu-id="05e65-844">匹配 `true`或 `false`（区分大小写）</span><span class="sxs-lookup"><span data-stu-id="05e65-844">Matches `true` or `false` (case-insensitive)</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="05e65-845">`2016-12-31`， `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="05e65-845">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="05e65-846">匹配有效的 `DateTime` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-846">Matches a valid `DateTime` value (in the invariant culture - see warning)</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="05e65-847">`49.99`， `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="05e65-847">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="05e65-848">匹配有效的 `decimal` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-848">Matches a valid `decimal` value (in the invariant culture - see warning)</span></span> |
| `double` | `{weight:double}` | <span data-ttu-id="05e65-849">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="05e65-849">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="05e65-850">匹配有效的 `double` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-850">Matches a valid `double` value (in the invariant culture - see warning)</span></span> |
| `float` | `{weight:float}` | <span data-ttu-id="05e65-851">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="05e65-851">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="05e65-852">匹配有效的 `float` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-852">Matches a valid `float` value (in the invariant culture - see warning)</span></span> |
| `guid` | `{id:guid}` | <span data-ttu-id="05e65-853">`CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="05e65-853">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="05e65-854">匹配有效的 `Guid` 值</span><span class="sxs-lookup"><span data-stu-id="05e65-854">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="05e65-855">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="05e65-855">`123456789`, `-123456789`</span></span> | <span data-ttu-id="05e65-856">匹配有效的 `long` 值</span><span class="sxs-lookup"><span data-stu-id="05e65-856">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="05e65-857">字符串必须至少为 4 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-857">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="05e65-858">字符串不得超过 8 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-858">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="05e65-859">字符串必须正好为 12 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-859">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="05e65-860">字符串必须至少为 8 个字符，且不得超过 16 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-860">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="05e65-861">整数值必须至少为 18</span><span class="sxs-lookup"><span data-stu-id="05e65-861">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="05e65-862">整数值不得超过 120</span><span class="sxs-lookup"><span data-stu-id="05e65-862">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="05e65-863">整数值必须至少为 18，且不得超过 120</span><span class="sxs-lookup"><span data-stu-id="05e65-863">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="05e65-864">字符串必须由一个或多个字母字符（`a`-`z`，区分大小写）组成</span><span class="sxs-lookup"><span data-stu-id="05e65-864">String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive)</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="05e65-865">字符串必须匹配正则表达式（参见有关定义正则表达式的提示）</span><span class="sxs-lookup"><span data-stu-id="05e65-865">String must match the regular expression (see tips about defining a regular expression)</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="05e65-866">用于强制在 URL 生成过程中存在非参数值</span><span class="sxs-lookup"><span data-stu-id="05e65-866">Used to enforce that a non-parameter value is present during URL generation</span></span> |

<span data-ttu-id="05e65-867">可向单个参数应用多个由冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-867">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="05e65-868">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="05e65-868">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="05e65-869">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="05e65-869">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="05e65-870">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="05e65-870">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="05e65-871">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-871">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="05e65-872">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-872">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="05e65-873">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="05e65-873">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="05e65-874">正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-874">Regular expressions</span></span>

<span data-ttu-id="05e65-875">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="05e65-875">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="05e65-876">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-876">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="05e65-877">正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="05e65-877">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="05e65-878">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="05e65-878">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="05e65-879">要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。</span><span class="sxs-lookup"><span data-stu-id="05e65-879">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="05e65-880">要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="05e65-880">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="05e65-881">下表展示了正则表达式和转义版本。</span><span class="sxs-lookup"><span data-stu-id="05e65-881">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="05e65-882">正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-882">Regular Expression</span></span>    | <span data-ttu-id="05e65-883">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-883">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="05e65-884">路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="05e65-884">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="05e65-885">表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="05e65-885">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="05e65-886">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="05e65-886">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="05e65-887">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="05e65-887">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="05e65-888">下表提供了示例并说明了它们匹配或匹配失败的原因。</span><span class="sxs-lookup"><span data-stu-id="05e65-888">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="05e65-889">表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-889">Expression</span></span>   | <span data-ttu-id="05e65-890">String</span><span class="sxs-lookup"><span data-stu-id="05e65-890">String</span></span>    | <span data-ttu-id="05e65-891">匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-891">Match</span></span> | <span data-ttu-id="05e65-892">注释</span><span class="sxs-lookup"><span data-stu-id="05e65-892">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="05e65-893">hello</span><span class="sxs-lookup"><span data-stu-id="05e65-893">hello</span></span>     | <span data-ttu-id="05e65-894">是</span><span class="sxs-lookup"><span data-stu-id="05e65-894">Yes</span></span>   | <span data-ttu-id="05e65-895">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-895">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="05e65-896">123abc456</span><span class="sxs-lookup"><span data-stu-id="05e65-896">123abc456</span></span> | <span data-ttu-id="05e65-897">是</span><span class="sxs-lookup"><span data-stu-id="05e65-897">Yes</span></span>   | <span data-ttu-id="05e65-898">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-898">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="05e65-899">mz</span><span class="sxs-lookup"><span data-stu-id="05e65-899">mz</span></span>        | <span data-ttu-id="05e65-900">是</span><span class="sxs-lookup"><span data-stu-id="05e65-900">Yes</span></span>   | <span data-ttu-id="05e65-901">匹配表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-901">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="05e65-902">MZ</span><span class="sxs-lookup"><span data-stu-id="05e65-902">MZ</span></span>        | <span data-ttu-id="05e65-903">是</span><span class="sxs-lookup"><span data-stu-id="05e65-903">Yes</span></span>   | <span data-ttu-id="05e65-904">不区分大小写</span><span class="sxs-lookup"><span data-stu-id="05e65-904">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="05e65-905">hello</span><span class="sxs-lookup"><span data-stu-id="05e65-905">hello</span></span>     | <span data-ttu-id="05e65-906">No</span><span class="sxs-lookup"><span data-stu-id="05e65-906">No</span></span>    | <span data-ttu-id="05e65-907">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="05e65-907">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="05e65-908">123abc456</span><span class="sxs-lookup"><span data-stu-id="05e65-908">123abc456</span></span> | <span data-ttu-id="05e65-909">No</span><span class="sxs-lookup"><span data-stu-id="05e65-909">No</span></span>    | <span data-ttu-id="05e65-910">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="05e65-910">See `^` and `$` above</span></span> |

<span data-ttu-id="05e65-911">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="05e65-911">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="05e65-912">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="05e65-912">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="05e65-913">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="05e65-913">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="05e65-914">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="05e65-914">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="05e65-915">已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="05e65-915">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="05e65-916">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="05e65-916">Custom Route Constraints</span></span>

<span data-ttu-id="05e65-917">除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-917">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="05e65-918"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="05e65-918">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="05e65-919">若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="05e65-919">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="05e65-920"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="05e65-920">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="05e65-921">应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="05e65-921">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="05e65-922">例如:</span><span class="sxs-lookup"><span data-stu-id="05e65-922">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="05e65-923">然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-923">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="05e65-924">例如:</span><span class="sxs-lookup"><span data-stu-id="05e65-924">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a><span data-ttu-id="05e65-925">参数转换器参考</span><span class="sxs-lookup"><span data-stu-id="05e65-925">Parameter transformer reference</span></span>

<span data-ttu-id="05e65-926">参数转换器：</span><span class="sxs-lookup"><span data-stu-id="05e65-926">Parameter transformers:</span></span>

* <span data-ttu-id="05e65-927">在为 <xref:Microsoft.AspNetCore.Routing.Route> 生成链接时执行。</span><span class="sxs-lookup"><span data-stu-id="05e65-927">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="05e65-928">实现 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。</span><span class="sxs-lookup"><span data-stu-id="05e65-928">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="05e65-929">使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-929">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="05e65-930">获取参数的路由值并将其转换为新的字符串值。</span><span class="sxs-lookup"><span data-stu-id="05e65-930">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="05e65-931">在生成的链接中使用转换后的值的结果。</span><span class="sxs-lookup"><span data-stu-id="05e65-931">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="05e65-932">例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="05e65-932">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="05e65-933">若要在路由模式中使用参数转换器，请先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：</span><span class="sxs-lookup"><span data-stu-id="05e65-933">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="05e65-934">框架使用参数转化器来转换进行终结点解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-934">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="05e65-935">例如，ASP.NET Core MVC 使用参数转换器来转换用于匹配 `area` `controller` `action` 和 `page` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-935">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="05e65-936">使用上述路由，操作 `SubscriptionManagementController.GetAll()` 与 URI `/subscription-management/get-all` 匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-936">With the preceding route, the action `SubscriptionManagementController.GetAll()` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="05e65-937">参数转换器不会更改用于生成链接的路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-937">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="05e65-938">例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="05e65-938">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="05e65-939">对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：</span><span class="sxs-lookup"><span data-stu-id="05e65-939">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="05e65-940">ASP.NET Core MVC 还具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="05e65-940">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="05e65-941">该约定将指定的参数转换器应用于应用中的所有属性路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-941">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="05e65-942">在替换属性路径令牌时，参数转换器将转换这些令牌。</span><span class="sxs-lookup"><span data-stu-id="05e65-942">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="05e65-943">有关详细信息，请参阅[使用参数转换器自定义标记替换](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="05e65-943">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="05e65-944">Razor Pages 具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="05e65-944">Razor Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="05e65-945">此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="05e65-945">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="05e65-946">参数转换器转换 Razor Pages.路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="05e65-946">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="05e65-947">有关详细信息，请参阅[使用参数转换器自定义页面路由](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="05e65-947">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

## <a name="url-generation-reference"></a><span data-ttu-id="05e65-948">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="05e65-948">URL generation reference</span></span>

<span data-ttu-id="05e65-949">以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。</span><span class="sxs-lookup"><span data-stu-id="05e65-949">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="05e65-950">上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="05e65-950">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="05e65-951">字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-951">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="05e65-952">有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。</span><span class="sxs-lookup"><span data-stu-id="05e65-952">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="05e65-953"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合  。</span><span class="sxs-lookup"><span data-stu-id="05e65-953">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="05e65-954">由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。</span><span class="sxs-lookup"><span data-stu-id="05e65-954">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="05e65-955">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-955">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="05e65-956">在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。</span><span class="sxs-lookup"><span data-stu-id="05e65-956">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="05e65-957">忽略与参数不匹配的环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-957">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="05e65-958">在显式提供的值会覆盖环境值的情况下，也会忽略环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-958">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="05e65-959">在 URL 中将从左到右进行匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-959">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="05e65-960">显式提供但与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="05e65-960">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="05e65-961">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="05e65-961">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="05e65-962">环境值</span><span class="sxs-lookup"><span data-stu-id="05e65-962">Ambient Values</span></span>                     | <span data-ttu-id="05e65-963">显式值</span><span class="sxs-lookup"><span data-stu-id="05e65-963">Explicit Values</span></span>                        | <span data-ttu-id="05e65-964">结果</span><span class="sxs-lookup"><span data-stu-id="05e65-964">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="05e65-965">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-965">controller = "Home"</span></span>                | <span data-ttu-id="05e65-966">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-966">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="05e65-967">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-967">controller = "Home"</span></span>                | <span data-ttu-id="05e65-968">控制器 =“Order”，操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-968">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="05e65-969">控制器 = “Home”，颜色 = “Red”</span><span class="sxs-lookup"><span data-stu-id="05e65-969">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="05e65-970">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-970">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="05e65-971">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-971">controller = "Home"</span></span>                | <span data-ttu-id="05e65-972">操作 =“About”，颜色 =“Red”</span><span class="sxs-lookup"><span data-stu-id="05e65-972">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="05e65-973">如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：</span><span class="sxs-lookup"><span data-stu-id="05e65-973">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="05e65-974">当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。</span><span class="sxs-lookup"><span data-stu-id="05e65-974">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="05e65-975">复杂段</span><span class="sxs-lookup"><span data-stu-id="05e65-975">Complex segments</span></span>

<span data-ttu-id="05e65-976">复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="05e65-976">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="05e65-977">请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。</span><span class="sxs-lookup"><span data-stu-id="05e65-977">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="05e65-978">ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。</span><span class="sxs-lookup"><span data-stu-id="05e65-978">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="05e65-979">路由负责将请求 URI 映射到路由处理程序和调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-979">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="05e65-980">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-980">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="05e65-981">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-981">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="05e65-982">如果使用应用中配置的路由，路由能生成映射到路由处理程序的 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-982">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

<span data-ttu-id="05e65-983">要在 ASP.NET Core 2.1 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：</span><span class="sxs-lookup"><span data-stu-id="05e65-983">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> <span data-ttu-id="05e65-984">本文档介绍较低级别的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-984">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="05e65-985">有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="05e65-985">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="05e65-986">有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-986">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="05e65-987">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="05e65-987">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="05e65-988">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="05e65-988">Routing basics</span></span>

<span data-ttu-id="05e65-989">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="05e65-989">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="05e65-990">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="05e65-990">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="05e65-991">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="05e65-991">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="05e65-992">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="05e65-992">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="05e65-993">开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-993">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="05e65-994">Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="05e65-994">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="05e65-995">也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-995">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="05e65-996">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="05e65-996">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="05e65-997">Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源  。</span><span class="sxs-lookup"><span data-stu-id="05e65-997">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="05e65-998">还可以使用其他约定来自定义 Razor Pages 路由行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-998">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="05e65-999">有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-999">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="05e65-1000">借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。</span><span class="sxs-lookup"><span data-stu-id="05e65-1000">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="05e65-1001">此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1001">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="05e65-1002">路由使用路由（<xref:Microsoft.AspNetCore.Routing.IRouter> 的实现）  ：</span><span class="sxs-lookup"><span data-stu-id="05e65-1002">Routing uses *routes* (implementations of <xref:Microsoft.AspNetCore.Routing.IRouter>) to:</span></span>

* <span data-ttu-id="05e65-1003">将传入请求映射到路由处理程序  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1003">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="05e65-1004">生成响应中使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-1004">Generate the URLs used in responses.</span></span>

<span data-ttu-id="05e65-1005">默认情况下，应用只有一个路由集合。</span><span class="sxs-lookup"><span data-stu-id="05e65-1005">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="05e65-1006">当请求到达时，将按照路由在集合中的存在顺序来处理路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1006">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="05e65-1007">框架试图通过在集合中的每个路由上调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法，将传入请求的 URL 与集合中的路由进行匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-1007">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="05e65-1008">响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="05e65-1008">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="05e65-1009">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="05e65-1009">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="05e65-1010">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1010">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="05e65-1011">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="05e65-1011">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="05e65-1012"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1012"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="05e65-1013">应用模型（如 MVC/Razor Pages）注册了其所有具有可预测的路由方案实现的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1013">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="05e65-1014">响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="05e65-1014">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="05e65-1015">URL 生成基于支持任意可扩展性的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1015">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="05e65-1016"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-1016"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

<span data-ttu-id="05e65-1017">路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。</span><span class="sxs-lookup"><span data-stu-id="05e65-1017">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="05e65-1018">[ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1018">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="05e65-1019">要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-1019">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="05e65-1020">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-1020">URL matching</span></span>

<span data-ttu-id="05e65-1021">URL 匹配是一个过程，通过该过程，路由可向处理程序调度传入请求  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1021">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="05e65-1022">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="05e65-1022">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="05e65-1023">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="05e65-1023">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="05e65-1024">传入请求将进入 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>，后者将对序列中的每个路由调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-1024">Incoming requests enter the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="05e65-1025"><xref:Microsoft.AspNetCore.Routing.IRouter> 实例将选择是否通过将 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) 设置为非 NULL <xref:Microsoft.AspNetCore.Http.RequestDelegate> 来处理请求  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1025">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="05e65-1026">如果路由为请求设置处理程序，将停止路由处理，并调用处理程序来处理该请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-1026">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="05e65-1027">如果未找到用于处理请求的路由处理程序，中间件会将请求传递给请求管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="05e65-1027">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="05e65-1028"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的主要输入是与当前请求关联的 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)。</span><span class="sxs-lookup"><span data-stu-id="05e65-1028">The primary input to <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="05e65-1029">[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 和 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) 是路由匹配后设置的输出。</span><span class="sxs-lookup"><span data-stu-id="05e65-1029">The [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="05e65-1030">调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的匹配还可根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1030">A match that calls <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> also sets the properties of the [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="05e65-1031">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1031">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="05e65-1032">这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-1032">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="05e65-1033">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。</span><span class="sxs-lookup"><span data-stu-id="05e65-1033">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="05e65-1034">提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。</span><span class="sxs-lookup"><span data-stu-id="05e65-1034"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="05e65-1035">这些值是开发者定义的，不会影响通过任何方式路由的行为  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1035">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="05e65-1036">此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。</span><span class="sxs-lookup"><span data-stu-id="05e65-1036">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="05e65-1037">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。</span><span class="sxs-lookup"><span data-stu-id="05e65-1037">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="05e65-1038">路由可以相互嵌套。</span><span class="sxs-lookup"><span data-stu-id="05e65-1038">Routes can be nested inside of one another.</span></span> <span data-ttu-id="05e65-1039"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-1039">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="05e65-1040">通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-1040">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="05e65-1041"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="05e65-1041">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation"></a><span data-ttu-id="05e65-1042">URL 生成</span><span class="sxs-lookup"><span data-stu-id="05e65-1042">URL generation</span></span>

<span data-ttu-id="05e65-1043">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="05e65-1043">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="05e65-1044">这需要考虑处理程序与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="05e65-1044">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="05e65-1045">URL 遵循类似的迭代过程，但开头是将用户或框架代码调用到路由集合的 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法中。</span><span class="sxs-lookup"><span data-stu-id="05e65-1045">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="05e65-1046">每个路由按顺序调用其 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法，直到返回非 NULL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1046">Each *route* has its <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="05e65-1047"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的主输入有：</span><span class="sxs-lookup"><span data-stu-id="05e65-1047">The primary inputs to <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> are:</span></span>

* [<span data-ttu-id="05e65-1048">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="05e65-1048">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [<span data-ttu-id="05e65-1049">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="05e65-1049">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [<span data-ttu-id="05e65-1050">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="05e65-1050">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

<span data-ttu-id="05e65-1051">路由主要使用 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 和 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 提供的路由值来确定是否可能生成 URL 以及要包括哪些值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1051">Routes primarily use the route values provided by <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> and <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="05e65-1052"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 是通过匹配当前请求而生成的路由值集。</span><span class="sxs-lookup"><span data-stu-id="05e65-1052">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="05e65-1053">与此相反，<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 是指定如何为当前操作生成所需 URL 的路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1053">In contrast, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="05e65-1054">如果路由应获取与当前上下文关联的服务或其他数据时，则提供 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>。</span><span class="sxs-lookup"><span data-stu-id="05e65-1054">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="05e65-1055">将 [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) 视为 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) 的一组替代。</span><span class="sxs-lookup"><span data-stu-id="05e65-1055">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="05e65-1056">URL 生成尝试重复使用当前请求中的路由值，以便使用相同路由或路由值生成链接的 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-1056">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="05e65-1057"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的输出是 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。</span><span class="sxs-lookup"><span data-stu-id="05e65-1057">The output of <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is a <xref:Microsoft.AspNetCore.Routing.VirtualPathData>.</span></span> <span data-ttu-id="05e65-1058"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> 是 <xref:Microsoft.AspNetCore.Routing.RouteData> 的并行值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1058"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> is a parallel of <xref:Microsoft.AspNetCore.Routing.RouteData>.</span></span> <span data-ttu-id="05e65-1059"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> 包含输出 URL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 以及路由应该设置的某些其他属性。</span><span class="sxs-lookup"><span data-stu-id="05e65-1059"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> contains the <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="05e65-1060">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 属性包含路由生成的虚拟路径  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1060">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="05e65-1061">你可能需要进一步处理路径，具体取决于你的需求。</span><span class="sxs-lookup"><span data-stu-id="05e65-1061">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="05e65-1062">如果要在 HTML 中呈现生成的 URL，请预置应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-1062">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="05e65-1063">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) 是对已成功生成 URL 的路由的引用。</span><span class="sxs-lookup"><span data-stu-id="05e65-1063">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="05e65-1064">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 属性是生成 URL 的路由的其他相关数据的字典。</span><span class="sxs-lookup"><span data-stu-id="05e65-1064">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="05e65-1065">这是 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 的并性值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1065">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

### <a name="create-routes"></a><span data-ttu-id="05e65-1066">创建路由</span><span class="sxs-lookup"><span data-stu-id="05e65-1066">Create routes</span></span>

<span data-ttu-id="05e65-1067">路由提供 <xref:Microsoft.AspNetCore.Routing.Route> 类，作为 <xref:Microsoft.AspNetCore.Routing.IRouter> 的标准实现。</span><span class="sxs-lookup"><span data-stu-id="05e65-1067">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="05e65-1068"><xref:Microsoft.AspNetCore.Routing.Route> 使用 route template 语法来定义模式，以便在调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 时匹配 URL 路径  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1068"><xref:Microsoft.AspNetCore.Routing.Route> uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="05e65-1069">调用 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 时，<xref:Microsoft.AspNetCore.Routing.Route> 使用同一路由模板生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-1069"><xref:Microsoft.AspNetCore.Routing.Route> uses the same route template to generate a URL when <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is called.</span></span>

<span data-ttu-id="05e65-1070">大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1070">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="05e65-1071">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。</span><span class="sxs-lookup"><span data-stu-id="05e65-1071">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="05e65-1072"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-1072"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="05e65-1073"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1073"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="05e65-1074">默认处理程序是 `IRouter`，该处理程序可能无法处理该请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-1074">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="05e65-1075">例如，ASP.NET Core MVC 通常被配置为默认处理程序，仅处理与可用控制器和操作匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-1075">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="05e65-1076">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="05e65-1076">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="05e65-1077">以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：</span><span class="sxs-lookup"><span data-stu-id="05e65-1077">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="05e65-1078">此模板与 URL 路径相匹配，并且提取路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1078">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="05e65-1079">例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1079">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="05e65-1080">路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1080">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="05e65-1081">路由参数已命名。</span><span class="sxs-lookup"><span data-stu-id="05e65-1081">Route parameters are named.</span></span> <span data-ttu-id="05e65-1082">参数通过将参数名称括在大括号 `{ ... }` 中来定义。</span><span class="sxs-lookup"><span data-stu-id="05e65-1082">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="05e65-1083">上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1083">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="05e65-1084">这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="05e65-1084">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="05e65-1085">路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1085">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="05e65-1086">路由参数名称后面的问号 (`?`) 定义了可选参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-1086">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="05e65-1087">路由匹配时，具有默认值的路由参数始终会生成路由值  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1087">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="05e65-1088">如果没有相应的 URL 路径段，则可选参数不会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1088">Optional parameters don't produce a route value if there was no corresponding URL path segment.</span></span> <span data-ttu-id="05e65-1089">有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-1089">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="05e65-1090">在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="05e65-1090">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="05e65-1091">此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-1091">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="05e65-1092">路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。</span><span class="sxs-lookup"><span data-stu-id="05e65-1092">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="05e65-1093">在此示例中，路由值 `id` 必须可转换为整数。</span><span class="sxs-lookup"><span data-stu-id="05e65-1093">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="05e65-1094">有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="05e65-1094">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="05e65-1095">其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1095">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="05e65-1096">这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="05e65-1096">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="05e65-1097">以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：</span><span class="sxs-lookup"><span data-stu-id="05e65-1097">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

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
> <span data-ttu-id="05e65-1098">定义约束和默认值的内联语法对于简单路由很方便。</span><span class="sxs-lookup"><span data-stu-id="05e65-1098">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="05e65-1099">但是，存在内联语法不支持的方案，例如数据令牌。</span><span class="sxs-lookup"><span data-stu-id="05e65-1099">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="05e65-1100">以下示例演示了一些其他方案：</span><span class="sxs-lookup"><span data-stu-id="05e65-1100">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="05e65-1101">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1101">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="05e65-1102">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="05e65-1102">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="05e65-1103">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1103">Default values can be specified in the route template.</span></span> <span data-ttu-id="05e65-1104">根据路由参数名称前的星号 (`*`) 外观，`article` 路由参数被定义为 catch-all  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1104">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="05e65-1105">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-1105">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="05e65-1106">以下示例添加了路由约束和数据令牌：</span><span class="sxs-lookup"><span data-stu-id="05e65-1106">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="05e65-1107">上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1107">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="05e65-1109">路由类 URL 生成</span><span class="sxs-lookup"><span data-stu-id="05e65-1109">Route class URL generation</span></span>

<span data-ttu-id="05e65-1110"><xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-1110">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="05e65-1111">从逻辑上来说，这是匹配 URL 路径的反向过程。</span><span class="sxs-lookup"><span data-stu-id="05e65-1111">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="05e65-1112">为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-1112">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="05e65-1113">将生成什么值？</span><span class="sxs-lookup"><span data-stu-id="05e65-1113">What values would be produced?</span></span> <span data-ttu-id="05e65-1114">这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。</span><span class="sxs-lookup"><span data-stu-id="05e65-1114">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="05e65-1115">以下示例使用常规 ASP.NET Core MVC 默认路由：</span><span class="sxs-lookup"><span data-stu-id="05e65-1115">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="05e65-1116">使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1116">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="05e65-1117">路由值将替换为相应的路由参数，以形成 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="05e65-1117">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="05e65-1118">由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1118">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="05e65-1119">使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1119">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="05e65-1120">提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。</span><span class="sxs-lookup"><span data-stu-id="05e65-1120">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="05e65-1121">已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1121">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="05e65-1122">使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1122">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="05e65-1123">有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-1123">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="05e65-1124">使用路由中间件</span><span class="sxs-lookup"><span data-stu-id="05e65-1124">Use Routing Middleware</span></span>

<span data-ttu-id="05e65-1125">引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="05e65-1125">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="05e65-1126">将路由添加到 `Startup.ConfigureServices` 中的服务容器：</span><span class="sxs-lookup"><span data-stu-id="05e65-1126">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="05e65-1127">必须在 `Startup.Configure` 方法中配置路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1127">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="05e65-1128">该示例应用使用以下 API：</span><span class="sxs-lookup"><span data-stu-id="05e65-1128">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="05e65-1129"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; 仅匹配 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-1129"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="05e65-1130">下表显示了具有给定 URI 的响应。</span><span class="sxs-lookup"><span data-stu-id="05e65-1130">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="05e65-1131">URI</span><span class="sxs-lookup"><span data-stu-id="05e65-1131">URI</span></span>                    | <span data-ttu-id="05e65-1132">响应</span><span class="sxs-lookup"><span data-stu-id="05e65-1132">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="05e65-1133">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-1133">Hello!</span></span> <span data-ttu-id="05e65-1134">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="05e65-1134">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="05e65-1135">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-1135">Hello!</span></span> <span data-ttu-id="05e65-1136">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="05e65-1136">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="05e65-1137">Hello!</span><span class="sxs-lookup"><span data-stu-id="05e65-1137">Hello!</span></span> <span data-ttu-id="05e65-1138">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="05e65-1138">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="05e65-1139">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-1139">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="05e65-1140">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="05e65-1140">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="05e65-1141">请求失败，仅匹配 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="05e65-1141">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="05e65-1142">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-1142">The request falls through, no match.</span></span>              |

<span data-ttu-id="05e65-1143">如果要配置单个路由，请在 `IRouter` 实例中调用 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> 传入。</span><span class="sxs-lookup"><span data-stu-id="05e65-1143">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="05e65-1144">无需使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder>。</span><span class="sxs-lookup"><span data-stu-id="05e65-1144">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

<span data-ttu-id="05e65-1145">框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="05e65-1145">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="05e65-1146">一些列出的方法（如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>）需要 <xref:Microsoft.AspNetCore.Http.RequestDelegate>。</span><span class="sxs-lookup"><span data-stu-id="05e65-1146">Some of listed methods, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, require a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="05e65-1147">路由匹配时，<xref:Microsoft.AspNetCore.Http.RequestDelegate> 会用作路由处理程序  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1147">The <xref:Microsoft.AspNetCore.Http.RequestDelegate> is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="05e65-1148">此系列中的其他方法允许配置中间件管道，将其用作路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="05e65-1148">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="05e65-1149">如果 `Map*` 方法不接受处理程序（例如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>），则它将使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>。</span><span class="sxs-lookup"><span data-stu-id="05e65-1149">If the `Map*` method doesn't accept a handler, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

<span data-ttu-id="05e65-1150">`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="05e65-1150">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="05e65-1151">有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="05e65-1151">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="05e65-1152">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="05e65-1152">Route template reference</span></span>

<span data-ttu-id="05e65-1153">如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1153">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="05e65-1154">可在路由段中定义多个路由参数，但必须由文本值隔开。</span><span class="sxs-lookup"><span data-stu-id="05e65-1154">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="05e65-1155">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1155">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="05e65-1156">这些路由参数必须具有名称，且可能指定了其他属性。</span><span class="sxs-lookup"><span data-stu-id="05e65-1156">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="05e65-1157">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="05e65-1157">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="05e65-1158">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="05e65-1158">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="05e65-1159">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="05e65-1159">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="05e65-1160">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="05e65-1160">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="05e65-1161">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1161">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="05e65-1162">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1162">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="05e65-1163">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。</span><span class="sxs-lookup"><span data-stu-id="05e65-1163">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="05e65-1164">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="05e65-1164">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="05e65-1165">可以使用星号 (`*`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-1165">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="05e65-1166">这称为 catch-all 参数  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1166">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="05e65-1167">例如，`blog/{*slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="05e65-1167">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="05e65-1168">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-1168">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="05e65-1169">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="05e65-1169">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="05e65-1170">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1170">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="05e65-1171">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="05e65-1171">Note the escaped forward slash.</span></span>

<span data-ttu-id="05e65-1172">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1172">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="05e65-1173">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1173">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="05e65-1174">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1174">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="05e65-1175">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="05e65-1175">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="05e65-1176">可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1176">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="05e65-1177">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-1177">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="05e65-1178">在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1178">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="05e65-1179">如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="05e65-1179">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="05e65-1180">通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-1180">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="05e65-1181">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="05e65-1181">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="05e65-1182">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-1182">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="05e65-1183">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="05e65-1183">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="05e65-1184">下表演示了示例路由模板及其行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-1184">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="05e65-1185">路由模板</span><span class="sxs-lookup"><span data-stu-id="05e65-1185">Route Template</span></span>                           | <span data-ttu-id="05e65-1186">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="05e65-1186">Example Matching URI</span></span>    | <span data-ttu-id="05e65-1187">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="05e65-1187">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="05e65-1188">仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1188">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="05e65-1189">匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1189">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="05e65-1190">匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1190">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="05e65-1191">映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="05e65-1191">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="05e65-1192">映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。</span><span class="sxs-lookup"><span data-stu-id="05e65-1192">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="05e65-1193">映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。</span><span class="sxs-lookup"><span data-stu-id="05e65-1193">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="05e65-1194">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="05e65-1194">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="05e65-1195">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1195">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="05e65-1196">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-1196">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="05e65-1197">保留的路由名称</span><span class="sxs-lookup"><span data-stu-id="05e65-1197">Reserved routing names</span></span>

<span data-ttu-id="05e65-1198">以下关键字是保留的名称，它们不能用作路由名称或参数：</span><span class="sxs-lookup"><span data-stu-id="05e65-1198">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="05e65-1199">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="05e65-1199">Route constraint reference</span></span>

<span data-ttu-id="05e65-1200">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1200">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="05e65-1201">路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。</span><span class="sxs-lookup"><span data-stu-id="05e65-1201">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="05e65-1202">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-1202">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="05e65-1203">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="05e65-1203">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="05e65-1204">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="05e65-1204">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="05e65-1205">请勿将约束用于“输入验证”  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1205">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="05e65-1206">如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求”    。</span><span class="sxs-lookup"><span data-stu-id="05e65-1206">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="05e65-1207">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1207">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="05e65-1208">下表演示示例路由约束及其预期行为。</span><span class="sxs-lookup"><span data-stu-id="05e65-1208">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="05e65-1209">约束</span><span class="sxs-lookup"><span data-stu-id="05e65-1209">constraint</span></span> | <span data-ttu-id="05e65-1210">示例</span><span class="sxs-lookup"><span data-stu-id="05e65-1210">Example</span></span> | <span data-ttu-id="05e65-1211">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="05e65-1211">Example Matches</span></span> | <span data-ttu-id="05e65-1212">说明</span><span class="sxs-lookup"><span data-stu-id="05e65-1212">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="05e65-1213">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="05e65-1213">`123456789`, `-123456789`</span></span> | <span data-ttu-id="05e65-1214">匹配任何整数</span><span class="sxs-lookup"><span data-stu-id="05e65-1214">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="05e65-1215">`true`， `FALSE`</span><span class="sxs-lookup"><span data-stu-id="05e65-1215">`true`, `FALSE`</span></span> | <span data-ttu-id="05e65-1216">匹配 `true`或 `false`（区分大小写）</span><span class="sxs-lookup"><span data-stu-id="05e65-1216">Matches `true` or `false` (case-insensitive)</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="05e65-1217">`2016-12-31`， `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="05e65-1217">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="05e65-1218">匹配有效的 `DateTime` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-1218">Matches a valid `DateTime` value (in the invariant culture - see warning)</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="05e65-1219">`49.99`， `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="05e65-1219">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="05e65-1220">匹配有效的 `decimal` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-1220">Matches a valid `decimal` value (in the invariant culture - see warning)</span></span> |
| `double` | `{weight:double}` | <span data-ttu-id="05e65-1221">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="05e65-1221">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="05e65-1222">匹配有效的 `double` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-1222">Matches a valid `double` value (in the invariant culture - see warning)</span></span> |
| `float` | `{weight:float}` | <span data-ttu-id="05e65-1223">`1.234`， `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="05e65-1223">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="05e65-1224">匹配有效的 `float` 值（位于固定区域性中 - 查看警告）</span><span class="sxs-lookup"><span data-stu-id="05e65-1224">Matches a valid `float` value (in the invariant culture - see warning)</span></span> |
| `guid` | `{id:guid}` | <span data-ttu-id="05e65-1225">`CD2C1638-1638-72D5-1638-DEADBEEF1638`， `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="05e65-1225">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="05e65-1226">匹配有效的 `Guid` 值</span><span class="sxs-lookup"><span data-stu-id="05e65-1226">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="05e65-1227">`123456789`， `-123456789`</span><span class="sxs-lookup"><span data-stu-id="05e65-1227">`123456789`, `-123456789`</span></span> | <span data-ttu-id="05e65-1228">匹配有效的 `long` 值</span><span class="sxs-lookup"><span data-stu-id="05e65-1228">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="05e65-1229">字符串必须至少为 4 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-1229">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="05e65-1230">字符串不得超过 8 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-1230">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="05e65-1231">字符串必须正好为 12 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-1231">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="05e65-1232">字符串必须至少为 8 个字符，且不得超过 16 个字符</span><span class="sxs-lookup"><span data-stu-id="05e65-1232">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="05e65-1233">整数值必须至少为 18</span><span class="sxs-lookup"><span data-stu-id="05e65-1233">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="05e65-1234">整数值不得超过 120</span><span class="sxs-lookup"><span data-stu-id="05e65-1234">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="05e65-1235">整数值必须至少为 18，且不得超过 120</span><span class="sxs-lookup"><span data-stu-id="05e65-1235">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="05e65-1236">字符串必须由一个或多个字母字符（`a`-`z`，区分大小写）组成</span><span class="sxs-lookup"><span data-stu-id="05e65-1236">String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive)</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="05e65-1237">字符串必须匹配正则表达式（参见有关定义正则表达式的提示）</span><span class="sxs-lookup"><span data-stu-id="05e65-1237">String must match the regular expression (see tips about defining a regular expression)</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="05e65-1238">用于强制在 URL 生成过程中存在非参数值</span><span class="sxs-lookup"><span data-stu-id="05e65-1238">Used to enforce that a non-parameter value is present during URL generation</span></span> |

<span data-ttu-id="05e65-1239">可向单个参数应用多个由冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-1239">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="05e65-1240">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="05e65-1240">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="05e65-1241">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="05e65-1241">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="05e65-1242">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="05e65-1242">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="05e65-1243">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1243">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="05e65-1244">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="05e65-1244">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="05e65-1245">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="05e65-1245">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="05e65-1246">正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-1246">Regular expressions</span></span>

<span data-ttu-id="05e65-1247">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1247">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="05e65-1248">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="05e65-1248">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="05e65-1249">正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="05e65-1249">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="05e65-1250">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="05e65-1250">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="05e65-1251">要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。</span><span class="sxs-lookup"><span data-stu-id="05e65-1251">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="05e65-1252">要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="05e65-1252">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="05e65-1253">下表展示了正则表达式和转义版本。</span><span class="sxs-lookup"><span data-stu-id="05e65-1253">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="05e65-1254">正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-1254">Regular Expression</span></span>    | <span data-ttu-id="05e65-1255">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-1255">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="05e65-1256">路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="05e65-1256">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="05e65-1257">表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="05e65-1257">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="05e65-1258">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1258">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="05e65-1259">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="05e65-1259">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="05e65-1260">下表提供了示例并说明了它们匹配或匹配失败的原因。</span><span class="sxs-lookup"><span data-stu-id="05e65-1260">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="05e65-1261">表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-1261">Expression</span></span>   | <span data-ttu-id="05e65-1262">String</span><span class="sxs-lookup"><span data-stu-id="05e65-1262">String</span></span>    | <span data-ttu-id="05e65-1263">匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-1263">Match</span></span> | <span data-ttu-id="05e65-1264">注释</span><span class="sxs-lookup"><span data-stu-id="05e65-1264">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="05e65-1265">hello</span><span class="sxs-lookup"><span data-stu-id="05e65-1265">hello</span></span>     | <span data-ttu-id="05e65-1266">是</span><span class="sxs-lookup"><span data-stu-id="05e65-1266">Yes</span></span>   | <span data-ttu-id="05e65-1267">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-1267">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="05e65-1268">123abc456</span><span class="sxs-lookup"><span data-stu-id="05e65-1268">123abc456</span></span> | <span data-ttu-id="05e65-1269">是</span><span class="sxs-lookup"><span data-stu-id="05e65-1269">Yes</span></span>   | <span data-ttu-id="05e65-1270">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="05e65-1270">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="05e65-1271">mz</span><span class="sxs-lookup"><span data-stu-id="05e65-1271">mz</span></span>        | <span data-ttu-id="05e65-1272">是</span><span class="sxs-lookup"><span data-stu-id="05e65-1272">Yes</span></span>   | <span data-ttu-id="05e65-1273">匹配表达式</span><span class="sxs-lookup"><span data-stu-id="05e65-1273">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="05e65-1274">MZ</span><span class="sxs-lookup"><span data-stu-id="05e65-1274">MZ</span></span>        | <span data-ttu-id="05e65-1275">是</span><span class="sxs-lookup"><span data-stu-id="05e65-1275">Yes</span></span>   | <span data-ttu-id="05e65-1276">不区分大小写</span><span class="sxs-lookup"><span data-stu-id="05e65-1276">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="05e65-1277">hello</span><span class="sxs-lookup"><span data-stu-id="05e65-1277">hello</span></span>     | <span data-ttu-id="05e65-1278">No</span><span class="sxs-lookup"><span data-stu-id="05e65-1278">No</span></span>    | <span data-ttu-id="05e65-1279">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="05e65-1279">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="05e65-1280">123abc456</span><span class="sxs-lookup"><span data-stu-id="05e65-1280">123abc456</span></span> | <span data-ttu-id="05e65-1281">No</span><span class="sxs-lookup"><span data-stu-id="05e65-1281">No</span></span>    | <span data-ttu-id="05e65-1282">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="05e65-1282">See `^` and `$` above</span></span> |

<span data-ttu-id="05e65-1283">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="05e65-1283">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="05e65-1284">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="05e65-1284">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="05e65-1285">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1285">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="05e65-1286">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="05e65-1286">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="05e65-1287">已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="05e65-1287">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="05e65-1288">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="05e65-1288">Custom Route Constraints</span></span>

<span data-ttu-id="05e65-1289">除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="05e65-1289">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="05e65-1290"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1290">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="05e65-1291">若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="05e65-1291">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="05e65-1292"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="05e65-1292">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="05e65-1293">应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="05e65-1293">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="05e65-1294">例如:</span><span class="sxs-lookup"><span data-stu-id="05e65-1294">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="05e65-1295">然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。</span><span class="sxs-lookup"><span data-stu-id="05e65-1295">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="05e65-1296">例如:</span><span class="sxs-lookup"><span data-stu-id="05e65-1296">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a><span data-ttu-id="05e65-1297">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="05e65-1297">URL generation reference</span></span>

<span data-ttu-id="05e65-1298">以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。</span><span class="sxs-lookup"><span data-stu-id="05e65-1298">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="05e65-1299">上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="05e65-1299">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="05e65-1300">字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1300">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="05e65-1301">有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。</span><span class="sxs-lookup"><span data-stu-id="05e65-1301">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="05e65-1302"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合  。</span><span class="sxs-lookup"><span data-stu-id="05e65-1302">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="05e65-1303">由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。</span><span class="sxs-lookup"><span data-stu-id="05e65-1303">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="05e65-1304">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1304">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="05e65-1305">在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。</span><span class="sxs-lookup"><span data-stu-id="05e65-1305">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="05e65-1306">忽略与参数不匹配的环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1306">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="05e65-1307">在显式提供的值会覆盖环境值的情况下，也会忽略环境值。</span><span class="sxs-lookup"><span data-stu-id="05e65-1307">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="05e65-1308">在 URL 中将从左到右进行匹配。</span><span class="sxs-lookup"><span data-stu-id="05e65-1308">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="05e65-1309">显式提供但与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="05e65-1309">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="05e65-1310">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="05e65-1310">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="05e65-1311">环境值</span><span class="sxs-lookup"><span data-stu-id="05e65-1311">Ambient Values</span></span>                     | <span data-ttu-id="05e65-1312">显式值</span><span class="sxs-lookup"><span data-stu-id="05e65-1312">Explicit Values</span></span>                        | <span data-ttu-id="05e65-1313">结果</span><span class="sxs-lookup"><span data-stu-id="05e65-1313">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="05e65-1314">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-1314">controller = "Home"</span></span>                | <span data-ttu-id="05e65-1315">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-1315">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="05e65-1316">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-1316">controller = "Home"</span></span>                | <span data-ttu-id="05e65-1317">控制器 =“Order”，操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-1317">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="05e65-1318">控制器 = “Home”，颜色 = “Red”</span><span class="sxs-lookup"><span data-stu-id="05e65-1318">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="05e65-1319">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="05e65-1319">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="05e65-1320">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="05e65-1320">controller = "Home"</span></span>                | <span data-ttu-id="05e65-1321">操作 =“About”，颜色 =“Red”</span><span class="sxs-lookup"><span data-stu-id="05e65-1321">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="05e65-1322">如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：</span><span class="sxs-lookup"><span data-stu-id="05e65-1322">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="05e65-1323">当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。</span><span class="sxs-lookup"><span data-stu-id="05e65-1323">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="05e65-1324">复杂段</span><span class="sxs-lookup"><span data-stu-id="05e65-1324">Complex segments</span></span>

<span data-ttu-id="05e65-1325">复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="05e65-1325">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="05e65-1326">请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。</span><span class="sxs-lookup"><span data-stu-id="05e65-1326">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="05e65-1327">ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。</span><span class="sxs-lookup"><span data-stu-id="05e65-1327">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end
