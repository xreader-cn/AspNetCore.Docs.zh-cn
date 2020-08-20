---
title: ASP.NET Core 中的路由
author: rick-anderson
description: 了解 ASP.NET Core 路由如何负责匹配 HTTP 请求并将其发送到可执行终结点。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/routing
ms.openlocfilehash: cf450385db3c7327de233357d4c13d556ee44bad
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633664"
---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="2e9d7-103">ASP.NET Core 中的路由</span><span class="sxs-lookup"><span data-stu-id="2e9d7-103">Routing in ASP.NET Core</span></span>

<span data-ttu-id="2e9d7-104">作者：[Ryan Nowak](https://github.com/rynowak)、[Kirk Larkinn](https://twitter.com/serpent5) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2e9d7-105">路由负责匹配传入的 HTTP 请求，然后将这些请求发送到应用的可执行终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-105">Routing is responsible for matching incoming HTTP requests and dispatching those requests to the app's executable endpoints.</span></span> <span data-ttu-id="2e9d7-106">[终结点](#endpoint)是应用的可执行请求处理代码单元。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-106">[Endpoints](#endpoint) are the app's units of executable request-handling code.</span></span> <span data-ttu-id="2e9d7-107">终结点在应用中进行定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-107">Endpoints are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="2e9d7-108">终结点匹配过程可以从请求的 URL 中提取值，并为请求处理提供这些值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-108">The endpoint matching process can extract values from the request's URL and provide those values for request processing.</span></span> <span data-ttu-id="2e9d7-109">通过使用应用中的终结点信息，路由还能生成映射到终结点的 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-109">Using endpoint information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="2e9d7-110">应用可以使用以下内容配置路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-110">Apps can configure routing using:</span></span>

- <span data-ttu-id="2e9d7-111">Controllers</span><span class="sxs-lookup"><span data-stu-id="2e9d7-111">Controllers</span></span>
- <span data-ttu-id="2e9d7-112">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2e9d7-112">Razor Pages</span></span>
- SignalR
- <span data-ttu-id="2e9d7-113">gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="2e9d7-113">gRPC Services</span></span>
- <span data-ttu-id="2e9d7-114">启用终结点的[中间件](xref:fundamentals/middleware/index)，例如[运行状况检查](xref:host-and-deploy/health-checks)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-114">Endpoint-enabled [middleware](xref:fundamentals/middleware/index) such as [Health Checks](xref:host-and-deploy/health-checks).</span></span>
- <span data-ttu-id="2e9d7-115">通过路由注册的委托和 Lambda。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-115">Delegates and lambdas registered with routing.</span></span>

<span data-ttu-id="2e9d7-116">本文档介绍 ASP.NET Core 路由的较低级别详细信息。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-116">This document covers low-level details of ASP.NET Core routing.</span></span> <span data-ttu-id="2e9d7-117">有关配置路由的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-117">For information on configuring routing:</span></span>

* <span data-ttu-id="2e9d7-118">对于控制器，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-118">For controllers, see <xref:mvc/controllers/routing>.</span></span>
* <span data-ttu-id="2e9d7-119">对于 Razor Pages 约定，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-119">For Razor Pages conventions, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="2e9d7-120">本文档中所述的终结点路由系统适用于 ASP.NET Core 3.0 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-120">The endpoint routing system described in this document applies to ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="2e9d7-121">有关以前基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由系统信息，请使用以下方法之一选择 ASP.NET Core 2.1 版本：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-121">For information on the previous routing system based on <xref:Microsoft.AspNetCore.Routing.IRouter>, select the ASP.NET Core 2.1 version using one of the following approaches:</span></span>

* <span data-ttu-id="2e9d7-122">以前版本的版本选择器。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-122">The version selector for a previous version.</span></span>
* <span data-ttu-id="2e9d7-123">选择 [ASP.NET Core 2.1 路由](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-123">Select [ASP.NET Core 2.1 routing](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="2e9d7-124">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2e9d7-124">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2e9d7-125">此文档的下载示例由特定 `Startup` 类启用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-125">The download samples for this document are enabled by a specific `Startup` class.</span></span> <span data-ttu-id="2e9d7-126">若要运行特定的示例，请修改 Program.cs，以便调用所需的 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-126">To run a specific sample, modify *Program.cs* to call the desired `Startup` class.</span></span>

## <a name="routing-basics"></a><span data-ttu-id="2e9d7-127">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="2e9d7-127">Routing basics</span></span>

<span data-ttu-id="2e9d7-128">所有 ASP.NET Core 模板都包括生成的代码中的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-128">All ASP.NET Core templates include routing in the generated code.</span></span> <span data-ttu-id="2e9d7-129">路由在 `Startup.Configure` 中的[中间件](xref:fundamentals/middleware/index)管道中进行注册。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-129">Routing is registered in the [middleware](xref:fundamentals/middleware/index) pipeline in `Startup.Configure`.</span></span>

<span data-ttu-id="2e9d7-130">以下代码演示路由的基本示例：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-130">The following code shows a basic example of routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

<span data-ttu-id="2e9d7-131">路由使用一对由 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> 和 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 注册的中间件：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-131">Routing uses a pair of middleware, registered by <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>:</span></span>

* <span data-ttu-id="2e9d7-132">`UseRouting` 向中间件管道添加路由匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-132">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="2e9d7-133">此中间件会查看应用中定义的终结点集，并根据请求选择[最佳匹配](#urlm)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-133">This middleware looks at the set of endpoints defined in the app, and selects the [best match](#urlm) based on the request.</span></span>
* <span data-ttu-id="2e9d7-134">`UseEndpoints` 向中间件管道添加终结点执行。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-134">`UseEndpoints` adds endpoint execution to the middleware pipeline.</span></span> <span data-ttu-id="2e9d7-135">它会运行与所选终结点关联的委托。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-135">It runs the delegate associated with the selected endpoint.</span></span>

<span data-ttu-id="2e9d7-136">前面的示例包含使用 [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) 方法的单一路由到代码终结点：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-136">The preceding example includes a single *route to code* endpoint using the [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) method:</span></span>

* <span data-ttu-id="2e9d7-137">当 HTTP `GET` 请求发送到根 URL `/` 时：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-137">When an HTTP `GET` request is sent to the root URL `/`:</span></span>
  * <span data-ttu-id="2e9d7-138">将执行显示的请求委托。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-138">The request delegate shown executes.</span></span>
  * <span data-ttu-id="2e9d7-139">`Hello World!` 会写入 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-139">`Hello World!` is written to the HTTP response.</span></span> <span data-ttu-id="2e9d7-140">默认情况下，根 URL `/` 为 `https://localhost:5001/`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-140">By default, the root URL `/` is `https://localhost:5001/`.</span></span>
* <span data-ttu-id="2e9d7-141">如果请求方法不是 `GET` 或根 URL 不是 `/`，则无路由匹配，并返回 HTTP 404。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-141">If the request method is not `GET` or the root URL is not `/`, no route matches and an HTTP 404 is returned.</span></span>

### <a name="endpoint"></a><span data-ttu-id="2e9d7-142">终结点</span><span class="sxs-lookup"><span data-stu-id="2e9d7-142">Endpoint</span></span>

<a name="endpoint"></a>

<span data-ttu-id="2e9d7-143">`MapGet` 方法用于定义终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-143">The `MapGet` method is used to define an **endpoint**.</span></span> <span data-ttu-id="2e9d7-144">终结点可以：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-144">An endpoint is something that can be:</span></span>

* <span data-ttu-id="2e9d7-145">通过匹配 URL 和 HTTP 方法来选择。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-145">Selected, by matching the URL and HTTP method.</span></span>
* <span data-ttu-id="2e9d7-146">通过运行委托来执行。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-146">Executed, by running the delegate.</span></span>

<span data-ttu-id="2e9d7-147">可通过应用匹配和执行的终结点在 `UseEndpoints` 中进行配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-147">Endpoints that can be matched and executed by the app are configured in `UseEndpoints`.</span></span> <span data-ttu-id="2e9d7-148">例如，<xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>、<xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> 和[类似的方法](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions)将请求委托连接到路由系统。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-148">For example, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, and [similar methods](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) connect request delegates to the routing system.</span></span>
<span data-ttu-id="2e9d7-149">其他方法可用于将 ASP.NET Core 框架功能连接到路由系统：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-149">Additional methods can be used to connect ASP.NET Core framework features to the routing system:</span></span>
- <span data-ttu-id="2e9d7-150">[用于 Razor Pages 的 MapRazorPages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-150">[MapRazorPages for Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span></span>
- [<span data-ttu-id="2e9d7-151">用于控制器的 MapControllers</span><span class="sxs-lookup"><span data-stu-id="2e9d7-151">MapControllers for controllers</span></span>](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- <span data-ttu-id="2e9d7-152">[用于 SignalR 的 MapHub\<THub>](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-152">[MapHub\<THub> for SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span></span> 
- [<span data-ttu-id="2e9d7-153">用于 gRPC 的 MapGrpcService\<TService></span><span class="sxs-lookup"><span data-stu-id="2e9d7-153">MapGrpcService\<TService> for gRPC</span></span>](xref:grpc/aspnetcore)

<span data-ttu-id="2e9d7-154">下面的示例演示如何使用更复杂的路由模板进行路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-154">The following example shows routing with a more sophisticated route template:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

<span data-ttu-id="2e9d7-155">`/hello/{name:alpha}` 字符串是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-155">The string `/hello/{name:alpha}` is a **route template**.</span></span> <span data-ttu-id="2e9d7-156">用于配置终结点的匹配方式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-156">It is used to configure how the endpoint is matched.</span></span> <span data-ttu-id="2e9d7-157">在这种情况下，模板将匹配：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-157">In this case, the template matches:</span></span>

* <span data-ttu-id="2e9d7-158">类似 `/hello/Ryan` 的 URL</span><span class="sxs-lookup"><span data-stu-id="2e9d7-158">A URL like `/hello/Ryan`</span></span>
* <span data-ttu-id="2e9d7-159">以 `/hello/` 开头、后跟一系列字母字符的任何 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-159">Any URL path that begins with `/hello/` followed by a sequence of alphabetic characters.</span></span>  <span data-ttu-id="2e9d7-160">`:alpha` 应用仅匹配字母字符的路由约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-160">`:alpha` applies a route constraint that matches only alphabetic characters.</span></span> <span data-ttu-id="2e9d7-161">[路由约束](#route-constraint-reference)将在本文档的后面详细介绍。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-161">[Route constraints](#route-constraint-reference) are explained later in this document.</span></span>

<span data-ttu-id="2e9d7-162">URL 路径的第二段 `{name:alpha}`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-162">The second segment of the URL path, `{name:alpha}`:</span></span>

* <span data-ttu-id="2e9d7-163">绑定到 `name` 参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-163">Is bound to the `name` parameter.</span></span>
* <span data-ttu-id="2e9d7-164">捕获并存储在 [HttpRequest. RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*) 中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-164">Is captured and stored in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).</span></span>

<span data-ttu-id="2e9d7-165">本文档中所述的终结点路由系统是新版本 ASP.NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-165">The endpoint routing system described in this document is new as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="2e9d7-166">但是，所有版本的 ASP.NET Core 都支持相同的路由模板功能和路由约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-166">However, all versions of ASP.NET Core support the same set of route template features and route constraints.</span></span>

<span data-ttu-id="2e9d7-167">下面的示例演示如何通过[运行状况检查](xref:host-and-deploy/health-checks)和授权进行路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-167">The following example shows routing with [health checks](xref:host-and-deploy/health-checks) and authorization:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="2e9d7-168">前面的示例展示了如何：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-168">The preceding example demonstrates how:</span></span>

* <span data-ttu-id="2e9d7-169">将授权中间件与路由一起使用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-169">The authorization middleware can be used with routing.</span></span>
* <span data-ttu-id="2e9d7-170">将终结点用于配置授权行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-170">Endpoints can be used to configure authorization behavior.</span></span>

<span data-ttu-id="2e9d7-171"><xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> 调用添加运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-171">The <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> call adds a health check endpoint.</span></span> <span data-ttu-id="2e9d7-172">将 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> 链接到此调用会将授权策略附加到该终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-172">Chaining <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> on to this call attaches an authorization policy to the endpoint.</span></span>

<span data-ttu-id="2e9d7-173">调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 和 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> 会添加身份验证和授权中间件。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-173">Calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> adds the authentication and authorization middleware.</span></span> <span data-ttu-id="2e9d7-174">这些中间件位于 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> 和 `UseEndpoints` 之间，因此它们可以：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-174">These middleware are placed between <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and `UseEndpoints` so that they can:</span></span>

* <span data-ttu-id="2e9d7-175">查看 `UseRouting` 选择的终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-175">See which endpoint was selected by `UseRouting`.</span></span>
* <span data-ttu-id="2e9d7-176">将 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 发送到终结点之前应用授权策略。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-176">Apply an authorization policy before <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> dispatches to the endpoint.</span></span>

<a name="metadata"></a>

### <a name="endpoint-metadata"></a><span data-ttu-id="2e9d7-177">终结点元数据</span><span class="sxs-lookup"><span data-stu-id="2e9d7-177">Endpoint metadata</span></span>

<span data-ttu-id="2e9d7-178">前面的示例中有两个终结点，但只有运行状况检查终结点附加了授权策略。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-178">In the preceding example, there are two endpoints, but only the health check endpoint has an authorization policy attached.</span></span> <span data-ttu-id="2e9d7-179">如果请求与运行状况检查终结点 `/healthz` 匹配，则执行授权检查。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-179">If the request matches the health check endpoint, `/healthz`, an authorization check is performed.</span></span> <span data-ttu-id="2e9d7-180">这表明，终结点可以附加额外的数据。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-180">This demonstrates that endpoints can have extra data attached to them.</span></span> <span data-ttu-id="2e9d7-181">此额外数据称为终结点元数据：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-181">This extra data is called endpoint **metadata**:</span></span>

* <span data-ttu-id="2e9d7-182">可以通过路由感知中间件来处理元数据。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-182">The metadata can be processed by routing-aware middleware.</span></span>
* <span data-ttu-id="2e9d7-183">元数据可以是任意的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-183">The metadata can be of any .NET type.</span></span>

## <a name="routing-concepts"></a><span data-ttu-id="2e9d7-184">路由概念</span><span class="sxs-lookup"><span data-stu-id="2e9d7-184">Routing concepts</span></span>

<span data-ttu-id="2e9d7-185">路由系统通过添加功能强大的终结点概念，构建在中间件管道之上。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-185">The routing system builds on top of the middleware pipeline by adding the powerful **endpoint** concept.</span></span> <span data-ttu-id="2e9d7-186">终结点代表应用的功能单元，在路由、授权和任意数量的 ASP.NET Core 系统方面彼此不同。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-186">Endpoints represent units of the app's functionality that are distinct from each other in terms of routing, authorization, and any number of ASP.NET Core's systems.</span></span>

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a><span data-ttu-id="2e9d7-187">ASP.NET Core 终结点定义</span><span class="sxs-lookup"><span data-stu-id="2e9d7-187">ASP.NET Core endpoint definition</span></span>

<span data-ttu-id="2e9d7-188">ASP.NET Core 终结点是：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-188">An ASP.NET Core endpoint is:</span></span>

* <span data-ttu-id="2e9d7-189">可执行：具有 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-189">Executable: Has a <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.</span></span>
* <span data-ttu-id="2e9d7-190">可扩展：具有[元数据](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*)集合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-190">Extensible: Has a [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) collection.</span></span>
* <span data-ttu-id="2e9d7-191">Selectable:可选择性包含[路由信息](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-191">Selectable: Optionally, has [routing information](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).</span></span>
* <span data-ttu-id="2e9d7-192">可枚举：可通过从 [DI](xref:fundamentals/dependency-injection) 中检索 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> 来列出终结点集合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-192">Enumerable: The collection of endpoints can be listed by retrieving the <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> from [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2e9d7-193">以下代码显示了如何检索和检查与当前请求匹配的终结点：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-193">The following code shows how to retrieve and inspect the endpoint matching the current request:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

<span data-ttu-id="2e9d7-194">如果选择了终结点，可从 `HttpContext` 中进行检索。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-194">The endpoint, if selected, can be retrieved from the `HttpContext`.</span></span> <span data-ttu-id="2e9d7-195">可以检查其属性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-195">Its properties can be inspected.</span></span> <span data-ttu-id="2e9d7-196">终结点对象是不可变的，并且在创建后无法修改。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-196">Endpoint objects are immutable and cannot be modified after creation.</span></span> <span data-ttu-id="2e9d7-197">最常见的终结点类型是 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-197">The most common type of endpoint is a <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>.</span></span> <span data-ttu-id="2e9d7-198">`RouteEndpoint` 包括允许自己被路由系统选择的信息。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-198">`RouteEndpoint` includes information that allows it to be to selected by the routing system.</span></span>

<span data-ttu-id="2e9d7-199">在前面的代码中，[app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) 配置了一个内联[中间件](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-199">In the preceding code, [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configures an in-line [middleware](xref:fundamentals/middleware/index).</span></span>

<a name="mt"></a>

<span data-ttu-id="2e9d7-200">下面的代码显示，根据管道中调用 `app.Use` 的位置，可能不存在终结点：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-200">The following code shows that, depending on where `app.Use` is called in the pipeline, there may not be an endpoint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

<span data-ttu-id="2e9d7-201">前面的示例添加了 `Console.WriteLine` 语句，这些语句显示是否已选择终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-201">This preceding sample adds `Console.WriteLine` statements that display whether or not an endpoint has been selected.</span></span> <span data-ttu-id="2e9d7-202">为清楚起见，该示例将显示名称分配给提供的 `/` 终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-202">For clarity, the sample assigns a display name to the provided `/` endpoint.</span></span>

<span data-ttu-id="2e9d7-203">使用 `/` URL 运行此代码将显示：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-203">Running this code with a URL of `/` displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

<span data-ttu-id="2e9d7-204">使用任何其他 URL 运行此代码将显示：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-204">Running this code with any other URL displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

<span data-ttu-id="2e9d7-205">此输出说明：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-205">This output demonstrates that:</span></span>

* <span data-ttu-id="2e9d7-206">调用 `UseRouting` 之前，终结点始终为 null。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-206">The endpoint is always null before `UseRouting` is called.</span></span>
* <span data-ttu-id="2e9d7-207">如果找到匹配项，则 `UseRouting` 和 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 之间的终结点为非 null。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-207">If a match is found, the endpoint is non-null between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="2e9d7-208">如果找到匹配项，则 `UseEndpoints` 中间件即为终端。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-208">The `UseEndpoints` middleware is **terminal** when a match is found.</span></span> <span data-ttu-id="2e9d7-209">稍后会在本文档中定义[终端中间件](#tm)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-209">[Terminal middleware](#tm) is defined later in this document.</span></span>
* <span data-ttu-id="2e9d7-210">仅当找不到匹配项时才执行 `UseEndpoints` 后的中间件。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-210">The middleware after `UseEndpoints` execute only when no match is found.</span></span>

<span data-ttu-id="2e9d7-211">`UseRouting` 中间件使用 [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) 方法将终结点附加到当前上下文。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-211">The `UseRouting` middleware uses the [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) method to attach the endpoint to the current context.</span></span> <span data-ttu-id="2e9d7-212">可以将 `UseRouting` 中间件替换为自定义逻辑，同时仍可获得使用终结点的益处。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-212">It's possible to replace the `UseRouting` middleware with custom logic and still get the benefits of using endpoints.</span></span> <span data-ttu-id="2e9d7-213">终结点是中间件等低级别基元，不与路由实现耦合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-213">Endpoints are a low-level primitive like middleware, and aren't coupled to the routing implementation.</span></span> <span data-ttu-id="2e9d7-214">大多数应用都不需要将 `UseRouting` 替换为自定义逻辑。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-214">Most apps don't need to replace `UseRouting` with custom logic.</span></span>

<span data-ttu-id="2e9d7-215">`UseEndpoints` 中间件旨在与 `UseRouting` 中间件配合使用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-215">The `UseEndpoints` middleware is designed to be used in tandem with the `UseRouting` middleware.</span></span> <span data-ttu-id="2e9d7-216">执行终结点的核心逻辑并不复杂。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-216">The core logic to execute an endpoint isn't complicated.</span></span> <span data-ttu-id="2e9d7-217">使用 <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> 检索终结点，然后调用其 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> 属性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-217">Use <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> to retrieve the endpoint, and then invoke its <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> property.</span></span>

<span data-ttu-id="2e9d7-218">下面的代码演示中间件如何影响或响应路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-218">The following code demonstrates how middleware can influence or react to routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="2e9d7-219">前面的示例演示两个重要概念：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-219">The preceding example demonstrates two important concepts:</span></span>

* <span data-ttu-id="2e9d7-220">中间件可以在 `UseRouting` 之前运行，以修改路由操作的数据。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-220">Middleware can run before `UseRouting` to modify the data that routing operates upon.</span></span>
    * <span data-ttu-id="2e9d7-221">通常，在路由之前出现的中间件会修改请求的某些属性，如 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>、<xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> 或 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-221">Usually middleware that appears before routing modifies some property of the request, such as <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>, or <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.</span></span>
* <span data-ttu-id="2e9d7-222">中间件可以在 `UseRouting` 和 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 之间运行，以便在执行终结点前处理路由结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-222">Middleware can run between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> to process the results of routing before the endpoint is executed.</span></span>
    * <span data-ttu-id="2e9d7-223">在 `UseRouting` 和 `UseEndpoints` 之间运行的中间件：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-223">Middleware that runs between `UseRouting` and `UseEndpoints`:</span></span>
      * <span data-ttu-id="2e9d7-224">通常会检查元数据以了解终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-224">Usually inspects metadata to understand the endpoints.</span></span>
      * <span data-ttu-id="2e9d7-225">通常会根据 `UseAuthorization` 和 `UseCors` 做出安全决策。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-225">Often makes security decisions, as done by `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="2e9d7-226">中间件和元数据的组合允许按终结点配置策略。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-226">The combination of middleware and metadata allows configuring policies per-endpoint.</span></span>

<span data-ttu-id="2e9d7-227">前面的代码显示了支持按终结点策略的自定义中间件示例。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-227">The preceding code shows an example of a custom middleware that supports per-endpoint policies.</span></span> <span data-ttu-id="2e9d7-228">中间件将访问敏感数据的审核日志写入控制台。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-228">The middleware writes an *audit log* of access to sensitive data to the console.</span></span> <span data-ttu-id="2e9d7-229">可以将中间件配置为审核具有 `AuditPolicyAttribute` 元数据的终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-229">The middleware can be configured to *audit* an endpoint with the `AuditPolicyAttribute` metadata.</span></span> <span data-ttu-id="2e9d7-230">此示例演示选择加入模式，其中仅审核标记为敏感的终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-230">This sample demonstrates an *opt-in* pattern where only endpoints that are marked as sensitive are audited.</span></span> <span data-ttu-id="2e9d7-231">例如，可以反向定义此逻辑，从而审核未标记为安全的所有内容。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-231">It's possible to define this logic in reverse, auditing everything that isn't marked as safe, for example.</span></span> <span data-ttu-id="2e9d7-232">终结点元数据系统非常灵活。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-232">The endpoint metadata system is flexible.</span></span> <span data-ttu-id="2e9d7-233">此逻辑可以以任何适合用例的方法进行设计。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-233">This logic could be designed in whatever way suits the use case.</span></span>

<span data-ttu-id="2e9d7-234">前面的示例代码旨在演示终结点的基本概念。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-234">The preceding sample code is intended to demonstrate the basic concepts of endpoints.</span></span> <span data-ttu-id="2e9d7-235">该示例不应在生产环境中使用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-235">**The sample is not intended for production use**.</span></span> <span data-ttu-id="2e9d7-236">审核日志中间件的更完整版本如下：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-236">A more complete version of an *audit log* middleware would:</span></span>

* <span data-ttu-id="2e9d7-237">记录到文件或数据库。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-237">Log to a file or database.</span></span>
* <span data-ttu-id="2e9d7-238">包括详细信息，如用户、IP 地址、敏感终结点的名称等。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-238">Include details such as the user, IP address, name of the sensitive endpoint, and more.</span></span>

<span data-ttu-id="2e9d7-239">审核策略元数据 `AuditPolicyAttribute` 定义为一个 `Attribute`，便于和基于类的框架（如控制器和 SignalR）结合使用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-239">The audit policy metadata `AuditPolicyAttribute` is defined as an `Attribute` for easier use with class-based frameworks such as controllers and SignalR.</span></span> <span data-ttu-id="2e9d7-240">使用路由到代码时：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-240">When using *route to code*:</span></span>

* <span data-ttu-id="2e9d7-241">元数据附有生成器 API。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-241">Metadata is attached with a builder API.</span></span>
* <span data-ttu-id="2e9d7-242">基于类的框架在创建终结点时，包含了相应方法和类的所有特性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-242">Class-based frameworks include all attributes on the corresponding method and class when creating endpoints.</span></span>

<span data-ttu-id="2e9d7-243">对于元数据类型，最佳做法是将它们定义为接口或特性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-243">The best practices for metadata types are to define them either as interfaces or attributes.</span></span> <span data-ttu-id="2e9d7-244">接口和特性允许代码重用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-244">Interfaces and attributes allow code reuse.</span></span> <span data-ttu-id="2e9d7-245">元数据系统非常灵活，无任何限制。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-245">The metadata system is flexible and doesn't impose any limitations.</span></span>

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a><span data-ttu-id="2e9d7-246">比较终端中间件和路由</span><span class="sxs-lookup"><span data-stu-id="2e9d7-246">Comparing a terminal middleware and routing</span></span>

<span data-ttu-id="2e9d7-247">下面的代码示例对比使用中间件和使用路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-247">The following code sample contrasts using middleware with using routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="2e9d7-248">使用 `Approach 1:` 显示的中间件样式是终端中间件。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-248">The style of middleware shown with `Approach 1:` is **terminal middleware**.</span></span> <span data-ttu-id="2e9d7-249">之所以称之为终端中间件，是因为它执行匹配的操作：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-249">It's called terminal middleware because it does a matching operation:</span></span>

* <span data-ttu-id="2e9d7-250">前面示例中的匹配操作是用于中间件的 `Path == "/"` 和用于路由的 `Path == "/Movie"`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-250">The matching operation in the preceding sample is `Path == "/"` for the middleware and `Path == "/Movie"` for routing.</span></span>
* <span data-ttu-id="2e9d7-251">如果匹配成功，它将执行一些功能并返回，而不是调用 `next` 中间件。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-251">When a match is successful, it executes some functionality and returns, rather than invoking the `next` middleware.</span></span>

<span data-ttu-id="2e9d7-252">之所以称之为终端中间件，是因为它会终止搜索，执行一些功能，然后返回。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-252">It's called terminal middleware because it terminates the search, executes some functionality, and then returns.</span></span>

<span data-ttu-id="2e9d7-253">比较终端中间件和路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-253">Comparing a terminal middleware and routing:</span></span>
* <span data-ttu-id="2e9d7-254">这两种方法都允许终止处理管道：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-254">Both approaches allow terminating the processing pipeline:</span></span>
    * <span data-ttu-id="2e9d7-255">中间件通过返回而不是调用 `next` 来终止管道。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-255">Middleware terminates the pipeline by returning rather than invoking `next`.</span></span>
    * <span data-ttu-id="2e9d7-256">终结点始终是终端。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-256">Endpoints are always terminal.</span></span>
* <span data-ttu-id="2e9d7-257">终端中间件允许在管道中的任意位置放置中间件：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-257">Terminal middleware allows positioning the middleware at an arbitrary place in the pipeline:</span></span>
    * <span data-ttu-id="2e9d7-258">终结点在 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 位置执行。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-258">Endpoints execute at the position of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="2e9d7-259">终端中间件允许任意代码确定中间件匹配的时间：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-259">Terminal middleware allows arbitrary code to determine when the middleware matches:</span></span>
    * <span data-ttu-id="2e9d7-260">自定义路由匹配代码可能比较复杂，且难以正确编写。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-260">Custom route matching code can be verbose and difficult to write correctly.</span></span>
    * <span data-ttu-id="2e9d7-261">路由为典型应用提供了简单的解决方案。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-261">Routing provides straightforward solutions for typical apps.</span></span> <span data-ttu-id="2e9d7-262">大多数应用不需要自定义路由匹配代码。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-262">Most apps don't require custom route matching code.</span></span>
* <span data-ttu-id="2e9d7-263">带有中间件的终结点接口，如 `UseAuthorization` 和 `UseCors`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-263">Endpoints interface with middleware such as `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="2e9d7-264">通过 `UseAuthorization` 或 `UseCors` 使用终端中间件需要与授权系统进行手动交互。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-264">Using a terminal middleware with `UseAuthorization` or `UseCors` requires manual interfacing with the authorization system.</span></span>

<span data-ttu-id="2e9d7-265">[终结点](#endpoint)定义以下两者：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-265">An [endpoint](#endpoint) defines both:</span></span>

* <span data-ttu-id="2e9d7-266">用于处理请求的委托。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-266">A delegate to process requests.</span></span>
* <span data-ttu-id="2e9d7-267">任意元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-267">A collection of arbitrary metadata.</span></span> <span data-ttu-id="2e9d7-268">元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-268">The metadata is used to implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="2e9d7-269">终端中间件可以是一种有效的工具，但可能需要：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-269">Terminal middleware can be an effective tool, but can require:</span></span>

* <span data-ttu-id="2e9d7-270">大量的编码和测试。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-270">A significant amount of coding and testing.</span></span>
* <span data-ttu-id="2e9d7-271">手动与其他系统集成，以实现所需的灵活性级别。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-271">Manual integration with other systems to achieve the desired level of flexibility.</span></span>

<span data-ttu-id="2e9d7-272">请考虑在写入终端中间件之前与路由集成。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-272">Consider integrating with routing before writing a terminal middleware.</span></span>

<span data-ttu-id="2e9d7-273">与 [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) 或 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 相集成的现有终端中间件通常会转换为路由感知终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-273">Existing terminal middleware that integrates with [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) or <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> can usually be turned into a routing aware endpoint.</span></span> <span data-ttu-id="2e9d7-274">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) 演示了路由器软件的模式：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-274">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) demonstrates the pattern for router-ware:</span></span>
* <span data-ttu-id="2e9d7-275">在 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> 上编写扩展方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-275">Write an extension method on <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span>
* <span data-ttu-id="2e9d7-276">使用 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*> 创建嵌套中间件管道。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-276">Create a nested middleware pipeline using <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.</span></span>
* <span data-ttu-id="2e9d7-277">将中间件附加到新管道。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-277">Attach the middleware to the new pipeline.</span></span> <span data-ttu-id="2e9d7-278">在本例中为 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-278">In this case, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.</span></span>
* <span data-ttu-id="2e9d7-279"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> 将中间件管道附加到 <xref:Microsoft.AspNetCore.Http.RequestDelegate>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-279"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> the middleware pipeline into a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="2e9d7-280">调用 `Map` 并提供新的中间件管道。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-280">Call `Map` and provide the new middleware pipeline.</span></span>
* <span data-ttu-id="2e9d7-281">从扩展方法返回由 `Map` 提供的生成器对象。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-281">Return the builder object provided by `Map` from the extension method.</span></span>

<span data-ttu-id="2e9d7-282">下面的代码演示了 [MapHealthChecks](xref:host-and-deploy/health-checks) 的使用方法：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-282">The following code shows use of [MapHealthChecks](xref:host-and-deploy/health-checks):</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

<span data-ttu-id="2e9d7-283">前面的示例说明了为什么返回生成器对象很重要。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-283">The preceding sample shows why returning the builder object is important.</span></span> <span data-ttu-id="2e9d7-284">返回生成器对象后，应用开发者可以配置策略，如终结点的授权。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-284">Returning the builder object allows the app developer to configure policies such as authorization for the endpoint.</span></span> <span data-ttu-id="2e9d7-285">在此示例中，运行状况检查中间件不与授权系统直接集成。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-285">In this example, the health checks middleware has no direct integration with the authorization system.</span></span>

<span data-ttu-id="2e9d7-286">元数据系统的创建目的是为了响应扩展性创建者使用终端中间件时遇到的问题。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-286">The metadata system was created in response to the problems encountered by extensibility authors using terminal middleware.</span></span> <span data-ttu-id="2e9d7-287">对于每个中间件，实现自己与授权系统的集成都会出现问题。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-287">It's problematic for each middleware to implement its own integration with the authorization system.</span></span>

<a name="urlm"></a>

### <a name="url-matching"></a><span data-ttu-id="2e9d7-288">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-288">URL matching</span></span>

* <span data-ttu-id="2e9d7-289">是路由将传入请求匹配到[终结点](#endpoint)的过程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-289">Is the process by which routing matches an incoming request to an [endpoint](#endpoint).</span></span>
* <span data-ttu-id="2e9d7-290">基于 URL 路径中的数据和标头。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-290">Is based on data in the URL path and headers.</span></span>
* <span data-ttu-id="2e9d7-291">可进行扩展，以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-291">Can be extended to consider any data in the request.</span></span>

<span data-ttu-id="2e9d7-292">当路由中间件执行时，它会设置 `Endpoint`，并将值从当前请求路由到 <xref:Microsoft.AspNetCore.Http.HttpContext> 上的[请求功能](xref:fundamentals/request-features)：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-292">When a routing middleware executes, it sets an `Endpoint` and route values to a [request feature](xref:fundamentals/request-features) on the <xref:Microsoft.AspNetCore.Http.HttpContext> from the current request:</span></span>

* <span data-ttu-id="2e9d7-293">调用 [GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) 获取终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-293">Calling [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) gets the endpoint.</span></span>
* <span data-ttu-id="2e9d7-294">`HttpRequest.RouteValues` 将获取路由值的集合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-294">`HttpRequest.RouteValues` gets the collection of route values.</span></span>

<span data-ttu-id="2e9d7-295">在路由中间件之后运行的[中间件](xref:fundamentals/middleware/index)可以检查终结点并采取措施。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-295">[Middleware](xref:fundamentals/middleware/index) running after the routing middleware can inspect the endpoint and take action.</span></span> <span data-ttu-id="2e9d7-296">例如，授权中间件可以在终结点的元数据集合中询问授权策略。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-296">For example, an authorization middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span> <span data-ttu-id="2e9d7-297">请求处理管道中的所有中间件执行后，将调用所选终结点的委托。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-297">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>

<span data-ttu-id="2e9d7-298">终结点路由中的路由系统负责所有的调度决策。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-298">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="2e9d7-299">中间件基于所选终结点应用策略，因此重要的是：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-299">Because the middleware applies policies based on the selected endpoint, it's important that:</span></span>

* <span data-ttu-id="2e9d7-300">任何可能影响发送或安全策略应用的决定都应在路由系统中做出。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-300">Any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

> [!WARNING]
> <span data-ttu-id="2e9d7-301">对于后向兼容性，执行 Controller 或 Razor Pages 终结点委托时，应根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-301">For backwards-compatibility, when a Controller or Razor Pages endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>
>
> <span data-ttu-id="2e9d7-302">在未来的版本中，会将 `RouteContext` 类型标记为已过时：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-302">The `RouteContext` type will be marked obsolete in a future release:</span></span>
>
> * <span data-ttu-id="2e9d7-303">将 `RouteData.Values` 迁移到 `HttpRequest.RouteValues`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-303">Migrate `RouteData.Values` to `HttpRequest.RouteValues`.</span></span>
> * <span data-ttu-id="2e9d7-304">迁移 `RouteData.DataTokens` 以从终结点元数据检索 [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-304">Migrate `RouteData.DataTokens` to retrieve [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) from the endpoint metadata.</span></span>

<span data-ttu-id="2e9d7-305">URL 匹配在可配置的阶段集中运行。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-305">URL matching operates in a configurable set of phases.</span></span> <span data-ttu-id="2e9d7-306">在每个阶段中，输出为一组匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-306">In each phase, the output is a set of matches.</span></span> <span data-ttu-id="2e9d7-307">下一阶段可以进一步缩小这一组匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-307">The set of matches can be narrowed down further by the next phase.</span></span> <span data-ttu-id="2e9d7-308">路由实现不保证匹配终结点的处理顺序。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-308">The routing implementation does not guarantee a processing order for matching endpoints.</span></span> <span data-ttu-id="2e9d7-309">所有可能的匹配项一次性处理。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-309">**All** possible matches are processed at once.</span></span> <span data-ttu-id="2e9d7-310">URL 匹配阶段按以下顺序出现。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-310">The URL matching phases occur in the following order.</span></span> <span data-ttu-id="2e9d7-311">ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-311">ASP.NET Core:</span></span>

1. <span data-ttu-id="2e9d7-312">针对终结点集及其路由模板处理 URL 路径，收集所有匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-312">Processes the URL path against the set of endpoints and their route templates, collecting **all** of the matches.</span></span>
1. <span data-ttu-id="2e9d7-313">采用前面的列表并删除在应用路由约束时失败的匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-313">Takes the preceding list and removes matches that fail with route constraints applied.</span></span>
1. <span data-ttu-id="2e9d7-314">采用前面的列表并删除 [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) 实例集失败的匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-314">Takes the preceding list and removes matches that fail the set of [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) instances.</span></span>
1. <span data-ttu-id="2e9d7-315">使用 [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) 从前面的列表中做出最终决定。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-315">Uses the [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) to make a final decision from the preceding list.</span></span>

<span data-ttu-id="2e9d7-316">根据以下内容设置终结点列表的优先级：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-316">The list of endpoints is prioritized according to:</span></span>

* <span data-ttu-id="2e9d7-317">[RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-317">The [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span></span>
* <span data-ttu-id="2e9d7-318">[路由模板优先顺序](#rtp)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-318">The [route template precedence](#rtp)</span></span>

<span data-ttu-id="2e9d7-319">在每个阶段中处理所有匹配的终结点，直到达到 <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-319">All matching endpoints are processed in each phase until the <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> is reached.</span></span> <span data-ttu-id="2e9d7-320">`EndpointSelector` 是最后一个阶段。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-320">The `EndpointSelector` is the final phase.</span></span> <span data-ttu-id="2e9d7-321">它从匹配项中选择最高优先级终结点作为最佳匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-321">It chooses the highest priority endpoint from the matches as the best match.</span></span> <span data-ttu-id="2e9d7-322">如果存在具有与最佳匹配相同优先级的其他匹配项，则会引发不明确的匹配异常。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-322">If there are other matches with the same priority as the best match, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="2e9d7-323">路由优先顺序基于更具体的路由模板（优先级更高）进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-323">The route precedence is computed based on a **more specific** route template being given a higher priority.</span></span> <span data-ttu-id="2e9d7-324">例如，考虑模板 `/hello` 和 `/{message}`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-324">For example, consider the templates `/hello` and `/{message}`:</span></span>

* <span data-ttu-id="2e9d7-325">两者都匹配 URL 路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-325">Both match the URL path `/hello`.</span></span>
* <span data-ttu-id="2e9d7-326">`/hello` 更具体，因此优先级更高。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-326">`/hello`  is more specific and therefore higher priority.</span></span>

<span data-ttu-id="2e9d7-327">通常，路由优先顺序非常适合为实践操作中使用的各种 URL 方案选择最佳匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-327">In general, route precedence does a good job of choosing the best match for the kinds of URL schemes used in practice.</span></span> <span data-ttu-id="2e9d7-328">仅在必要时才使用 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> 来避免多义性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-328">Use <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> only when necessary to avoid an ambiguity.</span></span>

<span data-ttu-id="2e9d7-329">由于路由提供的扩展性种类，路由系统无法提前计算不明确的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-329">Due to the kinds of extensibility provided by routing, it isn't possible for the routing system to compute ahead of time the ambiguous routes.</span></span> <span data-ttu-id="2e9d7-330">假设有一个示例，例如路由模板 `/{message:alpha}` 和 `/{message:int}`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-330">Consider an example such as the route templates `/{message:alpha}` and `/{message:int}`:</span></span>

* <span data-ttu-id="2e9d7-331">`alpha` 约束仅匹配字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-331">The `alpha` constraint matches only alphabetic characters.</span></span>
* <span data-ttu-id="2e9d7-332">`int` 约束仅匹配数字。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-332">The `int` constraint matches only numbers.</span></span>
* <span data-ttu-id="2e9d7-333">这些模板具有相同的路由优先顺序，但没有两者均匹配的单一 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-333">These templates have the same route precedence, but there's no single URL they both match.</span></span>
* <span data-ttu-id="2e9d7-334">如果路由系统在启动时报告了多义性错误，则会阻止此有效用例。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-334">If the routing system reported an ambiguity error at startup, it would block this valid use case.</span></span>

> [!WARNING]
>
> <span data-ttu-id="2e9d7-335"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 内的操作顺序并不会影响路由行为，但有一个例外。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-335">The order of operations inside <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> doesn't influence the behavior of routing, with one exception.</span></span> <span data-ttu-id="2e9d7-336"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 和 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 会根据调用顺序，自动将顺序值分配给其终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-336"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="2e9d7-337">这会模拟控制器的长时间行为，而无需路由系统提供与早期路由实现相同的保证。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-337">This simulates long-time behavior of controllers without the routing system providing the same guarantees as older routing implementations.</span></span>
>
> <span data-ttu-id="2e9d7-338">在路由的早期实现中，可以实现具有依赖于路由处理顺序的路由扩展性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-338">In the legacy implementation of routing, it's possible to implement routing extensibility that has a dependency on the order in which routes are processed.</span></span> <span data-ttu-id="2e9d7-339">ASP.NET Core 3.0 及更高版本中的终结点路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-339">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>
> 
> * <span data-ttu-id="2e9d7-340">没有路由的概念。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-340">Doesn't have a concept of routes.</span></span>
> * <span data-ttu-id="2e9d7-341">不提供顺序保证。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-341">Doesn't provide ordering guarantees.</span></span> <span data-ttu-id="2e9d7-342">同时处理所有终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-342">All endpoints are processed at once.</span></span>
>
> <span data-ttu-id="2e9d7-343">如果这表示你无法使用旧版路由系统，请[提出 GitHub 问题以获取帮助](https://github.com/dotnet/aspnetcore/issues)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-343">If this means you're stuck using the legacy routing system, [open a GitHub issue for assistance](https://github.com/dotnet/aspnetcore/issues).</span></span>

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a><span data-ttu-id="2e9d7-344">路由模板优先顺序和终结点选择顺序</span><span class="sxs-lookup"><span data-stu-id="2e9d7-344">Route template precedence and endpoint selection order</span></span>

<span data-ttu-id="2e9d7-345">[路由模板优先顺序](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16)是一种系统，该系统根据每个路由模板的具体程度为其分配值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-345">[Route template precedence](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) is a system that assigns each route template a value based on how specific it is.</span></span> <span data-ttu-id="2e9d7-346">路由模板优先顺序：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-346">Route template precedence:</span></span>

* <span data-ttu-id="2e9d7-347">无需在常见情况下调整终结点的顺序。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-347">Avoids the need to adjust the order of endpoints in common cases.</span></span>
* <span data-ttu-id="2e9d7-348">尝试匹配路由行为的常识性预期。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-348">Attempts to match the common-sense expectations of routing behavior.</span></span>

<span data-ttu-id="2e9d7-349">例如，考虑模板 `/Products/List` 和 `/Products/{id}`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-349">For example, consider templates `/Products/List` and `/Products/{id}`.</span></span> <span data-ttu-id="2e9d7-350">我们可合理地假设，对于 URL 路径 `/Products/List`，`/Products/List` 匹配项比 `/Products/{id}` 更好。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-350">It would be reasonable to assume that `/Products/List` is a better match than `/Products/{id}` for the URL path `/Products/List`.</span></span> <span data-ttu-id="2e9d7-351">这种假设合理是因为文本段 `/List` 比参数段 `/{id}` 具有更好的优先顺序。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-351">The works because the literal segment `/List` is considered to have better precedence than the parameter segment `/{id}`.</span></span>

<span data-ttu-id="2e9d7-352">优先顺序工作原理的详细信息与路由模板的定义方式相耦合：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-352">The details of how precedence works are coupled to how route templates are defined:</span></span>

* <span data-ttu-id="2e9d7-353">具有更多段的模板则更具体。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-353">Templates with more segments are considered more specific.</span></span>
* <span data-ttu-id="2e9d7-354">带有文本的段比参数段更具体。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-354">A segment with literal text is considered more specific than a parameter segment.</span></span>
* <span data-ttu-id="2e9d7-355">具有约束的参数段比没有约束的参数段更具体。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-355">A parameter segment with a constraint is considered more specific than one without.</span></span>
* <span data-ttu-id="2e9d7-356">复杂段与具有约束的参数段同样具体。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-356">A complex segment is considered as specific as a parameter segment with a constraint.</span></span>
* <span data-ttu-id="2e9d7-357">catch-all 参数是最不具体的参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-357">Catch-all parameters are the least specific.</span></span> <span data-ttu-id="2e9d7-358">有关 catch-all 路由的重要信息，请参阅 [路由模板参考](#rtr) 中的“catch-all”。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-358">See **catch-all** in the [Route template reference](#rtr) for important information on catch-all routes.</span></span>

<span data-ttu-id="2e9d7-359">有关确切值的参考，请参阅 [GitHub 上的源代码](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-359">See the [source code on GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) for a reference of exact values.</span></span>

<a name="lg"></a>

### <a name="url-generation-concepts"></a><span data-ttu-id="2e9d7-360">URL 生成概念</span><span class="sxs-lookup"><span data-stu-id="2e9d7-360">URL generation concepts</span></span>

<span data-ttu-id="2e9d7-361">URL 生成：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-361">URL generation:</span></span>

* <span data-ttu-id="2e9d7-362">是指路由基于一系列路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-362">Is the process by which routing can create a URL path based on a set of route values.</span></span>
* <span data-ttu-id="2e9d7-363">允许终结点与访问它们的 URL 之间存在逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-363">Allows for a logical separation between endpoints and the URLs that access them.</span></span>

<span data-ttu-id="2e9d7-364">终结点路由包含 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-364">Endpoint routing includes the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API.</span></span> <span data-ttu-id="2e9d7-365">`LinkGenerator` 是 [DI](xref:fundamentals/dependency-injection) 中可用的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-365">`LinkGenerator` is a singleton service available from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2e9d7-366">`LinkGenerator` API 可在执行请求的上下文之外使用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-366">The `LinkGenerator` API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="2e9d7-367">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) 和依赖 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的方案（如[标记帮助程序](xref:mvc/views/tag-helpers/intro)、HTML 帮助程序和[操作结果](xref:mvc/controllers/actions)）在内部使用 `LinkGenerator` API 提供链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-367">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the `LinkGenerator` API internally to provide link generating capabilities.</span></span>

<span data-ttu-id="2e9d7-368">链接生成器基于“地址”和“地址方案”概念 。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-368">The link generator is backed by the concept of an **address** and **address schemes**.</span></span> <span data-ttu-id="2e9d7-369">地址方案是确定哪些终结点用于链接生成的方式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-369">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="2e9d7-370">例如，许多用户熟悉的来自控制器或 Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-370">For example, the route name and route values scenarios many users are familiar with from controllers and Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="2e9d7-371">链接生成器可以通过以下扩展方法链接到控制器或 Razor Pages：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-371">The link generator can link to controllers and Razor Pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="2e9d7-372">这些方法的重载接受包含 `HttpContext` 的参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-372">Overloads of these methods accept arguments that include the `HttpContext`.</span></span> <span data-ttu-id="2e9d7-373">这些方法在功能上等同于 [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) 和 [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)，但提供了更大的灵活性和更多选项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-373">These methods are functionally equivalent to [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) and [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), but offer additional flexibility and options.</span></span>

<span data-ttu-id="2e9d7-374">`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-374">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page`, in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="2e9d7-375">`GetUri*` 方法始终生成包含方案和主机的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-375">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="2e9d7-376">接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-376">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="2e9d7-377">除非重写，否则将使用来自执行请求的[环境](#ambient)路由值、URL 基路径、方案和主机。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-377">The [ambient](#ambient) route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="2e9d7-378">使用地址调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-378"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="2e9d7-379">生成 URI 的过程分两步进行：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-379">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="2e9d7-380">将地址绑定到与地址匹配的终结点列表。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-380">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="2e9d7-381">计算每个终结点的 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern>，直到找到与提供的值匹配的路由模式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-381">Each endpoint's <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="2e9d7-382">输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-382">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="2e9d7-383">对任何类型的地址，<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法均支持标准链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-383">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="2e9d7-384">使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-384">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type:</span></span>

| <span data-ttu-id="2e9d7-385">扩展方法</span><span class="sxs-lookup"><span data-stu-id="2e9d7-385">Extension Method</span></span> | <span data-ttu-id="2e9d7-386">描述</span><span class="sxs-lookup"><span data-stu-id="2e9d7-386">Description</span></span> |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="2e9d7-387">根据提供的值生成具有绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-387">Generates a URI with an absolute path based on the provided values.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="2e9d7-388">根据提供的值生成绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-388">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="2e9d7-389">请注意有关调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列含义：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-389">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="2e9d7-390">对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-390">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="2e9d7-391">如果未验证传入请求的 `Host` 标头，则可能以视图或页面中 URI 的形式将不受信任的请求输入发送回客户端。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-391">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view or page.</span></span> <span data-ttu-id="2e9d7-392">建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-392">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="2e9d7-393">在中间件中将 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-393">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="2e9d7-394">`Map*` 会更改执行请求的基路径，这会影响链接生成的输出。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-394">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="2e9d7-395">所有 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允许指定基路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-395">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="2e9d7-396">指定一个空的基路径来撤消 `Map*` 对链接生成的影响。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-396">Specify an empty base path to undo the `Map*` affect on link generation.</span></span>

### <a name="middleware-example"></a><span data-ttu-id="2e9d7-397">中间件示例</span><span class="sxs-lookup"><span data-stu-id="2e9d7-397">Middleware example</span></span>

<span data-ttu-id="2e9d7-398">在以下示例中，中间件使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 创建列出存储产品的操作方法的链接。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-398">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create a link to an action method that lists store products.</span></span> <span data-ttu-id="2e9d7-399">应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-399">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a><span data-ttu-id="2e9d7-400">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-400">Route template reference</span></span>

<span data-ttu-id="2e9d7-401">如果路由找到匹配项，`{}` 内的令牌定义绑定的路由参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-401">Tokens within `{}` define route parameters that are bound if the route is matched.</span></span> <span data-ttu-id="2e9d7-402">可在路由段中定义多个路由参数，但必须用文本值隔开这些路由参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-402">More than one route parameter can be defined in a route segment, but route parameters  must be separated by a literal value.</span></span> <span data-ttu-id="2e9d7-403">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-403">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span>  <span data-ttu-id="2e9d7-404">路由参数必须具有名称，且可能指定了其他特性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-404">Route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="2e9d7-405">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-405">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="2e9d7-406">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-406">Text matching is case-insensitive and based on the decoded representation of the URL's path.</span></span> <span data-ttu-id="2e9d7-407">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-407">To match a literal route parameter delimiter `{` or `}`, escape the delimiter by repeating the character.</span></span> <span data-ttu-id="2e9d7-408">例如 `{{` 或 `}}`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-408">For example `{{` or `}}`.</span></span>

<span data-ttu-id="2e9d7-409">星号 `*` 或双星号 `**`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-409">Asterisk `*` or double asterisk `**`:</span></span>

* <span data-ttu-id="2e9d7-410">可用作路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-410">Can be used as a prefix to a route parameter to bind to the rest of the URI.</span></span>
* <span data-ttu-id="2e9d7-411">称为 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-411">Are called a **catch-all** parameters.</span></span> <span data-ttu-id="2e9d7-412">例如，`blog/{**slug}`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-412">For example, `blog/{**slug}`:</span></span>
  * <span data-ttu-id="2e9d7-413">匹配以 `/blog` 开头并在其后面包含任何值的任何 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-413">Matches any URI that starts with `/blog` and has any value following it.</span></span>
  * <span data-ttu-id="2e9d7-414">`/blog` 后的值分配给 [slug](https://developer.mozilla.org/docs/Glossary/Slug) 路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-414">The value following `/blog` is assigned to the [slug](https://developer.mozilla.org/docs/Glossary/Slug) route value.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<span data-ttu-id="2e9d7-415">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-415">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="2e9d7-416">使用路由生成 URL（包括路径分隔符 `/`）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-416">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator `/` characters.</span></span> <span data-ttu-id="2e9d7-417">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-417">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="2e9d7-418">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-418">Note the escaped forward slash.</span></span> <span data-ttu-id="2e9d7-419">要往返路径分隔符，请使用 `**` 路由参数前缀。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-419">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="2e9d7-420">`{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-420">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="2e9d7-421">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-421">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="2e9d7-422">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-422">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="2e9d7-423">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-423">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="2e9d7-424">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随 `.` 是可选的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-424">If only a value for `filename` exists in the URL, the route matches because the trailing `.` is  optional.</span></span> <span data-ttu-id="2e9d7-425">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-425">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="2e9d7-426">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-426">Route parameters may have **default values** designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="2e9d7-427">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-427">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="2e9d7-428">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-428">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="2e9d7-429">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-429">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name.</span></span> <span data-ttu-id="2e9d7-430">例如 `id?`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-430">For example, `id?`.</span></span> <span data-ttu-id="2e9d7-431">可选值和默认路由参数之间的差异是：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-431">The difference between optional values and default route parameters is:</span></span>

* <span data-ttu-id="2e9d7-432">具有默认值的路由参数始终生成一个值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-432">A route parameter with a default value always produces a value.</span></span>
* <span data-ttu-id="2e9d7-433">仅当请求 URL 提供值时，可选参数才具有值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-433">An optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="2e9d7-434">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-434">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="2e9d7-435">在路由参数后面添加一个 `:` 和约束名称可指定路由参数上的内联约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-435">Adding `:` and constraint name after the route parameter name specifies an inline constraint on a route parameter.</span></span> <span data-ttu-id="2e9d7-436">如果约束需要参数，将以在约束名称后括在括号 `(...)` 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-436">If the constraint requires arguments, they're enclosed in parentheses `(...)` after the constraint name.</span></span> <span data-ttu-id="2e9d7-437">通过追加另一个 `:` 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-437">Multiple *inline constraints* can be specified by appending another `:` and constraint name.</span></span>

<span data-ttu-id="2e9d7-438">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-438">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="2e9d7-439">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-439">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="2e9d7-440">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-440">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="2e9d7-441">路由参数还可以具有参数转换器。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-441">Route parameters may also have parameter transformers.</span></span> <span data-ttu-id="2e9d7-442">参数转换器在生成链接以及将操作和页面匹配到 URI 时转换参数的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-442">Parameter transformers transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="2e9d7-443">与约束类似，可在路由参数名称后面添加 `:` 和转换器名称，将参数变换器内联添加到路径参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-443">Like constraints, parameter transformers can be added inline to a route parameter by adding a `:` and transformer name after the route parameter name.</span></span> <span data-ttu-id="2e9d7-444">例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-444">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="2e9d7-445">有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-445">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="2e9d7-446">下表演示了示例路由模板及其行为：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-446">The following table demonstrates example route templates and their behavior:</span></span>

| <span data-ttu-id="2e9d7-447">路由模板</span><span class="sxs-lookup"><span data-stu-id="2e9d7-447">Route Template</span></span>                           | <span data-ttu-id="2e9d7-448">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="2e9d7-448">Example Matching URI</span></span>    | <span data-ttu-id="2e9d7-449">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="2e9d7-449">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="2e9d7-450">仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-450">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="2e9d7-451">匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-451">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="2e9d7-452">匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-452">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="2e9d7-453">映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-453">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="2e9d7-454">映射到 `Products` 控制器和 `Details` 操作，并将 `id` 设置为 123。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-454">Maps to the `Products` controller and  `Details` action with`id` set to 123.</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="2e9d7-455">映射到 `Home` 控制器和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-455">Maps to the `Home` controller and `Index` method.</span></span> <span data-ttu-id="2e9d7-456">`id` 将被忽略。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-456">`id` is ignored.</span></span>        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | <span data-ttu-id="2e9d7-457">映射到 `Products` 控制器和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-457">Maps to the `Products` controller and `Index` method.</span></span> <span data-ttu-id="2e9d7-458">`id` 将被忽略。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-458">`id` is ignored.</span></span>        |

<span data-ttu-id="2e9d7-459">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-459">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="2e9d7-460">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-460">Constraints and defaults can also be specified outside the route template.</span></span>

### <a name="complex-segments"></a><span data-ttu-id="2e9d7-461">复杂段</span><span class="sxs-lookup"><span data-stu-id="2e9d7-461">Complex segments</span></span>

<span data-ttu-id="2e9d7-462">复杂段通过[非贪婪](#greedy)的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-462">Complex segments are processed by matching up literal delimiters from right to left in a [non-greedy](#greedy) way.</span></span> <span data-ttu-id="2e9d7-463">例如，`[Route("/a{b}c{d}")]` 是一个复杂段。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-463">For example, `[Route("/a{b}c{d}")]` is a complex segment.</span></span>
<span data-ttu-id="2e9d7-464">复杂段以一种特定的方式工作，必须理解这种方式才能成功地使用它们。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-464">Complex segments work in a particular way that must be understood to use them successfully.</span></span> <span data-ttu-id="2e9d7-465">本部分中的示例演示了为什么复杂段只有在分隔符文本没有出现在参数值中时才真正有效。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-465">The example in this section demonstrates why complex segments only really work well when the delimiter text doesn't appear inside the parameter values.</span></span> <span data-ttu-id="2e9d7-466">对于更复杂的情况，需要使用 [regex](/dotnet/standard/base-types/regular-expressions)，然后手动提取值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-466">Using a [regex](/dotnet/standard/base-types/regular-expressions) and then manually extracting the values is needed for more complex cases.</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="2e9d7-467">这是使用模板 `/a{b}c{d}` 和 URL 路径 `/abcd` 执行路由的步骤摘要。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-467">This is a summary of the steps that routing performs with the template `/a{b}c{d}` and the URL path `/abcd`.</span></span> <span data-ttu-id="2e9d7-468">`|` 有助于可视化算法的工作方式：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-468">The `|` is used to help visualize how the algorithm works:</span></span>

* <span data-ttu-id="2e9d7-469">从右到左的第一个文本是 `c`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-469">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="2e9d7-470">因此从右侧搜索 `/abcd`，并查找 `/ab|c|d`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-470">So `/abcd` is searched from right and finds `/ab|c|d`.</span></span>
* <span data-ttu-id="2e9d7-471">右侧的所有内容 (`d`) 现在都与路由参数 `{d}` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-471">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="2e9d7-472">从右到左的下一个文本是 `a`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-472">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="2e9d7-473">从我们停止的地方开始搜索 `/ab|c|d`，然后 `/|a|b|c|d` 找到 `a`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-473">So `/ab|c|d` is searched starting where we left off, then `a` is found `/|a|b|c|d`.</span></span>
* <span data-ttu-id="2e9d7-474">右侧的值 (`b`) 现在与路由参数 `{b}` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-474">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="2e9d7-475">没有剩余的文本，并且没有剩余的路由模板，因此这是一个匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-475">There is no remaining text and no remaining route template, so this is a match.</span></span>

<span data-ttu-id="2e9d7-476">下面是使用相同模板 `/a{b}c{d}` 和 URL 路径 `/aabcd` 的负面案例示例。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-476">Here's an example of a negative case using the same template `/a{b}c{d}` and the URL path `/aabcd`.</span></span> <span data-ttu-id="2e9d7-477">`|` 有助于可视化算法的工作方式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-477">The `|` is used to help visualize how the algorithm works.</span></span> <span data-ttu-id="2e9d7-478">该案例不是匹配项，可由相同算法解释：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-478">This case isn't a match, which is explained by the same algorithm:</span></span>
* <span data-ttu-id="2e9d7-479">从右到左的第一个文本是 `c`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-479">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="2e9d7-480">因此从右侧搜索 `/aabcd`，并查找 `/aab|c|d`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-480">So `/aabcd` is searched from right and finds `/aab|c|d`.</span></span>
* <span data-ttu-id="2e9d7-481">右侧的所有内容 (`d`) 现在都与路由参数 `{d}` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-481">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="2e9d7-482">从右到左的下一个文本是 `a`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-482">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="2e9d7-483">从我们停止的地方开始搜索 `/aab|c|d`，然后 `/a|a|b|c|d` 找到 `a`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-483">So `/aab|c|d` is searched starting where we left off, then `a` is found `/a|a|b|c|d`.</span></span>
* <span data-ttu-id="2e9d7-484">右侧的值 (`b`) 现在与路由参数 `{b}` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-484">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="2e9d7-485">此时还有剩余的文本 `a`，但是算法已经耗尽了要解析的路由模板，所以这不是一个匹配项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-485">At this point there is remaining text `a`, but the algorithm has run out of route template to parse, so this is not a match.</span></span>

<span data-ttu-id="2e9d7-486">匹配算法是[非贪婪](#greedy)算法：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-486">Since the matching algorithm is [non-greedy](#greedy):</span></span>

* <span data-ttu-id="2e9d7-487">它匹配每个步骤中最小可能文本量。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-487">It matches the smallest amount of text possible in each step.</span></span>
* <span data-ttu-id="2e9d7-488">如果分隔符值出现在参数值内，则会导致不匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-488">Any case where the delimiter value appears inside the parameter values results in not matching.</span></span>

<span data-ttu-id="2e9d7-489">正则表达式可以更好地控制它们的匹配行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-489">Regular expressions provide much more control over their matching behavior.</span></span>

<a name="greedy"></a>

<span data-ttu-id="2e9d7-490">贪婪匹配（也称为[懒惰匹配](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)）匹配最大可能字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-490">Greedy matching, also know as [lazy matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), matches the largest possible string.</span></span> <span data-ttu-id="2e9d7-491">非贪婪匹配最小可能字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-491">Non-greedy matches the smallest possible string.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="2e9d7-492">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-492">Route constraint reference</span></span>

<span data-ttu-id="2e9d7-493">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-493">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="2e9d7-494">路径约束通常检查通过路径模板关联的路径值，并对该值是否为可接受做出对/错决定。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-494">Route constraints generally inspect the route value associated via the route template and make a true or false decision about whether the value is acceptable.</span></span> <span data-ttu-id="2e9d7-495">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-495">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="2e9d7-496">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-496">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="2e9d7-497">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-497">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="2e9d7-498">请勿将约束用于输入验证。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-498">Don't use constraints for input validation.</span></span> <span data-ttu-id="2e9d7-499">如果约束用于输入验证，则无效的输入将导致 `404`（找不到页面）响应。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-499">If constraints are used for input validation, invalid input results in a `404` Not Found response.</span></span> <span data-ttu-id="2e9d7-500">无效输入可能生成包含相应错误消息的 `400` 错误请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-500">Invalid input should produce a `400` Bad Request with an appropriate error message.</span></span> <span data-ttu-id="2e9d7-501">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-501">Route constraints are used to disambiguate similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="2e9d7-502">下表演示示例路由约束及其预期行为：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-502">The following table demonstrates example route constraints and their expected behavior:</span></span>

| <span data-ttu-id="2e9d7-503">约束</span><span class="sxs-lookup"><span data-stu-id="2e9d7-503">constraint</span></span> | <span data-ttu-id="2e9d7-504">示例</span><span class="sxs-lookup"><span data-stu-id="2e9d7-504">Example</span></span> | <span data-ttu-id="2e9d7-505">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="2e9d7-505">Example Matches</span></span> | <span data-ttu-id="2e9d7-506">说明</span><span class="sxs-lookup"><span data-stu-id="2e9d7-506">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="2e9d7-507">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-507">`123456789`, `-123456789`</span></span> | <span data-ttu-id="2e9d7-508">匹配任何整数</span><span class="sxs-lookup"><span data-stu-id="2e9d7-508">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="2e9d7-509">`true`，`FALSE`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-509">`true`, `FALSE`</span></span> | <span data-ttu-id="2e9d7-510">匹配 `true` 或 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-510">Matches `true` or `false`.</span></span> <span data-ttu-id="2e9d7-511">不区分大小写</span><span class="sxs-lookup"><span data-stu-id="2e9d7-511">Case-insensitive</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="2e9d7-512">`2016-12-31`，`2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-512">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="2e9d7-513">在固定区域性中匹配有效的 `DateTime` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-513">Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-514">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-514">See preceding warning.</span></span> |
| `decimal` | `{price:decimal}` | <span data-ttu-id="2e9d7-515">`49.99`，`-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-515">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="2e9d7-516">在固定区域性中匹配有效的 `decimal` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-516">Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-517">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-517">See preceding warning.</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="2e9d7-518">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-518">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="2e9d7-519">在固定区域性中匹配有效的 `double` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-519">Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-520">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-520">See preceding warning.</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="2e9d7-521">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-521">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="2e9d7-522">在固定区域性中匹配有效的 `float` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-522">Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-523">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-523">See preceding warning.</span></span>|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | <span data-ttu-id="2e9d7-524">匹配有效的 `Guid` 值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-524">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="2e9d7-525">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-525">`123456789`, `-123456789`</span></span> | <span data-ttu-id="2e9d7-526">匹配有效的 `long` 值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-526">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="2e9d7-527">字符串必须至少为 4 个字符</span><span class="sxs-lookup"><span data-stu-id="2e9d7-527">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | <span data-ttu-id="2e9d7-528">字符串不得超过 8 个字符</span><span class="sxs-lookup"><span data-stu-id="2e9d7-528">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="2e9d7-529">字符串必须正好为 12 个字符</span><span class="sxs-lookup"><span data-stu-id="2e9d7-529">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="2e9d7-530">字符串必须至少为 8 个字符，且不得超过 16 个字符</span><span class="sxs-lookup"><span data-stu-id="2e9d7-530">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="2e9d7-531">整数值必须至少为 18</span><span class="sxs-lookup"><span data-stu-id="2e9d7-531">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="2e9d7-532">整数值不得超过 120</span><span class="sxs-lookup"><span data-stu-id="2e9d7-532">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="2e9d7-533">整数值必须至少为 18，且不得超过 120</span><span class="sxs-lookup"><span data-stu-id="2e9d7-533">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="2e9d7-534">字符串必须由一个或多个字母字符组成，`a`-`z`，并区分大小写。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-534">String must consist of one or more alphabetical characters, `a`-`z` and case-insensitive.</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="2e9d7-535">字符串必须与正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-535">String must match the regular expression.</span></span> <span data-ttu-id="2e9d7-536">请参阅有关定义正则表达式的提示。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-536">See tips about defining a regular expression.</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="2e9d7-537">用于强制在 URL 生成过程中存在非参数值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-537">Used to enforce that a non-parameter value is present during URL generation</span></span> |

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="2e9d7-538">可向单个参数应用多个用冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-538">Multiple, colon delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="2e9d7-539">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-539">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="2e9d7-540">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-540">Route constraints that verify the URL and are converted to a CLR type always use the invariant culture.</span></span> <span data-ttu-id="2e9d7-541">例如，转换为 CLR 类型 `int` 或 `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-541">For example, conversion to the CLR type `int` or `DateTime`.</span></span> <span data-ttu-id="2e9d7-542">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-542">These constraints assume that the URL is not localizable.</span></span> <span data-ttu-id="2e9d7-543">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-543">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="2e9d7-544">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-544">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="2e9d7-545">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-545">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

### <a name="regular-expressions-in-constraints"></a><span data-ttu-id="2e9d7-546">约束中的正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-546">Regular expressions in constraints</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="2e9d7-547">使用 `regex(...)` 路由约束可以将正则表达式指定为内联约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-547">Regular expressions can be specified as inline constraints using the `regex(...)` route constraint.</span></span> <span data-ttu-id="2e9d7-548"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 系列中的方法还接受约束的对象文字。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-548">Methods in the <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> family also accept an object literal of constraints.</span></span> <span data-ttu-id="2e9d7-549">如果使用该窗体，则字符串值将解释为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-549">If that form is used, string values are interpreted as regular expressions.</span></span>

<span data-ttu-id="2e9d7-550">下面的代码使用内联 regex 约束：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-550">The following code uses an inline regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

<span data-ttu-id="2e9d7-551">下面的代码使用对象文字来指定 regex 约束：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-551">The following code uses an object literal to specify a regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

<span data-ttu-id="2e9d7-552">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-552">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="2e9d7-553">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-553">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="2e9d7-554">正则表达式与路由和 C# 语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-554">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="2e9d7-555">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-555">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="2e9d7-556">若要在内联约束中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，请使用以下某项：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-556">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in an inline constraint, use one of the following:</span></span>

* <span data-ttu-id="2e9d7-557">将字符串中提供的 `\` 字符替换为 C# 源文件中的 `\\` 字符，以便对 `\` 字符串转义字符进行转义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-557">Replace `\` characters provided in the string as `\\` characters in the C# source file in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="2e9d7-558">[逐字字符串文本](/dotnet/csharp/language-reference/keywords/string)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-558">[Verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="2e9d7-559">要对路由参数分隔符 `{`、`}`、`[`、`]` 进行转义，请将表达式（例如 `{{`、`}}`、`[[`、`]]`）中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-559">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression, for example, `{{`, `}}`, `[[`, `]]`.</span></span> <span data-ttu-id="2e9d7-560">下表展示了正则表达式及其转义的版本：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-560">The following table shows a regular expression and its escaped version:</span></span>

| <span data-ttu-id="2e9d7-561">正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-561">Regular expression</span></span>    | <span data-ttu-id="2e9d7-562">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-562">Escaped regular expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="2e9d7-563">路由中使用的正则表达式通常以 `^` 字符开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-563">Regular expressions used in routing often start with the `^` character and match the starting position of the string.</span></span> <span data-ttu-id="2e9d7-564">表达式通常以 `$` 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-564">The expressions often end with the `$` character and match the end of the string.</span></span> <span data-ttu-id="2e9d7-565">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-565">The `^` and `$` characters ensure that the regular expression matches the entire route parameter value.</span></span> <span data-ttu-id="2e9d7-566">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-566">Without the `^` and `$` characters, the regular expression matches any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="2e9d7-567">下表提供了示例并说明了它们匹配或匹配失败的原因：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-567">The following table provides examples and explains why they match or fail to match:</span></span>

| <span data-ttu-id="2e9d7-568">表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-568">Expression</span></span>   | <span data-ttu-id="2e9d7-569">String</span><span class="sxs-lookup"><span data-stu-id="2e9d7-569">String</span></span>    | <span data-ttu-id="2e9d7-570">匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-570">Match</span></span> | <span data-ttu-id="2e9d7-571">注释</span><span class="sxs-lookup"><span data-stu-id="2e9d7-571">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-572">hello</span><span class="sxs-lookup"><span data-stu-id="2e9d7-572">hello</span></span>     | <span data-ttu-id="2e9d7-573">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-573">Yes</span></span>   | <span data-ttu-id="2e9d7-574">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-574">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-575">123abc456</span><span class="sxs-lookup"><span data-stu-id="2e9d7-575">123abc456</span></span> | <span data-ttu-id="2e9d7-576">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-576">Yes</span></span>   | <span data-ttu-id="2e9d7-577">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-577">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-578">mz</span><span class="sxs-lookup"><span data-stu-id="2e9d7-578">mz</span></span>        | <span data-ttu-id="2e9d7-579">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-579">Yes</span></span>   | <span data-ttu-id="2e9d7-580">匹配表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-580">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-581">MZ</span><span class="sxs-lookup"><span data-stu-id="2e9d7-581">MZ</span></span>        | <span data-ttu-id="2e9d7-582">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-582">Yes</span></span>   | <span data-ttu-id="2e9d7-583">不区分大小写</span><span class="sxs-lookup"><span data-stu-id="2e9d7-583">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="2e9d7-584">hello</span><span class="sxs-lookup"><span data-stu-id="2e9d7-584">hello</span></span>     | <span data-ttu-id="2e9d7-585">否</span><span class="sxs-lookup"><span data-stu-id="2e9d7-585">No</span></span>    | <span data-ttu-id="2e9d7-586">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-586">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="2e9d7-587">123abc456</span><span class="sxs-lookup"><span data-stu-id="2e9d7-587">123abc456</span></span> | <span data-ttu-id="2e9d7-588">否</span><span class="sxs-lookup"><span data-stu-id="2e9d7-588">No</span></span>    | <span data-ttu-id="2e9d7-589">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-589">See `^` and `$` above</span></span> |

<span data-ttu-id="2e9d7-590">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-590">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="2e9d7-591">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-591">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="2e9d7-592">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-592">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="2e9d7-593">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-593">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="2e9d7-594">已传递到约束字典且不匹配任何已知约束的约束也将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-594">Constraints that are passed in the constraints dictionary that don't match one of the known constraints are also treated as regular expressions.</span></span> <span data-ttu-id="2e9d7-595">模板内传递且不匹配任何已知约束的约束将不被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-595">Constraints that are passed  within a template that don't match one of the known constraints are not treated as regular expressions.</span></span>

### <a name="custom-route-constraints"></a><span data-ttu-id="2e9d7-596">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="2e9d7-596">Custom route constraints</span></span>

<span data-ttu-id="2e9d7-597">可实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-597">Custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="2e9d7-598">`IRouteConstraint` 接口包含 <xref:System.Web.Routing.IRouteConstraint.Match*>，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-598">The `IRouteConstraint` interface contains <xref:System.Web.Routing.IRouteConstraint.Match*>, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="2e9d7-599">很少需要自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-599">Custom route constraints are rarely needed.</span></span> <span data-ttu-id="2e9d7-600">在实现自定义路由约束之前，请考虑替代方法，如模型绑定。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-600">Before implementing a custom route constraint, consider alternatives, such as model binding.</span></span>

<span data-ttu-id="2e9d7-601">ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) 文件夹提供了创建约束的经典示例。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-601">The ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) folder provides good examples of creating a constraints.</span></span> <span data-ttu-id="2e9d7-602">例如 [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-602">For example, [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).</span></span>

<span data-ttu-id="2e9d7-603">若要使用自定义 `IRouteConstraint`，必须在服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-603">To use a custom `IRouteConstraint`, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the service container.</span></span> <span data-ttu-id="2e9d7-604">`ConstraintMap` 是将路由约束键映射到验证这些约束的 `IRouteConstraint` 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-604">A `ConstraintMap` is a dictionary that maps route constraint keys to `IRouteConstraint` implementations that validate those constraints.</span></span> <span data-ttu-id="2e9d7-605">应用的 `ConstraintMap` 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-605">An app's `ConstraintMap` can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="2e9d7-606">例如：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-606">For example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

<span data-ttu-id="2e9d7-607">前面的约束应用于以下代码：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-607">The preceding constraint is applied in the following code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<span data-ttu-id="2e9d7-608">实现 `MyCustomConstraint` 可防止将 `0` 应用于路由参数：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-608">The implementation of `MyCustomConstraint` prevents `0` being applied to a route parameter:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="2e9d7-609">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-609">The preceding code:</span></span>

* <span data-ttu-id="2e9d7-610">阻止路由的 `{id}` 段中的 `0`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-610">Prevents `0` in the `{id}` segment of the route.</span></span>
* <span data-ttu-id="2e9d7-611">显示以提供实现自定义约束的基本示例。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-611">Is shown to provide a basic example of implementing a custom constraint.</span></span> <span data-ttu-id="2e9d7-612">不应在产品应用中使用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-612">It should not be used in a production app.</span></span>

<span data-ttu-id="2e9d7-613">下面的代码是防止处理包含 `0` 的 `id` 的更好方法：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-613">The following code is a better approach to preventing an `id` containing a `0` from being processed:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="2e9d7-614">前面的代码与 `MyCustomConstraint` 方法相比具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-614">The preceding code has the following advantages over the `MyCustomConstraint` approach:</span></span>

* <span data-ttu-id="2e9d7-615">它不需要自定义约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-615">It doesn't require a custom constraint.</span></span>
* <span data-ttu-id="2e9d7-616">当路由参数包括 `0` 时，它将返回更具描述性的错误。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-616">It returns a more descriptive error when the route parameter includes `0`.</span></span>

## <a name="parameter-transformer-reference"></a><span data-ttu-id="2e9d7-617">参数转换器参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-617">Parameter transformer reference</span></span>

<span data-ttu-id="2e9d7-618">参数转换器：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-618">Parameter transformers:</span></span>

* <span data-ttu-id="2e9d7-619">使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 生成链接时执行。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-619">Execute when generating a link using <xref:Microsoft.AspNetCore.Routing.LinkGenerator>.</span></span>
* <span data-ttu-id="2e9d7-620">实现 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-620">Implement <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.</span></span>
* <span data-ttu-id="2e9d7-621">使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-621">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="2e9d7-622">获取参数的路由值并将其转换为新的字符串值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-622">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="2e9d7-623">在生成的链接中使用转换后的值的结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-623">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="2e9d7-624">例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-624">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="2e9d7-625">请考虑以下 `IOutboundParameterTransformer` 实现：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-625">Consider the following `IOutboundParameterTransformer` implementation:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

<span data-ttu-id="2e9d7-626">若要在路由模式中使用参数转换器，请在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-626">To use a parameter transformer in a route pattern, configure it using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

<span data-ttu-id="2e9d7-627">ASP.NET Core 框架使用参数转化器来转换进行终结点解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-627">The ASP.NET Core framework uses parameter transformers to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="2e9d7-628">例如，参数转换器转换用于匹配 `area`、`controller`、`action` 和 `page` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-628">For example, parameter transformers transform the route values used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="2e9d7-629">使用上述路由模板，可将操作 `SubscriptionManagementController.GetAll` 与 URI `/subscription-management/get-all` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-629">With the preceding route template, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="2e9d7-630">参数转换器不会更改用于生成链接的路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-630">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="2e9d7-631">例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-631">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="2e9d7-632">对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-632">ASP.NET Core provides API conventions for using parameter transformers with generated routes:</span></span>

* <span data-ttu-id="2e9d7-633"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC 约定将指定的参数转换器应用于应用中的所有特性路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-633">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="2e9d7-634">在替换属性路径令牌时，参数转换器将转换这些令牌。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-634">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="2e9d7-635">有关详细信息，请参阅[使用参数转换器自定义标记替换](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-635">For more information, see [Use a parameter transformer to customize token replacement](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="2e9d7-636">Razor Pages 使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API 约定。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-636">Razor Pages uses the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API convention.</span></span> <span data-ttu-id="2e9d7-637">此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-637">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="2e9d7-638">参数转换器转换 Razor Pages 路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-638">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="2e9d7-639">有关详细信息，请参阅[使用参数转换器自定义页面路由](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-639">For more information, see [Use a parameter transformer to customize page routes](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

<a name="ugr"></a>

## <a name="url-generation-reference"></a><span data-ttu-id="2e9d7-640">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-640">URL generation reference</span></span>

<span data-ttu-id="2e9d7-641">本部分包含 URL 生成实现的算法的参考。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-641">This section contains a reference for the algorithm implemented by URL generation.</span></span> <span data-ttu-id="2e9d7-642">在实践中，最复杂的 URL 生成示例使用控制器或 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-642">In practice, most complex examples of URL generation use controllers or Razor Pages.</span></span> <span data-ttu-id="2e9d7-643">有关其他信息，请参阅[控制器中的路由](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-643">See  [routing in controllers](xref:mvc/controllers/routing) for additional information.</span></span>

<span data-ttu-id="2e9d7-644">URL 生成过程首先调用 [GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) 或类似方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-644">The URL generation process begins with a call to [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) or a similar method.</span></span> <span data-ttu-id="2e9d7-645">此方法提供了一个地址、一组路由值以及有关 `HttpContext` 中当前请求的可选信息。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-645">The method is provided with an address, a set of route values, and optionally information about the current request from `HttpContext`.</span></span>

<span data-ttu-id="2e9d7-646">第一步是使用地址解析一组候选终结点，该终结点使用与该地址类型匹配的 [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-646">The first step is to use the address to resolve a set of candidate endpoints using an [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) that matches the address's type.</span></span>

<span data-ttu-id="2e9d7-647">地址方案找到一组候选项后，就会以迭代方式对终结点进行排序和处理，直到 URL 生成操作成功。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-647">Once of set of candidates is found by the address scheme, the endpoints are ordered and processed iteratively until a URL generation operation succeeds.</span></span> <span data-ttu-id="2e9d7-648">URL 生成不检查多义性，返回的第一个结果就是最终结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-648">URL generation does **not** check for ambiguities, the first result returned is the final result.</span></span>

### <a name="troubleshooting-url-generation-with-logging"></a><span data-ttu-id="2e9d7-649">使用日志记录对 URL 生成进行故障排除</span><span class="sxs-lookup"><span data-stu-id="2e9d7-649">Troubleshooting URL generation with logging</span></span>

<span data-ttu-id="2e9d7-650">对 URL 生成进行故障排除的第一步是将 `Microsoft.AspNetCore.Routing` 的日志记录级别设置为 `TRACE`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-650">The first step in troubleshooting URL generation is setting the logging level of `Microsoft.AspNetCore.Routing` to `TRACE`.</span></span> <span data-ttu-id="2e9d7-651">`LinkGenerator` 记录有关其处理的许多详细信息，有助于排查问题。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-651">`LinkGenerator` logs many details about its processing which can be useful to troubleshoot problems.</span></span>

<span data-ttu-id="2e9d7-652">有关 URL 生成的详细信息，请参阅 [URL 生成参考](#ugr)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-652">See [URL generation reference](#ugr) for details on URL generation.</span></span>

### <a name="addresses"></a><span data-ttu-id="2e9d7-653">地址</span><span class="sxs-lookup"><span data-stu-id="2e9d7-653">Addresses</span></span>

<span data-ttu-id="2e9d7-654">地址是 URL 生成中的概念，用于将链接生成器中的调用绑定到一组终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-654">Addresses are the concept in URL generation used to bind a call into the link generator to a set of candidate endpoints.</span></span>

<span data-ttu-id="2e9d7-655">地址是一个可扩展的概念，默认情况下有两种实现：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-655">Addresses are an extensible concept that come with two implementations by default:</span></span>

* <span data-ttu-id="2e9d7-656">使用终结点名称 (`string`) 作为地址：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-656">Using *endpoint name* (`string`) as the address:</span></span>
    * <span data-ttu-id="2e9d7-657">提供与 MVC 的路由名称相似的功能。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-657">Provides similar functionality to MVC's route name.</span></span>
    * <span data-ttu-id="2e9d7-658">使用 <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> 元数据类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-658">Uses the <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> metadata type.</span></span>
    * <span data-ttu-id="2e9d7-659">根据所有注册的终结点的元数据解析提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-659">Resolves the provided string against the metadata of all registered endpoints.</span></span>
    * <span data-ttu-id="2e9d7-660">如果多个终结点使用相同的名称，启动时会引发异常。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-660">Throws an exception on startup if multiple endpoints use the same name.</span></span>
    * <span data-ttu-id="2e9d7-661">建议用于控制器和 Razor Pages 之外的常规用途。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-661">Recommended for general-purpose use outside of controllers and Razor Pages.</span></span>
* <span data-ttu-id="2e9d7-662">使用路由值 (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) 作为地址：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-662">Using *route values* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) as the address:</span></span>
    * <span data-ttu-id="2e9d7-663">提供与控制器和 Razor Pages 生成旧 URL 相同的功能。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-663">Provides similar functionality to controllers and Razor Pages legacy URL generation.</span></span>
    * <span data-ttu-id="2e9d7-664">扩展和调试非常复杂。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-664">Very complex to extend and debug.</span></span>
    * <span data-ttu-id="2e9d7-665">提供 `IUrlHelper`、标记帮助程序、HTML 帮助程序、操作结果等所使用的实现。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-665">Provides the implementation used by `IUrlHelper`, Tag Helpers, HTML Helpers, Action Results, etc.</span></span>

<span data-ttu-id="2e9d7-666">地址方案的作用是根据任意条件在地址和匹配终结点之间建立关联：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-666">The role of the address scheme is to make the association between the address and matching endpoints by arbitrary criteria:</span></span>

* <span data-ttu-id="2e9d7-667">终结点名称方案执行基本字典查找。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-667">The endpoint name scheme performs a basic dictionary lookup.</span></span>
* <span data-ttu-id="2e9d7-668">路由值方案有一个复杂的子集算法，这是集算法的最佳子集。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-668">The route values scheme has a complex best subset of set algorithm.</span></span>

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a><span data-ttu-id="2e9d7-669">环境值和显式值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-669">Ambient values and explicit values</span></span>

<span data-ttu-id="2e9d7-670">从当前请求开始，路由将访问当前请求 `HttpContext.Request.RouteValues` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-670">From the current request, routing accesses the route values of the current request `HttpContext.Request.RouteValues`.</span></span> <span data-ttu-id="2e9d7-671">与当前请求关联的值称为环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-671">The values associated with the current request are referred to as the **ambient values**.</span></span> <span data-ttu-id="2e9d7-672">为清楚起见，文档是指作为显式值传入方法的路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-672">For the purpose of clarity, the documentation refers to the route values passed in to methods as **explicit values**.</span></span>

<span data-ttu-id="2e9d7-673">下面的示例演示了环境值和显式值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-673">The following example shows ambient values and explicit values.</span></span> <span data-ttu-id="2e9d7-674">它将提供当前请求中的环境值和显式值：`{ id = 17, }`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-674">It provides ambient values from the current request and explicit values: `{ id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

<span data-ttu-id="2e9d7-675">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-675">The preceding code:</span></span>

* <span data-ttu-id="2e9d7-676">返回 `/Widget/Index/17`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-676">Returns `/Widget/Index/17`</span></span>
* <span data-ttu-id="2e9d7-677">通过 [DI](xref:fundamentals/dependency-injection) 获取 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-677">Gets <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2e9d7-678">下面的代码不提供环境值和显式值：`{ controller = "Home", action = "Subscribe", id = 17, }`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-678">The following code provides no ambient values and explicit values: `{ controller = "Home", action = "Subscribe", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

<span data-ttu-id="2e9d7-679">前面的方法返回 `/Home/Subscribe/17`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-679">The preceding  method returns `/Home/Subscribe/17`</span></span>

<span data-ttu-id="2e9d7-680">`WidgetController` 中的下列代码返回 `/Widget/Subscribe/17`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-680">The following code in the `WidgetController` returns `/Widget/Subscribe/17`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

<span data-ttu-id="2e9d7-681">下面的代码提供当前请求中的环境值和显式值中的控制器：`{ action = "Edit", id = 17, }`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-681">The following code provides the controller from ambient values in the current request and explicit values: `{ action = "Edit", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

<span data-ttu-id="2e9d7-682">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-682">In the preceding code:</span></span>

* <span data-ttu-id="2e9d7-683">返回 `/Gadget/Edit/17`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-683">`/Gadget/Edit/17` is returned.</span></span>
* <span data-ttu-id="2e9d7-684"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> 获取 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-684"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> gets the <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.</span></span>
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
<span data-ttu-id="2e9d7-685">生成一个 URL，其中包含操作方法的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-685">generates a URL with an absolute path for an action method.</span></span> <span data-ttu-id="2e9d7-686">URL 包含指定的 `action` 名称和 `route` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-686">The URL contains the specified `action` name and `route` values.</span></span>

<span data-ttu-id="2e9d7-687">下面的代码提供当前请求中的环境值和显式值：`{ page = "./Edit, id = 17, }`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-687">The following code provides ambient values from the current request and explicit values: `{ page = "./Edit, id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2e9d7-688">当“编辑 Razor”页包含以下页面指令时，前面的代码会将 `url` 设置为 `/Edit/17`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-688">The preceding code sets `url` to  `/Edit/17` when the Edit Razor Page contains the following page directive:</span></span>

 `@page "{id:int}"`

<span data-ttu-id="2e9d7-689">如果“编辑”页面不包含 `"{id:int}"` 路由模板，`url` 为 `/Edit?id=17`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-689">If the Edit page doesn't contain the `"{id:int}"` route template, `url` is `/Edit?id=17`.</span></span>

<span data-ttu-id="2e9d7-690">除了此处所述的规则外，MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的行为还增加了一层复杂性：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-690">The behavior of MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> adds a layer of complexity in addition to the rules described here:</span></span>

* <span data-ttu-id="2e9d7-691">`IUrlHelper` 始终将当前请求中的路由值作为环境值提供。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-691">`IUrlHelper` always provides the route values from the current request as ambient values.</span></span>
* <span data-ttu-id="2e9d7-692">[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) 始终将当前 `action` 和 `controller` 路由值复制为显式值，除非由开发者重写。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-692">[IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) always copies the current `action` and `controller` route values as explicit values unless overridden by the developer.</span></span>
* <span data-ttu-id="2e9d7-693">[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) 始终将当前 `page` 路由值复制为显式值，除非重写。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-693">[IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) always copies the current `page` route value as an explicit value unless overridden.</span></span> <!--by the user-->
* <span data-ttu-id="2e9d7-694">`IUrlHelper.Page` 始终替代当前 `handler` 路由值，且 `null` 为显式值，除非重写。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-694">`IUrlHelper.Page` always overrides the current `handler` route value with `null` as an explicit values unless overridden.</span></span>

<span data-ttu-id="2e9d7-695">用户常常对环境值的行为详细信息感到惊讶，因为 MVC 似乎不遵循自己的规则。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-695">Users are often surprised by the behavioral details of ambient values, because MVC doesn't seem to follow its own rules.</span></span> <span data-ttu-id="2e9d7-696">出于历史和兼容性原因，某些路由值（例如 `action`、`controller`、`page` 和 `handler`）具有其自己的特殊行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-696">For historical and compatibility reasons, certain route values such as `action`, `controller`, `page`, and `handler` have their own special-case behavior.</span></span>

<span data-ttu-id="2e9d7-697">`LinkGenerator.GetPathByAction` 和 `LinkGenerator.GetPathByPage` 提供的等效功能与 `IUrlHelper` 的兼容性异常相同。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-697">The equivalent functionality provided by `LinkGenerator.GetPathByAction` and `LinkGenerator.GetPathByPage` duplicates these anomalies of `IUrlHelper` for compatibility.</span></span>

### <a name="url-generation-process"></a><span data-ttu-id="2e9d7-698">URL 生成过程</span><span class="sxs-lookup"><span data-stu-id="2e9d7-698">URL generation process</span></span>

<span data-ttu-id="2e9d7-699">找到候选终结点集后，URL 生成算法将：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-699">Once the set of candidate endpoints are found, the URL generation algorithm:</span></span>

* <span data-ttu-id="2e9d7-700">以迭代方式处理终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-700">Processes the endpoints iteratively.</span></span>
* <span data-ttu-id="2e9d7-701">返回第一个成功的结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-701">Returns the first successful result.</span></span>

<span data-ttu-id="2e9d7-702">此过程的第一步称为路由值失效。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-702">The first step in this process is called **route value invalidation**.</span></span>  <span data-ttu-id="2e9d7-703">路由值失效是路由决定应使用环境值中的哪些路由值以及应忽略哪些路由值的过程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-703">Route value invalidation is the process by which routing decides which route values from the ambient values should be used and which should be ignored.</span></span> <span data-ttu-id="2e9d7-704">将考虑每个环境值，要么与显式值组合，要么忽略它们。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-704">Each ambient value is considered and either combined with the explicit values, or ignored.</span></span>

<span data-ttu-id="2e9d7-705">考虑环境值角色的最佳方式是在某些常见情况下，尝试保存应用程序开发者的键入内容。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-705">The best way to think about the role of ambient values is that they attempt to save application developers typing, in some common cases.</span></span> <span data-ttu-id="2e9d7-706">就传统意义而言，环境值非常有用的情况与 MVC 相关：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-706">Traditionally, the scenarios where ambient values are helpful are related to MVC:</span></span>

* <span data-ttu-id="2e9d7-707">链接到同一控制器中的其他操作时，不需要指定控制器名称。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-707">When linking to another action in the same controller, the controller name doesn't need to be specified.</span></span>
* <span data-ttu-id="2e9d7-708">链接到同一区域中的另一个控制器时，不需要指定区域名称。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-708">When linking to another controller in the same area, the area name doesn't need to be specified.</span></span>
* <span data-ttu-id="2e9d7-709">链接到相同的操作方法时，不需要指定路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-709">When linking to the same action method, route values don't need to be specified.</span></span>
* <span data-ttu-id="2e9d7-710">链接到应用的其他部分时，不需要在应用的该部分中传递无意义的路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-710">When linking to another part of the app, you don't want to carry over route values that have no meaning in that part of the app.</span></span>

<span data-ttu-id="2e9d7-711">如果不了解路由值失效，通常就会导致对返回 `null` 的 `LinkGenerator` 或 `IUrlHelper` 执行调用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-711">Calls to `LinkGenerator` or `IUrlHelper` that return `null` are usually caused by not understanding route value invalidation.</span></span> <span data-ttu-id="2e9d7-712">显式指定更多路由值，对路由值失效进行故障排除，以查看是否解决了问题。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-712">Troubleshoot route value invalidation by explicitly specifying more of the route values to see if that solves the problem.</span></span>

<span data-ttu-id="2e9d7-713">路由值失效的前提是应用的 URL 方案是分层的，并按从左到右的顺序组成层次结构。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-713">Route value invalidation works on the assumption that the app's URL scheme is hierarchical, with a hierarchy formed from left-to-right.</span></span> <span data-ttu-id="2e9d7-714">请考虑基本控制器路由模板 `{controller}/{action}/{id?}`，以直观地了解实践操作中该模板的工作方式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-714">Consider the basic controller route template `{controller}/{action}/{id?}` to get an intuitive sense of how this works in practice.</span></span> <span data-ttu-id="2e9d7-715">对值进行更改会使右侧显示的所有路由值都失效 。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-715">A **change** to a value **invalidates** all of the route values that appear to the right.</span></span> <span data-ttu-id="2e9d7-716">这反映了关于层次结构的假设。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-716">This reflects the assumption about hierarchy.</span></span> <span data-ttu-id="2e9d7-717">如果应用的 `id` 有一个环境值，则操作会为 `controller` 指定一个不同的值：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-717">If the app has an ambient value for `id`, and the operation specifies a different value for the `controller`:</span></span>

* <span data-ttu-id="2e9d7-718">`id` 不会重复使用，因为 `{controller}` 在 `{id?}` 的左侧。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-718">`id` won't be reused because `{controller}` is to the left of `{id?}`.</span></span>

<span data-ttu-id="2e9d7-719">演示此原则的一些示例如下：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-719">Some examples demonstrating this principle:</span></span>

* <span data-ttu-id="2e9d7-720">如果显式值包含 `id` 的值，则将忽略 `id` 的环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-720">If the explicit values contain a value for `id`, the ambient value for `id` is ignored.</span></span> <span data-ttu-id="2e9d7-721">可以使用 `controller` 和 `action` 的环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-721">The ambient values for `controller` and `action` can be used.</span></span>
* <span data-ttu-id="2e9d7-722">如果显式值包含 `action` 的值，则将忽略 `action` 的任何环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-722">If the explicit values contain a value for `action`, any ambient value for `action` is ignored.</span></span> <span data-ttu-id="2e9d7-723">可以使用 `controller` 的环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-723">The ambient values for `controller` can be used.</span></span> <span data-ttu-id="2e9d7-724">如果 `action` 的显式值不同于 `action` 的环境值，则不会使用 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-724">If the explicit value for `action` is different from the ambient value for `action`, the `id` value won't be used.</span></span>  <span data-ttu-id="2e9d7-725">如果 `action` 的显式值与 `action` 的环境值相同，则可以使用 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-725">If the explicit value for `action` is the same as the ambient value for `action`, the `id` value can be used.</span></span>
* <span data-ttu-id="2e9d7-726">如果显式值包含 `controller` 的值，则将忽略 `controller` 的任何环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-726">If the explicit values contain a value for `controller`, any ambient value for `controller` is ignored.</span></span> <span data-ttu-id="2e9d7-727">如果 `controller` 的显式值不同于 `controller` 的环境值，则不会使用 `action` 和 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-727">If the explicit value for `controller` is different from the ambient value for `controller`, the `action` and `id` values won't be used.</span></span> <span data-ttu-id="2e9d7-728">如果 `controller` 的显式值与 `controller` 的环境值相同，则可以使用 `action` 和 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-728">If the explicit value for `controller` is the same as the ambient value for `controller`, the `action` and `id` values can be used.</span></span>

<span data-ttu-id="2e9d7-729">由于存在特性路由和专用传统路由，此过程将变得更加复杂。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-729">This process is further complicated by the existence of attribute routes and dedicated conventional routes.</span></span> <span data-ttu-id="2e9d7-730">控制器传统路由（例如 `{controller}/{action}/{id?}`）使用路由参数指定层次结构。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-730">Controller conventional routes such as `{controller}/{action}/{id?}` specify a hierarchy using route parameters.</span></span> <span data-ttu-id="2e9d7-731">对于控制器和 Razor Pages 的[专用传统路由](xref:mvc/controllers/routing#dcr)和[特性路由](xref:mvc/controllers/routing#ar)：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-731">For [dedicated conventional routes](xref:mvc/controllers/routing#dcr) and [attribute routes](xref:mvc/controllers/routing#ar) to controllers and Razor Pages:</span></span>

* <span data-ttu-id="2e9d7-732">有一个路由值层次结构。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-732">There is a hierarchy of route values.</span></span>
* <span data-ttu-id="2e9d7-733">它们不会出现在模板中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-733">They don't appear in the template.</span></span>

<span data-ttu-id="2e9d7-734">对于这些情况，URL 生成将定义“所需值”这一概念。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-734">For these cases, URL generation defines the **required values** concept.</span></span> <span data-ttu-id="2e9d7-735">而由控制器和 Razor Pages 创建的终结点将指定所需值，以允许路由值失效起作用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-735">Endpoints created by controllers and Razor Pages have required values specified that allow route value invalidation to work.</span></span>

<span data-ttu-id="2e9d7-736">路由值失效算法的详细信息：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-736">The route value invalidation algorithm in detail:</span></span>

* <span data-ttu-id="2e9d7-737">所需值名称与路由参数组合在一起，然后从左到右进行处理。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-737">The required value names are combined with the route parameters, then processed from left-to-right.</span></span>
* <span data-ttu-id="2e9d7-738">对于每个参数，将比较环境值和显式值：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-738">For each parameter, the ambient value and explicit value are compared:</span></span>
    * <span data-ttu-id="2e9d7-739">如果环境值和显式值相同，则该过程将继续。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-739">If the ambient value and explicit value are the same, the process continues.</span></span>
    * <span data-ttu-id="2e9d7-740">如果环境值存在而显式值不存在，则在生成 URL 时使用环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-740">If the ambient value is present and the explicit value isn't, the ambient value is used when generating the URL.</span></span>
    * <span data-ttu-id="2e9d7-741">如果环境值不存在而显式值存在，则拒绝环境值和所有后续环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-741">If the ambient value isn't present and the explicit value is, reject the ambient value and all subsequent ambient values.</span></span>
    * <span data-ttu-id="2e9d7-742">如果环境值和显式值均存在，并且这两个值不同，则拒绝环境值和所有后续环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-742">If the ambient value and the explicit value are present, and the two values are different, reject the ambient value and all subsequent ambient values.</span></span>

<span data-ttu-id="2e9d7-743">此时，URL 生成操作就可以计算路由约束了。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-743">At this point, the URL generation operation is ready to evaluate route constraints.</span></span> <span data-ttu-id="2e9d7-744">接受的值集与提供给约束的参数默认值相结合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-744">The set of accepted values is combined with the parameter default values, which is provided to constraints.</span></span> <span data-ttu-id="2e9d7-745">如果所有约束都通过，则操作将继续。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-745">If the constraints all pass, the operation continues.</span></span>

<span data-ttu-id="2e9d7-746">接下来，接受的值可用于扩展路由模板。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-746">Next, the **accepted values** can be used to expand the route template.</span></span> <span data-ttu-id="2e9d7-747">处理路由模板：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-747">The route template is processed:</span></span>

* <span data-ttu-id="2e9d7-748">从左到右。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-748">From left-to-right.</span></span>
* <span data-ttu-id="2e9d7-749">每个参数都替换了接受的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-749">Each parameter has its accepted value substituted.</span></span>
* <span data-ttu-id="2e9d7-750">具有以下特殊情况：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-750">With the following special cases:</span></span>
  * <span data-ttu-id="2e9d7-751">如果接受的值缺少一个值并且参数具有默认值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-751">If the accepted values is missing a value and the parameter has a default value, the default value is used.</span></span>
  * <span data-ttu-id="2e9d7-752">如果接受的值缺少一个值并且参数是可选的，则继续处理。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-752">If the accepted values is missing a value and the parameter is optional, processing continues.</span></span>
  * <span data-ttu-id="2e9d7-753">如果缺少的可选参数右侧的任何路由参数都具有值，则操作将失败。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-753">If any route parameter to the right of a missing optional parameter has a value, the operation fails.</span></span>
  * <!-- review default-valued parameters optional parameters --> <span data-ttu-id="2e9d7-754">如果可能，连续的默认值参数和可选参数会折叠。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-754">Contiguous default-valued parameters and optional parameters are collapsed where possible.</span></span>

<span data-ttu-id="2e9d7-755">显式提供且与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-755">Values explicitly provided that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="2e9d7-756">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-756">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="2e9d7-757">环境值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-757">Ambient Values</span></span>                     | <span data-ttu-id="2e9d7-758">显式值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-758">Explicit Values</span></span>                        | <span data-ttu-id="2e9d7-759">结果</span><span class="sxs-lookup"><span data-stu-id="2e9d7-759">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="2e9d7-760">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-760">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-761">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-761">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="2e9d7-762">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-762">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-763">控制器 =“Order”，操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-763">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="2e9d7-764">控制器 = “Home”，颜色 = “Red”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-764">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="2e9d7-765">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-765">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="2e9d7-766">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-766">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-767">操作 =“About”，颜色 =“Red”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-767">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a><span data-ttu-id="2e9d7-768">路由值失效的问题</span><span class="sxs-lookup"><span data-stu-id="2e9d7-768">Problems with route value invalidation</span></span>

<span data-ttu-id="2e9d7-769">从 ASP.NET Core 3.0 开始，早期 ASP.NET Core 版本中使用的一些 URL 生成方案不适用于 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-769">As of ASP.NET Core 3.0, some URL generation schemes used in earlier ASP.NET Core versions don't work well with URL generation.</span></span> <span data-ttu-id="2e9d7-770">ASP.NET Core 团队计划在未来的版本中添加功能来满足这些需求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-770">The ASP.NET Core team plans to add features to address these needs in a future release.</span></span> <span data-ttu-id="2e9d7-771">现在，最佳解决方案是使用旧路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-771">For now the best solution is to use legacy routing.</span></span>

<span data-ttu-id="2e9d7-772">下面的代码显示了路由不支持的 URL 生成方案示例。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-772">The following code shows an example of a URL generation scheme that's not supported by routing.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

<span data-ttu-id="2e9d7-773">在前面的代码中，`culture` 路由参数用于本地化。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-773">In the preceding code, the `culture` route parameter is used for localization.</span></span> <span data-ttu-id="2e9d7-774">需要将 `culture` 参数始终作为环境值接受。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-774">The desire is to have the `culture` parameter always accepted as an ambient value.</span></span> <span data-ttu-id="2e9d7-775">但由于所需值的工作方式，不会将 `culture` 参数作为环境值接受：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-775">However, the `culture` parameter is not accepted as an ambient value because of the way required values work:</span></span>

* <span data-ttu-id="2e9d7-776">在 `"default"` 路由模板中，`culture` 路由参数位于 `controller` 的左侧，因此对 `controller` 的更改不会使 `culture` 失效。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-776">In the `"default"` route template, the `culture` route parameter is to the left of `controller`, so changes to `controller` won't invalidate `culture`.</span></span>
* <span data-ttu-id="2e9d7-777">在 `"blog"` 路由模板中，`culture` 路由参数应在 `controller` 的右侧，这会显示在所需值中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-777">In the `"blog"` route template, the `culture` route parameter is considered to be to the right of `controller`, which appears in the required values.</span></span>

## <a name="configuring-endpoint-metadata"></a><span data-ttu-id="2e9d7-778">配置终结点元数据</span><span class="sxs-lookup"><span data-stu-id="2e9d7-778">Configuring endpoint metadata</span></span>

<span data-ttu-id="2e9d7-779">以下链接提供有关配置终结点元数据的信息：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-779">The following links provide information on configuring endpoint metadata:</span></span>

* [<span data-ttu-id="2e9d7-780">通过终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="2e9d7-780">Enable Cors with endpoint routing</span></span>](xref:security/cors#enable-cors-with-endpoint-routing)
* <span data-ttu-id="2e9d7-781">使用自定义 `[MinimumAgeAuthorize]` 属性的 [IAuthorizationPolicyProvider 示例](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-781">[IAuthorizationPolicyProvider sample](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider) using a custom `[MinimumAgeAuthorize]` attribute</span></span>
* <span data-ttu-id="2e9d7-782">[使用 [Authorize] 属性测试身份验证](xref:security/authentication/identity#test-identity)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-782">[Test authentication with the [Authorize] attribute](xref:security/authentication/identity#test-identity)</span></span>
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* <span data-ttu-id="2e9d7-783">[使用 [Authorize] 属性选择方案](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-783">[Selecting the scheme with the [Authorize] attribute](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span></span>
* <span data-ttu-id="2e9d7-784">[使用 [Authorize] 属性应用策略](xref:security/authorization/policies#apply-policies-to-mvc-controllers)</span><span class="sxs-lookup"><span data-stu-id="2e9d7-784">[Apply policies using the [Authorize] attribute](xref:security/authorization/policies#apply-policies-to-mvc-controllers)</span></span>
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a><span data-ttu-id="2e9d7-785">路由中与 RequireHost 匹配的主机</span><span class="sxs-lookup"><span data-stu-id="2e9d7-785">Host matching in routes with RequireHost</span></span>

<span data-ttu-id="2e9d7-786"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> 将约束应用于需要指定主机的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-786"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> applies a constraint to the route which requires the specified host.</span></span> <span data-ttu-id="2e9d7-787">`RequireHost` 或 [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) 参数可以是：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-787">The `RequireHost` or [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) parameter can be:</span></span>

* <span data-ttu-id="2e9d7-788">主机：`www.domain.com`匹配任何端口的 `www.domain.com`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-788">Host: `www.domain.com`, matches `www.domain.com` with any port.</span></span>
* <span data-ttu-id="2e9d7-789">带有通配符的主机：`*.domain.com`，匹配任何端口上的 `www.domain.com`、`subdomain.domain.com` 或 `www.subdomain.domain.com`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-789">Host with wildcard: `*.domain.com`, matches `www.domain.com`, `subdomain.domain.com`, or `www.subdomain.domain.com` on any port.</span></span>
* <span data-ttu-id="2e9d7-790">端口：`*:5000` 匹配任何主机的端口 5000。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-790">Port: `*:5000`, matches port 5000 with any host.</span></span>
* <span data-ttu-id="2e9d7-791">主机和端口：`www.domain.com:5000` 或 `*.domain.com:5000`（匹配主机和端口）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-791">Host and port: `www.domain.com:5000` or `*.domain.com:5000`, matches host and port.</span></span>

<span data-ttu-id="2e9d7-792">可以使用 `RequireHost` 或 `[Host]` 指定多个参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-792">Multiple parameters can be specified using `RequireHost` or `[Host]`.</span></span> <span data-ttu-id="2e9d7-793">约束匹配对任何参数均有效的主机。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-793">The constraint  matches hosts valid for any of the parameters.</span></span> <span data-ttu-id="2e9d7-794">例如，`[Host("domain.com", "*.domain.com")]` 匹配 `domain.com`、`www.domain.com` 和 `subdomain.domain.com`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-794">For example, `[Host("domain.com", "*.domain.com")]` matches `domain.com`, `www.domain.com`, and `subdomain.domain.com`.</span></span>

<span data-ttu-id="2e9d7-795">以下代码使用 `RequireHost` 来要求路由上的指定主机：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-795">The following code uses `RequireHost` to require the specified host on the route:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

<span data-ttu-id="2e9d7-796">以下代码使用控制器上的 `[Host]` 特性来要求任何指定的主机：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-796">The following code uses the `[Host]` attribute on the controller to require any of the specified hosts:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

<span data-ttu-id="2e9d7-797">当 `[Host]` 属性同时应用于控制器和操作方法时：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-797">When the `[Host]` attribute is applied to both the controller and action method:</span></span>

* <span data-ttu-id="2e9d7-798">使用操作上的属性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-798">The attribute on the action is used.</span></span>
* <span data-ttu-id="2e9d7-799">忽略控制器属性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-799">The controller attribute is ignored.</span></span>

## <a name="performance-guidance-for-routing"></a><span data-ttu-id="2e9d7-800">路由的性能指南</span><span class="sxs-lookup"><span data-stu-id="2e9d7-800">Performance guidance for routing</span></span>

<span data-ttu-id="2e9d7-801">大多数路由在 ASP.NET Core 3.0 中都进行了更新，以提高性能。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-801">Most of routing was updated in ASP.NET Core 3.0 to increase performance.</span></span>

<span data-ttu-id="2e9d7-802">当应用出现性能问题时，人们常常会怀疑路由是问题所在。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-802">When an app has performance problems, routing is often suspected as the problem.</span></span> <span data-ttu-id="2e9d7-803">怀疑路由的原因是，控制器和 Razor Pages 等框架在其日志记录消息中报告框架内所用的时间。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-803">The reason routing is suspected is that frameworks like controllers and Razor Pages report the amount of time spent inside the framework in their logging messages.</span></span> <span data-ttu-id="2e9d7-804">如果控制器报告的时间与请求的总时间之间存在明显的差异：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-804">When there's a significant difference between the time reported by controllers and the total time of the request:</span></span>

* <span data-ttu-id="2e9d7-805">开发者会将应用代码排除在问题根源之外。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-805">Developers eliminate their app code as the source of the problem.</span></span>
* <span data-ttu-id="2e9d7-806">他们通常认为路由是问题的原因。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-806">It's common to assume routing is the cause.</span></span>

<span data-ttu-id="2e9d7-807">路由的性能使用数千个终结点进行测试。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-807">Routing is performance tested using thousands of endpoints.</span></span> <span data-ttu-id="2e9d7-808">典型的应用不太可能仅仅因为太大而遇到性能问题。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-808">It's unlikely that a typical app will encounter a performance problem just by being too large.</span></span> <span data-ttu-id="2e9d7-809">路由性能缓慢的最常见根本原因通常在于性能不佳的自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-809">The most common root cause of slow routing performance is usually a badly-behaving custom middleware.</span></span>

<span data-ttu-id="2e9d7-810">下面的代码示例演示了一种用于缩小延迟源的基本方法：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-810">This following code sample demonstrates a basic technique for narrowing down the source of delay:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

<span data-ttu-id="2e9d7-811">时间路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-811">To time routing:</span></span>

* <span data-ttu-id="2e9d7-812">使用前面代码中所示的计时中间件的副本交错执行每个中间件。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-812">Interleave each middleware with a copy of the timing middleware shown in the preceding code.</span></span>
* <span data-ttu-id="2e9d7-813">添加唯一标识符，以便将计时数据与代码相关联。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-813">Add a unique identifier to correlate the timing data with the code.</span></span>

<span data-ttu-id="2e9d7-814">这是一种可在延迟显著的情况下减少延迟的基本方法，例如超过 `10ms`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-814">This is a basic way to narrow down the delay when it's significant, for example, more than `10ms`.</span></span>  <span data-ttu-id="2e9d7-815">从 `Time 1` 中减去 `Time 2` 会报告 `UseRouting` 中间件内所用的时间。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-815">Subtracting `Time 2` from `Time 1` reports the time spent inside the `UseRouting` middleware.</span></span>

<span data-ttu-id="2e9d7-816">下面的代码使用一种更紧凑的方法来处理前面的计时代码：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-816">The following code uses a more compact approach to the preceding timing code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a><span data-ttu-id="2e9d7-817">可能比较昂贵的路由功能</span><span class="sxs-lookup"><span data-stu-id="2e9d7-817">Potentially expensive routing features</span></span>

<span data-ttu-id="2e9d7-818">下面的列表提供了一些路由功能，这些功能相对于基本路由模板来说比较昂贵：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-818">The following list provides some insight into routing features that are relatively expensive compared with basic route templates:</span></span>

* <span data-ttu-id="2e9d7-819">正则表达式：可以编写复杂的正则表达式，或具有少量输入的长时间运行时间。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-819">Regular expressions: It's possible to write regular expressions that are complex, or have long running time with a small amount of input.</span></span>

* <span data-ttu-id="2e9d7-820">复杂段 (`{x}-{y}-{z}`)：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-820">Complex segments (`{x}-{y}-{z}`):</span></span> 
  * <span data-ttu-id="2e9d7-821">比分析常规 URL 路径段贵得多。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-821">Are significantly more expensive than parsing a regular URL path segment.</span></span>
  * <span data-ttu-id="2e9d7-822">导致更多的子字符串被分配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-822">Result in many more substrings being allocated.</span></span>
  * <span data-ttu-id="2e9d7-823">ASP.NET Core 3.0 路由性能更新中未更新的复杂段逻辑。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-823">The complex segment logic was not updated in ASP.NET Core 3.0 routing performance update.</span></span>

* <span data-ttu-id="2e9d7-824">同步数据访问：许多复杂应用都将数据库访问作为其路由的一部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-824">Synchronous data access: Many complex apps have database access as part of their routing.</span></span> <span data-ttu-id="2e9d7-825">ASP.NET Core 2.2 及更低版本的路由可能未提供适当的扩展点，因此无法支持数据库访问路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-825">ASP.NET Core 2.2 and earlier routing might not provide the right extensibility points to support database access routing.</span></span> <span data-ttu-id="2e9d7-826">例如，<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 和 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 是同步的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-826">For example, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, and <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are synchronous.</span></span> <span data-ttu-id="2e9d7-827">扩展点（如 <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> 和 <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext>）是异步的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-827">Extensibility points such as <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> and <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> are asynchronous.</span></span>

## <a name="guidance-for-library-authors"></a><span data-ttu-id="2e9d7-828">库创建者指南</span><span class="sxs-lookup"><span data-stu-id="2e9d7-828">Guidance for library authors</span></span>

<span data-ttu-id="2e9d7-829">本部分包含基于路由构建的库创建者指南。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-829">This section contains guidance for library authors building on top of routing.</span></span> <span data-ttu-id="2e9d7-830">这些详细信息旨在确保应用开发者可以在使用扩展路由的库和框架时具有良好的体验。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-830">These details are intended to ensure that app developers have a good experience using libraries and frameworks that extend routing.</span></span>

### <a name="define-endpoints"></a><span data-ttu-id="2e9d7-831">定义终结点</span><span class="sxs-lookup"><span data-stu-id="2e9d7-831">Define endpoints</span></span>

<span data-ttu-id="2e9d7-832">若要创建一个使用路由进行 URL 匹配的框架，请先定义在 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 之上进行构建的用户体验。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-832">To create a framework that uses routing for URL matching, start by defining a user experience that builds on top of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>

<span data-ttu-id="2e9d7-833">在 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> 之上进行构建。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-833">**DO** build on top of <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span> <span data-ttu-id="2e9d7-834">由此，用户就可以用其他 ASP.NET Core 功能编写框架，而不会造成混淆。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-834">This allows users to compose your framework with other ASP.NET Core features without confusion.</span></span> <span data-ttu-id="2e9d7-835">每个 ASP.NET Core 模板都包含路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-835">Every ASP.NET Core template includes routing.</span></span> <span data-ttu-id="2e9d7-836">假定路由存在并且用户熟悉路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-836">Assume routing is present and familiar for users.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="2e9d7-837">从对实现 <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> 的 `MapMyFramework(...)` 的调用返回密式具体类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-837">**DO** return a sealed concrete type from a call to `MapMyFramework(...)` that implements <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>.</span></span> <span data-ttu-id="2e9d7-838">大多数框架 `Map...` 方法都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-838">Most framework `Map...` methods follow this pattern.</span></span> <span data-ttu-id="2e9d7-839">`IEndpointConventionBuilder` 接口：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-839">The `IEndpointConventionBuilder` interface:</span></span>

* <span data-ttu-id="2e9d7-840">允许元数据的可组合性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-840">Allows composability of metadata.</span></span>
* <span data-ttu-id="2e9d7-841">面向多种扩展方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-841">Is targeted by a variety of extension methods.</span></span>

<span data-ttu-id="2e9d7-842">通过声明自己的类型，你可以将自己的框架特定功能添加到生成器中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-842">Declaring your own type allows you to add your own framework-specific functionality to the builder.</span></span> <span data-ttu-id="2e9d7-843">可以包装一个框架声明的生成器并向其转发调用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-843">It's ok to wrap a framework-declared builder and forward calls to it.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="2e9d7-844">请考虑编写自己的 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-844">**CONSIDER** writing your own <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>.</span></span> <span data-ttu-id="2e9d7-845">`EndpointDataSource` 是用于声明和更新终结点集合的低级别基元。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-845">`EndpointDataSource` is the low-level primitive for declaring and updating a collection of endpoints.</span></span> <span data-ttu-id="2e9d7-846">`EndpointDataSource` 是控制器和 Razor Pages 使用的强大 API。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-846">`EndpointDataSource` is a powerful API used by controllers and Razor Pages.</span></span>

<span data-ttu-id="2e9d7-847">路由测试具有非更新数据源的[基本示例](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-847">The routing tests have a [basic example](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) of a non-updating data source.</span></span>

<span data-ttu-id="2e9d7-848">默认情况下，请不要尝试注册 `EndpointDataSource`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-848">**DO NOT** attempt to register an `EndpointDataSource` by default.</span></span> <span data-ttu-id="2e9d7-849">要求用户在 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 中注册你的框架。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-849">Require users to register your framework in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span> <span data-ttu-id="2e9d7-850">路由的理念是，默认情况下不包含任何内容，并且 `UseEndpoints` 是注册终结点的位置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-850">The philosophy of routing is that nothing is included by default, and that `UseEndpoints` is the place to register endpoints.</span></span>

### <a name="creating-routing-integrated-middleware"></a><span data-ttu-id="2e9d7-851">创建路由集成式中间件</span><span class="sxs-lookup"><span data-stu-id="2e9d7-851">Creating routing-integrated middleware</span></span>

<span data-ttu-id="2e9d7-852">请考虑将元数据类型定义为接口。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-852">**CONSIDER** defining metadata types as an interface.</span></span>

<span data-ttu-id="2e9d7-853">请实现在类和方法上使用元数据类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-853">**DO** make it possible to use metadata types as an attribute on classes and methods.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

<span data-ttu-id="2e9d7-854">控制器和 Razor Pages 等框架支持将元数据特性应用到类型和方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-854">Frameworks like controllers and Razor Pages support applying metadata attributes to types and methods.</span></span> <span data-ttu-id="2e9d7-855">如果声明元数据类型：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-855">If you declare metadata types:</span></span>

* <span data-ttu-id="2e9d7-856">将它们作为[特性](/dotnet/csharp/programming-guide/concepts/attributes/)进行访问。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-856">Make them accessible as [attributes](/dotnet/csharp/programming-guide/concepts/attributes/).</span></span>
* <span data-ttu-id="2e9d7-857">大多数用户都熟悉应用特性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-857">Most users are familiar with applying attributes.</span></span>

<span data-ttu-id="2e9d7-858">将元数据类型声明为接口可增加另一层灵活性：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-858">Declaring a metadata type as an interface adds another layer of flexibility:</span></span>

* <span data-ttu-id="2e9d7-859">接口是可组合的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-859">Interfaces are composable.</span></span>
* <span data-ttu-id="2e9d7-860">开发者可以声明自己的且组合多个策略的类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-860">Developers can declare their own types that combine multiple policies.</span></span>

<span data-ttu-id="2e9d7-861">请实现元数据替代，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-861">**DO** make it possible to override metadata, as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

<span data-ttu-id="2e9d7-862">遵循这些准则的最佳方式是避免定义标记元数据：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-862">The best way to follow these guidelines is to avoid defining **marker metadata**:</span></span>

* <span data-ttu-id="2e9d7-863">不要只是查找元数据类型的状态。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-863">Don't just look for the presence of a metadata type.</span></span>
* <span data-ttu-id="2e9d7-864">定义元数据的属性并检查该属性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-864">Define a property on the metadata and check the property.</span></span>

<span data-ttu-id="2e9d7-865">对元数据集合进行排序，并支持按优先级替代。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-865">The metadata collection is ordered and supports overriding by priority.</span></span> <span data-ttu-id="2e9d7-866">对于控制器，操作方法上的元数据是最具体的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-866">In the case of controllers, metadata on the action method is most specific.</span></span>

<span data-ttu-id="2e9d7-867">使中间件始终有用，不管有没有路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-867">**DO** make middleware useful with and without routing.</span></span>

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization();
});
```

<span data-ttu-id="2e9d7-868">作为此准则的一个示例，请考虑 `UseAuthorization` 中间件。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-868">As an example of this guideline, consider the `UseAuthorization` middleware.</span></span> <span data-ttu-id="2e9d7-869">授权中间件允许传入回退策略。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-869">The authorization middleware allows you to pass in a fallback policy.</span></span> <!-- shown where?  (shown here) --> <span data-ttu-id="2e9d7-870">如果指定了回退策略，则适用于以下两者：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-870">The fallback policy, if specified, applies to both:</span></span>

* <span data-ttu-id="2e9d7-871">无指定策略的终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-871">Endpoints without a specified policy.</span></span>
* <span data-ttu-id="2e9d7-872">与终结点不匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-872">Requests that don't match an endpoint.</span></span>

<span data-ttu-id="2e9d7-873">这使得授权中间件在路由上下文之外很有用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-873">This makes the authorization middleware useful outside of the context of routing.</span></span> <span data-ttu-id="2e9d7-874">授权中间件可用于传统中间件的编程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-874">The authorization middleware can be used for traditional middleware programming.</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2e9d7-875">路由负责将请求 URI 映射到终结点并向这些终结点调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-875">Routing is responsible for mapping request URIs to endpoints and dispatching incoming requests to those endpoints.</span></span> <span data-ttu-id="2e9d7-876">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-876">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="2e9d7-877">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-877">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="2e9d7-878">通过使用应用中的路由信息，路由还能生成映射到终结点的 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-878">Using route information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="2e9d7-879">要在 ASP.NET Core 2.2 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-879">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="2e9d7-880"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 选项确定路由是应在内部使用 ASP.NET Core 2.1 或更早版本的基于终结点的逻辑还是使用其基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的逻辑。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-880">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="2e9d7-881">兼容性版本设置为 2.2 或更高版本时，默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-881">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="2e9d7-882">将值设置为 `false` 以使用先前的路由逻辑：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-882">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="2e9d7-883">有关基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由的详细信息，请参阅本主题的 [ASP.NET Core 2.1 版本](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-883">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2e9d7-884">本文档介绍较低级别的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-884">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="2e9d7-885">有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-885">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="2e9d7-886">有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-886">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="2e9d7-887">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2e9d7-887">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="2e9d7-888">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="2e9d7-888">Routing basics</span></span>

<span data-ttu-id="2e9d7-889">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-889">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="2e9d7-890">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-890">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="2e9d7-891">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-891">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="2e9d7-892">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-892">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="2e9d7-893">开发者通常在专业情况下使用[特性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-893">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span> <span data-ttu-id="2e9d7-894">专用情况示例包括：博客和电子商务终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-894">Specialized situations examples include, blog and ecommerce endpoints.</span></span>

<span data-ttu-id="2e9d7-895">Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-895">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="2e9d7-896">也就是说，对同一逻辑资源执行的许多操作（例如，GET 和 POST）都使用相同 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-896">This means that many operations, for example, GET, and POST, on the same logical resource use the same URL.</span></span> <span data-ttu-id="2e9d7-897">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-897">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="2e9d7-898">Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-898">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="2e9d7-899">还可以使用其他约定来自定义 Razor Pages 路由行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-899">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="2e9d7-900">有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-900">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="2e9d7-901">借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-901">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="2e9d7-902">此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-902">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="2e9d7-903">路由使用“终结点”(`Endpoint`) 来表示应用中的逻辑终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-903">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="2e9d7-904">终结点定义用于处理请求的委托和任意元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-904">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="2e9d7-905">元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-905">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="2e9d7-906">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-906">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="2e9d7-907">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-907">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="2e9d7-908">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-908">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="2e9d7-909"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-909"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="2e9d7-910">应用模型（如 MVC/Razor Pages）注册其所有终结点，这些终结点具有可预测的路由方案实现。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-910">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="2e9d7-911">路由实现会在中间件管道中任何所需位置制定路由决策。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-911">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="2e9d7-912">路由中间件之后出现的中间件可以检查路由中间件针对给定请求 URI 的终结点决策结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-912">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="2e9d7-913">可以在中间件管道中的任何位置枚举应用中的所有终结点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-913">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="2e9d7-914">应用可根据终结点信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-914">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="2e9d7-915">URL 生成是基于支持任意可扩展性的地址：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-915">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="2e9d7-916">可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在任意位置解析链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) 以生成 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-916">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="2e9d7-917">如果无法通过 DI 获得链接生成器 API，则 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 会提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-917">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="2e9d7-918">在 ASP.NET Core 2.2 中发布终结点路由后，终结点链接的适用范围限制为 MVC/Razor Pages 操作和页面。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-918">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="2e9d7-919">将计划在未来发布的版本中扩展终结点链接的功能。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-919">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

<span data-ttu-id="2e9d7-920">路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-920">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="2e9d7-921">[ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-921">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="2e9d7-922">要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-922">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="2e9d7-923">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-923">URL matching</span></span>

<span data-ttu-id="2e9d7-924">URL 匹配是路由向终结点调度传入请求的过程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-924">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="2e9d7-925">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-925">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="2e9d7-926">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-926">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="2e9d7-927">终结点路由中的路由系统负责所有的调度决策。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-927">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="2e9d7-928">由于中间件是基于所选终结点来应用策略，因此任何可能影响调度或安全策略应用的决策都应在路由系统内部制定，这一点很重要。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-928">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="2e9d7-929">执行终结点委托时，将根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-929">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="2e9d7-930">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-930">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="2e9d7-931">这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-931">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="2e9d7-932">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-932">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="2e9d7-933">提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-933"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="2e9d7-934">这些值是开发者定义的，不会影响通过任何方式路由的行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-934">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="2e9d7-935">此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-935">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="2e9d7-936">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-936">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="2e9d7-937">路由可以相互嵌套。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-937">Routes can be nested inside of one another.</span></span> <span data-ttu-id="2e9d7-938"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-938">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="2e9d7-939">通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-939">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="2e9d7-940"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-940">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="2e9d7-941">使用 LinkGenerator 的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="2e9d7-941">URL generation with LinkGenerator</span></span>

<span data-ttu-id="2e9d7-942">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-942">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="2e9d7-943">这需要考虑终结点与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-943">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="2e9d7-944">终结点路由包含链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-944">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="2e9d7-945"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 是可从 [DI](xref:fundamentals/dependency-injection) 检索的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-945"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2e9d7-946">该 API 可在执行请求的上下文之外使用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-946">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="2e9d7-947">MVC 的 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 和依赖 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的方案（如 [Tag Helpers](xref:mvc/views/tag-helpers/intro)、HTML Helpers 和 [Action Results](xref:mvc/controllers/actions)）使用链接生成器提供链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-947">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="2e9d7-948">链接生成器基于“地址”和“地址方案”概念 。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-948">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="2e9d7-949">地址方案是确定哪些终结点用于链接生成的方式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-949">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="2e9d7-950">例如，许多用户熟悉的来自 MVC/Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-950">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="2e9d7-951">链接生成器可以通过以下扩展方法链接到 MVC/Razor Pages 操作和页面：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-951">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="2e9d7-952">这些方法的重载接受包含 `HttpContext` 的参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-952">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="2e9d7-953">这些方法在功能上等同于 `Url.Action` 和 `Url.Page`，但提供了更大的灵活性和更多的选项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-953">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="2e9d7-954">`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-954">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="2e9d7-955">`GetUri*` 方法始终生成包含方案和主机的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-955">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="2e9d7-956">接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-956">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="2e9d7-957">除非重写，否则将使用来自执行请求的环境路由值、URL 基本路径、方案和主机。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-957">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="2e9d7-958">使用地址调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-958"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="2e9d7-959">生成 URI 的过程分两步进行：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-959">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="2e9d7-960">将地址绑定到与地址匹配的终结点列表。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-960">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="2e9d7-961">计算每个终结点的 `RoutePattern`，直到找到与提供的值匹配的路由模式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-961">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="2e9d7-962">输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-962">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="2e9d7-963">对任何类型的地址，<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法均支持标准链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-963">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="2e9d7-964">使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-964">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="2e9d7-965">扩展方法</span><span class="sxs-lookup"><span data-stu-id="2e9d7-965">Extension Method</span></span>   | <span data-ttu-id="2e9d7-966">描述</span><span class="sxs-lookup"><span data-stu-id="2e9d7-966">Description</span></span>                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | <span data-ttu-id="2e9d7-967">根据提供的值生成具有绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-967">Generates a URI with an absolute path based on the provided values.</span></span> |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | <span data-ttu-id="2e9d7-968">根据提供的值生成绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-968">Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="2e9d7-969">请注意有关调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列含义：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-969">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="2e9d7-970">对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-970">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="2e9d7-971">如果未验证传入请求的 `Host`标头，则可能以视图/页面中 URI 的形式将不受信任的请求输入发送回客户端。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-971">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="2e9d7-972">建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-972">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="2e9d7-973">在中间件中将 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-973">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="2e9d7-974">`Map*` 会更改执行请求的基路径，这会影响链接生成的输出。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-974">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="2e9d7-975">所有 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允许指定基路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-975">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="2e9d7-976">始终指定一个空的基路径来撤消 `Map*` 对链接生成的影响。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-976">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="2e9d7-977">与早期版本路由的差异</span><span class="sxs-lookup"><span data-stu-id="2e9d7-977">Differences from earlier versions of routing</span></span>

<span data-ttu-id="2e9d7-978">ASP.NET Core 2.2 或更高版本中的终结点路由与 ASP.NET Core 中早期版本的路由之间存在一些差异：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-978">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="2e9d7-979">终结点路由系统不支持基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的可扩展性，包括从 <xref:Microsoft.AspNetCore.Routing.Route> 继承。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-979">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="2e9d7-980">终结点路由不支持 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-980">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="2e9d7-981">使用 2.1 [兼容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 以继续使用兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-981">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="2e9d7-982">在使用传统路由时，终结点路由对生成的 URI 的大小写具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-982">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="2e9d7-983">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-983">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="2e9d7-984">假设使用以下路由生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-984">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="2e9d7-985">如果使用基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由，此代码生成 URI `/blog/ReadPost/17`，该 URI 遵循所提供路由值的大小写。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-985">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="2e9d7-986">ASP.NET Core 2.2 或更高版本中的终结点路由生成 `/Blog/ReadPost/17`（“Blog”大写）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-986">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="2e9d7-987">终结点路由提供 `IOutboundParameterTransformer` 接口，可用于在全局范围自定义此行为或应用不同的约定来映射 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-987">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="2e9d7-988">有关详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-988">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="2e9d7-989">在试图链接到不存在的控制器/操作或页面时，MVC/Razor Pages 通过传统路由执行的链接生成，其操作具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-989">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="2e9d7-990">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-990">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="2e9d7-991">假设使用以下路由通过默认模板生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-991">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="2e9d7-992">如果使用基于 `IRouter` 的路由，即使 `BlogController` 不存在或没有 `ReadPost` 操作方法，结果也始终为 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-992">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="2e9d7-993">正如所料，如果操作方法存在，ASP.NET Core 2.2 或更高版本中的终结点路由会生成 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-993">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="2e9d7-994">但是，如果操作不存在，终结点路由会生成空字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-994">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="2e9d7-995">从概念上讲，如果操作不存在，终结点路由不会假定终结点存在。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-995">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="2e9d7-996">与终结点路由一起使用时，链接生成环境值失效算法的行为会有所不同。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-996">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="2e9d7-997">*环境值失效*是一种算法，用于决定当前正在执行的请求（环境值）中的哪些路由值可用于链接生成操作。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-997">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="2e9d7-998">链接到不同操作时，传统路由会使额外的路由值失效。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-998">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="2e9d7-999">ASP.NET Core 2.2 之前的版本中，属性路由不具有此行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-999">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="2e9d7-1000">在 ASP.NET Core 的早期版本中，如果有另一个操作使用同一路由参数名称，则该操作的链接会导致发生链接生成错误。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1000">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="2e9d7-1001">在 ASP.NET Core 2.2 或更高版本中，链接到另一个操作时，这两种路由形式都会使值失效。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1001">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="2e9d7-1002">请考虑 ASP.NET Core2.1 或更高版本中的以下示例。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1002">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="2e9d7-1003">链接到另一个操作（或另一页面）时，路由值可能会按非预期的方式被重用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1003">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="2e9d7-1004">在 /Pages/Store/Product.cshtml 中：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1004">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="2e9d7-1005">在 /Pages/Login.cshtml 中：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1005">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="2e9d7-1006">如果 ASP.NET Core 2.1 或更早版本中的 URI 为 `/Store/Product/18`，则由 `@Url.Page("/Login")` 在 Store/Info 页面上生成的链接为 `/Login/18`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1006">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="2e9d7-1007">即使链接目标完全是应用的其他部分，仍会重用 `id` 值 18。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1007">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="2e9d7-1008">`/Login` 页面上下文中的 `id` 路由值可能是用户 ID 值，而非存储产品 ID 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1008">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="2e9d7-1009">在 ASP.NET Core 2.2 或更高版本的终结点路由中，结果为 `/Login`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1009">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="2e9d7-1010">当链接的目标是另一个操作或页面时，不会重复使用环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1010">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="2e9d7-1011">往返路由参数语法：使用双星号 (`**`) catch-all 参数语法时，不对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1011">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="2e9d7-1012">在链接生成期间，路由系统对双星号 (`**`) catch-all 参数（例如，`{**myparametername}`）中捕获的除正斜杠外的值进行编码。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1012">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="2e9d7-1013">在 ASP.NET Core 2.2 或更高版本中，基于 `IRouter` 的路由支持双星号 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1013">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="2e9d7-1014">ASP.NET Core (`{*myparametername}`) 早期版本中的单星号 catch-all 参数语法仍然受支持，并对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1014">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="2e9d7-1015">路由</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1015">Route</span></span>              | <span data-ttu-id="2e9d7-1016">链接生成方式为</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1016">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | <span data-ttu-id="2e9d7-1017">`/search/admin%2Fproducts`（对正斜杠进行编码）</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1017">`/search/admin%2Fproducts` (the forward slash is encoded)</span></span>             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a><span data-ttu-id="2e9d7-1018">中间件示例</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1018">Middleware example</span></span>

<span data-ttu-id="2e9d7-1019">在以下示例中，中间件使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 创建列出存储产品的操作方法的链接。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1019">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="2e9d7-1020">应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1020">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

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

### <a name="create-routes"></a><span data-ttu-id="2e9d7-1021">创建路由</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1021">Create routes</span></span>

<span data-ttu-id="2e9d7-1022">大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1022">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="2e9d7-1023">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1023">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="2e9d7-1024"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1024"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="2e9d7-1025"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1025"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="2e9d7-1026">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1026">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="2e9d7-1027">以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1027">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2e9d7-1028">此模板与 URL 路径相匹配，并且提取路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1028">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="2e9d7-1029">例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1029">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="2e9d7-1030">路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1030">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="2e9d7-1031">路由参数已命名。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1031">Route parameters are named.</span></span> <span data-ttu-id="2e9d7-1032">参数通过将参数名称括在大括号 `{ ... }` 中来定义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1032">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="2e9d7-1033">上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1033">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="2e9d7-1034">这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1034">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="2e9d7-1035">路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1035">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="2e9d7-1036">路由参数名称后面的问号 (`?`) 定义了可选参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1036">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="2e9d7-1037">路由匹配时，具有默认值的路由参数始终会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1037">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="2e9d7-1038">如果没有相应的 URL 路径段，则可选参数不会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1038">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="2e9d7-1039">有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1039">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="2e9d7-1040">在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1040">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="2e9d7-1041">此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1041">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="2e9d7-1042">路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1042">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="2e9d7-1043">在此示例中，路由值 `id` 必须可转换为整数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1043">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="2e9d7-1044">有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1044">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="2e9d7-1045">其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1045">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="2e9d7-1046">这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1046">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="2e9d7-1047">以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1047">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

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
> <span data-ttu-id="2e9d7-1048">定义约束和默认值的内联语法对于简单路由很方便。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1048">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="2e9d7-1049">但是，存在内联语法不支持的方案，例如数据令牌。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1049">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="2e9d7-1050">以下示例演示了一些其他方案：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1050">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="2e9d7-1051">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1051">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="2e9d7-1052">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1052">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="2e9d7-1053">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1053">Default values can be specified in the route template.</span></span> <span data-ttu-id="2e9d7-1054">根据路由参数名称前的双星号 (`**`) 外观，`article` 路由参数被定义为 catch-all。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1054">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="2e9d7-1055">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1055">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="2e9d7-1056">以下示例添加了路由约束和数据令牌：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1056">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="2e9d7-1057">上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1057">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="2e9d7-1059">路由类 URL 生成</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1059">Route class URL generation</span></span>

<span data-ttu-id="2e9d7-1060"><xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1060">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="2e9d7-1061">从逻辑上来说，这是匹配 URL 路径的反向过程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1061">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="2e9d7-1062">为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1062">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="2e9d7-1063">将生成什么值？</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1063">What values would be produced?</span></span> <span data-ttu-id="2e9d7-1064">这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1064">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="2e9d7-1065">以下示例使用常规 ASP.NET Core MVC 默认路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1065">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2e9d7-1066">使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1066">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="2e9d7-1067">路由值将替换为相应的路由参数，以形成 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1067">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="2e9d7-1068">由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1068">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="2e9d7-1069">使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1069">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="2e9d7-1070">提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1070">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="2e9d7-1071">已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1071">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="2e9d7-1072">使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1072">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="2e9d7-1073">有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1073">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="2e9d7-1074">使用路由中间件</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1074">Use Routing Middleware</span></span>

<span data-ttu-id="2e9d7-1075">引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1075">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="2e9d7-1076">将路由添加到 `Startup.ConfigureServices` 中的服务容器：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1076">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="2e9d7-1077">必须在 `Startup.Configure` 方法中配置路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1077">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="2e9d7-1078">该示例应用使用以下 API：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1078">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="2e9d7-1079"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>：仅匹配 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1079"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="2e9d7-1080">下表显示了具有给定 URI 的响应。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1080">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="2e9d7-1081">URI</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1081">URI</span></span>                    | <span data-ttu-id="2e9d7-1082">响应</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1082">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="2e9d7-1083">Hello!</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1083">Hello!</span></span> <span data-ttu-id="2e9d7-1084">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1084">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="2e9d7-1085">Hello!</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1085">Hello!</span></span> <span data-ttu-id="2e9d7-1086">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1086">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="2e9d7-1087">Hello!</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1087">Hello!</span></span> <span data-ttu-id="2e9d7-1088">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1088">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="2e9d7-1089">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1089">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="2e9d7-1090">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1090">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="2e9d7-1091">请求失败，仅匹配 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1091">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="2e9d7-1092">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1092">The request falls through, no match.</span></span>              |

<span data-ttu-id="2e9d7-1093">框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1093">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

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

<span data-ttu-id="2e9d7-1094">`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1094">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="2e9d7-1095">有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1095">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="2e9d7-1096">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1096">Route template reference</span></span>

<span data-ttu-id="2e9d7-1097">如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1097">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="2e9d7-1098">可在路由段中定义多个路由参数，但必须由文本值隔开。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1098">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="2e9d7-1099">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1099">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="2e9d7-1100">这些路由参数必须具有名称，且可能指定了其他属性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1100">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="2e9d7-1101">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1101">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="2e9d7-1102">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1102">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="2e9d7-1103">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1103">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="2e9d7-1104">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1104">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="2e9d7-1105">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1105">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="2e9d7-1106">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1106">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="2e9d7-1107">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1107">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="2e9d7-1108">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1108">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="2e9d7-1109">可以使用星号 (`*`) 或双星号 (`**`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1109">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="2e9d7-1110">这些称为 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1110">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="2e9d7-1111">例如，`blog/{**slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1111">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="2e9d7-1112">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1112">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="2e9d7-1113">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1113">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="2e9d7-1114">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1114">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="2e9d7-1115">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1115">Note the escaped forward slash.</span></span> <span data-ttu-id="2e9d7-1116">要往返路径分隔符，请使用 `**` 路由参数前缀。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1116">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="2e9d7-1117">`{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1117">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="2e9d7-1118">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1118">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="2e9d7-1119">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1119">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="2e9d7-1120">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1120">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="2e9d7-1121">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1121">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="2e9d7-1122">可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1122">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="2e9d7-1123">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1123">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="2e9d7-1124">在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1124">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="2e9d7-1125">如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1125">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="2e9d7-1126">通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1126">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="2e9d7-1127">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1127">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="2e9d7-1128">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1128">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="2e9d7-1129">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1129">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="2e9d7-1130">路由参数还可以具有参数转换器，用于在生成链接以及将操作和页面匹配到 URI 时转换参数的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1130">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="2e9d7-1131">与约束类似，可以通过在路由参数名称后面添加冒号 (`:`) 和转换器名称，将参数变换器内联添加到路径参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1131">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="2e9d7-1132">例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1132">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="2e9d7-1133">有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1133">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="2e9d7-1134">下表演示了示例路由模板及其行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1134">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="2e9d7-1135">路由模板</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1135">Route Template</span></span>                           | <span data-ttu-id="2e9d7-1136">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1136">Example Matching URI</span></span>    | <span data-ttu-id="2e9d7-1137">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1137">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="2e9d7-1138">仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1138">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="2e9d7-1139">匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1139">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="2e9d7-1140">匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1140">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="2e9d7-1141">映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1141">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="2e9d7-1142">映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1142">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="2e9d7-1143">映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1143">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="2e9d7-1144">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1144">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="2e9d7-1145">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1145">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="2e9d7-1146">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1146">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="2e9d7-1147">保留的路由名称</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1147">Reserved routing names</span></span>

<span data-ttu-id="2e9d7-1148">以下关键字是保留的名称，它们不能用作路由名称或参数：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1148">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="2e9d7-1149">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1149">Route constraint reference</span></span>

<span data-ttu-id="2e9d7-1150">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1150">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="2e9d7-1151">路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1151">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="2e9d7-1152">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1152">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="2e9d7-1153">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1153">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="2e9d7-1154">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1154">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="2e9d7-1155">请勿将约束用于“输入验证”。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1155">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="2e9d7-1156">如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求” 。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1156">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="2e9d7-1157">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1157">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="2e9d7-1158">下表演示示例路由约束及其预期行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1158">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="2e9d7-1159">约束</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1159">Constraint</span></span> | <span data-ttu-id="2e9d7-1160">示例</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1160">Example</span></span> | <span data-ttu-id="2e9d7-1161">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1161">Example matches</span></span> | <span data-ttu-id="2e9d7-1162">说明</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1162">Notes</span></span> |
|------------|---------|-----------------|-------|
| `int` | `{id:int}` | <span data-ttu-id="2e9d7-1163">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1163">`123456789`, `-123456789`</span></span> | <span data-ttu-id="2e9d7-1164">匹配任何整数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1164">Matches any integer.</span></span>|
| `bool` | `{active:bool}` | <span data-ttu-id="2e9d7-1165">`true`，`FALSE`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1165">`true`, `FALSE`</span></span> | <span data-ttu-id="2e9d7-1166">匹配 `true` 或 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1166">Matches `true` or `false`.</span></span> <span data-ttu-id="2e9d7-1167">不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1167">Case-insensitive.</span></span>|
| `datetime` | `{dob:datetime}` | <span data-ttu-id="2e9d7-1168">`2016-12-31`，`2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1168">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="2e9d7-1169">在固定区域性中匹配有效的 `DateTime` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1169">Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-1170">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1170">See preceding warning.</span></span>|
| `decimal` | `{price:decimal}` | <span data-ttu-id="2e9d7-1171">`49.99`，`-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1171">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="2e9d7-1172">在固定区域性中匹配有效的 `decimal` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1172">Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-1173">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1173">See  preceding warning.</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="2e9d7-1174">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1174">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="2e9d7-1175">在固定区域性中匹配有效的 `double` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1175">Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-1176">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1176">See  preceding warning.</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="2e9d7-1177">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1177">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="2e9d7-1178">在固定区域性中匹配有效的 `float` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1178">Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-1179">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1179">See  preceding warning.</span></span>|
| `guid` | `{id:guid}` | <span data-ttu-id="2e9d7-1180">`CD2C1638-1638-72D5-1638-DEADBEEF1638`，`{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1180">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="2e9d7-1181">匹配有效的 `Guid` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1181">Matches a valid `Guid` value.</span></span>|
| `long` | `{ticks:long}` | <span data-ttu-id="2e9d7-1182">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1182">`123456789`, `-123456789`</span></span> | <span data-ttu-id="2e9d7-1183">匹配有效的 `long` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1183">Matches a valid `long` value.</span></span>|
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="2e9d7-1184">字符串必须至少为 4 个字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1184">String must be at least 4 characters.</span></span>|
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | <span data-ttu-id="2e9d7-1185">字符串最多包含 8 个字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1185">String has maximum of 8 characters.</span></span>|
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="2e9d7-1186">字符串必须正好为 12 个字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1186">String must be exactly 12 characters long.</span></span>|
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="2e9d7-1187">字符串必须至少为 8 个字符，且最多包含 16 个字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1187">String must be at least 8 and has maximum of 16 characters.</span></span>|
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="2e9d7-1188">整数值必须至少为 18 个字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1188">Integer value must be at least 18.</span></span>|
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="2e9d7-1189">整数值最多包含 120 个字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1189">Integer value maximum of 120.</span></span>|
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="2e9d7-1190">整数值必须至少为 18 个字符，且最多包含 120 个字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1190">Integer value must be at least 18 and maximum of 120.</span></span>|
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="2e9d7-1191">字符串必须由一个或多个字母字符组成，`a`-`z`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1191">String must consist of one or more alphabetical characters `a`-`z`.</span></span> <span data-ttu-id="2e9d7-1192">不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1192">Case-insensitive.</span></span>|
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="2e9d7-1193">字符串必须与正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1193">String must match the regular expression.</span></span> <span data-ttu-id="2e9d7-1194">请参阅有关定义正则表达式的提示。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1194">See tips about defining a regular expression.</span></span>|
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="2e9d7-1195">用于强制在 URL 生成过程中存在非参数值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1195">Used to enforce that a non-parameter value is present during URL generation.</span></span>|

<span data-ttu-id="2e9d7-1196">可向单个参数应用多个由冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1196">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="2e9d7-1197">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1197">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="2e9d7-1198">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1198">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="2e9d7-1199">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1199">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="2e9d7-1200">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1200">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="2e9d7-1201">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1201">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="2e9d7-1202">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1202">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="2e9d7-1203">正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1203">Regular expressions</span></span>

<span data-ttu-id="2e9d7-1204">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1204">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="2e9d7-1205">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1205">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="2e9d7-1206">正则表达式与路由和 C# 语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1206">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="2e9d7-1207">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1207">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="2e9d7-1208">在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1208">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing:</span></span>

* <span data-ttu-id="2e9d7-1209">表达式必须将字符串中提供的单反斜杠 `\` 字符作为源代码中的双反斜杠 `\\` 字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1209">The expression must have the single backslash `\` characters provided in the string as double backslash `\\` characters in the source code.</span></span>
* <span data-ttu-id="2e9d7-1210">正则表达式必须使用 `\\` 对 `\` 字符串转义字符进行转义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1210">The regular expression must us `\\` in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="2e9d7-1211">使用[逐字字符串文本](/dotnet/csharp/language-reference/keywords/string)时，正则表达式不需要 `\\`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1211">The regular expression doesn't require `\\` when using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="2e9d7-1212">要对路由参数分隔符 `{`、`}`、`[`、`]` 进行转义，请将表达式 `{{`、`}`、`[[`、`]]` 中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1212">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression `{{`, `}`, `[[`, `]]`.</span></span> <span data-ttu-id="2e9d7-1213">下表展示了正则表达式和转义版本：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1213">The following table shows a regular expression and the escaped version:</span></span>

| <span data-ttu-id="2e9d7-1214">正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1214">Regular Expression</span></span>    | <span data-ttu-id="2e9d7-1215">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1215">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="2e9d7-1216">路由中使用的正则表达式通常以脱字号 `^` 开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1216">Regular expressions used in routing often start with the caret `^` character and match starting position of the string.</span></span> <span data-ttu-id="2e9d7-1217">表达式通常以美元符号 `$` 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1217">The expressions often end with the dollar sign `$` character and match end of the string.</span></span> <span data-ttu-id="2e9d7-1218">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1218">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="2e9d7-1219">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1219">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="2e9d7-1220">下表提供了示例并说明了它们匹配或匹配失败的原因。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1220">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="2e9d7-1221">表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1221">Expression</span></span>   | <span data-ttu-id="2e9d7-1222">String</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1222">String</span></span>    | <span data-ttu-id="2e9d7-1223">匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1223">Match</span></span> | <span data-ttu-id="2e9d7-1224">注释</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1224">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-1225">hello</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1225">hello</span></span>     | <span data-ttu-id="2e9d7-1226">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1226">Yes</span></span>   | <span data-ttu-id="2e9d7-1227">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1227">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-1228">123abc456</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1228">123abc456</span></span> | <span data-ttu-id="2e9d7-1229">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1229">Yes</span></span>   | <span data-ttu-id="2e9d7-1230">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1230">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-1231">mz</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1231">mz</span></span>        | <span data-ttu-id="2e9d7-1232">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1232">Yes</span></span>   | <span data-ttu-id="2e9d7-1233">匹配表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1233">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-1234">MZ</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1234">MZ</span></span>        | <span data-ttu-id="2e9d7-1235">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1235">Yes</span></span>   | <span data-ttu-id="2e9d7-1236">不区分大小写</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1236">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="2e9d7-1237">hello</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1237">hello</span></span>     | <span data-ttu-id="2e9d7-1238">否</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1238">No</span></span>    | <span data-ttu-id="2e9d7-1239">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1239">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="2e9d7-1240">123abc456</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1240">123abc456</span></span> | <span data-ttu-id="2e9d7-1241">否</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1241">No</span></span>    | <span data-ttu-id="2e9d7-1242">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1242">See `^` and `$` above</span></span> |

<span data-ttu-id="2e9d7-1243">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1243">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="2e9d7-1244">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1244">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="2e9d7-1245">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1245">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="2e9d7-1246">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1246">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="2e9d7-1247">已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1247">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="2e9d7-1248">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1248">Custom route constraints</span></span>

<span data-ttu-id="2e9d7-1249">除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1249">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="2e9d7-1250"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1250">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="2e9d7-1251">若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1251">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="2e9d7-1252"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1252">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="2e9d7-1253">应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1253">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="2e9d7-1254">例如：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1254">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="2e9d7-1255">然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1255">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="2e9d7-1256">例如：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1256">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a><span data-ttu-id="2e9d7-1257">参数转换器参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1257">Parameter transformer reference</span></span>

<span data-ttu-id="2e9d7-1258">参数转换器：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1258">Parameter transformers:</span></span>

* <span data-ttu-id="2e9d7-1259">在为 <xref:Microsoft.AspNetCore.Routing.Route> 生成链接时执行。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1259">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="2e9d7-1260">实现 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1260">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="2e9d7-1261">使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1261">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="2e9d7-1262">获取参数的路由值并将其转换为新的字符串值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1262">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="2e9d7-1263">在生成的链接中使用转换后的值的结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1263">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="2e9d7-1264">例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1264">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="2e9d7-1265">若要在路由模式中使用参数转换器，请先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1265">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="2e9d7-1266">框架使用参数转化器来转换进行终结点解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1266">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="2e9d7-1267">例如，ASP.NET Core MVC 使用参数转换器来转换用于匹配 `area``controller``action` 和 `page` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1267">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="2e9d7-1268">使用上述路由，操作 `SubscriptionManagementController.GetAll` 与 URI `/subscription-management/get-all` 匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1268">With the preceding route, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="2e9d7-1269">参数转换器不会更改用于生成链接的路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1269">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="2e9d7-1270">例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1270">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="2e9d7-1271">对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1271">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="2e9d7-1272">ASP.NET Core MVC 还具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1272">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="2e9d7-1273">该约定将指定的参数转换器应用于应用中的所有属性路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1273">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="2e9d7-1274">在替换属性路径令牌时，参数转换器将转换这些令牌。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1274">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="2e9d7-1275">有关详细信息，请参阅[使用参数转换器自定义标记替换](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1275">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* <span data-ttu-id="2e9d7-1276">Razor Pages 具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1276">Razor Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="2e9d7-1277">此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1277">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="2e9d7-1278">参数转换器转换 Razor Pages 路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1278">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="2e9d7-1279">有关详细信息，请参阅[使用参数转换器自定义页面路由](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1279">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

## <a name="url-generation-reference"></a><span data-ttu-id="2e9d7-1280">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1280">URL generation reference</span></span>

<span data-ttu-id="2e9d7-1281">以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1281">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="2e9d7-1282">上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1282">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="2e9d7-1283">字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1283">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="2e9d7-1284">有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1284">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="2e9d7-1285"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1285">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="2e9d7-1286">由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1286">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="2e9d7-1287">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1287">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2e9d7-1288">在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1288">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="2e9d7-1289">忽略与参数不匹配的环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1289">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="2e9d7-1290">在显式提供的值会覆盖环境值的情况下，也会忽略环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1290">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="2e9d7-1291">在 URL 中将从左到右进行匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1291">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="2e9d7-1292">显式提供但与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1292">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="2e9d7-1293">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1293">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="2e9d7-1294">环境值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1294">Ambient Values</span></span>                     | <span data-ttu-id="2e9d7-1295">显式值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1295">Explicit Values</span></span>                        | <span data-ttu-id="2e9d7-1296">结果</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1296">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="2e9d7-1297">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1297">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-1298">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1298">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="2e9d7-1299">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1299">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-1300">控制器 =“Order”，操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1300">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="2e9d7-1301">控制器 = “Home”，颜色 = “Red”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1301">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="2e9d7-1302">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1302">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="2e9d7-1303">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1303">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-1304">操作 =“About”，颜色 =“Red”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1304">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="2e9d7-1305">如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1305">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="2e9d7-1306">当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1306">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="2e9d7-1307">复杂段</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1307">Complex segments</span></span>

<span data-ttu-id="2e9d7-1308">复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1308">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="2e9d7-1309">请参阅[此代码](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1309">See [this code](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="2e9d7-1310">ASP.NET Core 无法使用[代码示例](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1310">The [code sample](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2e9d7-1311">路由负责将请求 URI 映射到路由处理程序和调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1311">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="2e9d7-1312">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1312">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="2e9d7-1313">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1313">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="2e9d7-1314">如果使用应用中配置的路由，路由能生成映射到路由处理程序的 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1314">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

<span data-ttu-id="2e9d7-1315">要在 ASP.NET Core 2.1 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1315">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> <span data-ttu-id="2e9d7-1316">本文档介绍较低级别的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1316">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="2e9d7-1317">有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1317">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="2e9d7-1318">有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1318">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="2e9d7-1319">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1319">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="2e9d7-1320">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1320">Routing basics</span></span>

<span data-ttu-id="2e9d7-1321">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1321">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="2e9d7-1322">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1322">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="2e9d7-1323">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1323">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="2e9d7-1324">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1324">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="2e9d7-1325">开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1325">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="2e9d7-1326">Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1326">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="2e9d7-1327">也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1327">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="2e9d7-1328">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1328">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="2e9d7-1329">Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1329">Razor Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="2e9d7-1330">还可以使用其他约定来自定义 Razor Pages 路由行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1330">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="2e9d7-1331">有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1331">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="2e9d7-1332">借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1332">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="2e9d7-1333">此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1333">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="2e9d7-1334">路由使用 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由实现来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1334">Routing uses routes implementations of <xref:Microsoft.AspNetCore.Routing.IRouter> to:</span></span>

* <span data-ttu-id="2e9d7-1335">将传入请求映射到路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1335">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="2e9d7-1336">生成响应中使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1336">Generate the URLs used in responses.</span></span>

<span data-ttu-id="2e9d7-1337">默认情况下，应用只有一个路由集合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1337">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="2e9d7-1338">当请求到达时，将按照路由在集合中的存在顺序来处理路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1338">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="2e9d7-1339">框架试图通过在集合中的每个路由上调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法，将传入请求的 URL 与集合中的路由进行匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1339">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="2e9d7-1340">响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1340">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="2e9d7-1341">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1341">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="2e9d7-1342">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1342">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="2e9d7-1343">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1343">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="2e9d7-1344"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1344"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="2e9d7-1345">应用模型（如 MVC/Razor Pages）注册了其所有具有可预测的路由方案实现的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1345">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="2e9d7-1346">响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1346">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="2e9d7-1347">URL 生成基于支持任意可扩展性的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1347">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="2e9d7-1348"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1348"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
<span data-ttu-id="2e9d7-1349">路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1349">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="2e9d7-1350">[ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1350">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="2e9d7-1351">要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1351">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="2e9d7-1352">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1352">URL matching</span></span>

<span data-ttu-id="2e9d7-1353">URL 匹配是一个过程，通过该过程，路由可向处理程序调度传入请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1353">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="2e9d7-1354">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1354">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="2e9d7-1355">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1355">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="2e9d7-1356">传入请求将进入 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>，后者将对序列中的每个路由调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1356">Incoming requests enter the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="2e9d7-1357"><xref:Microsoft.AspNetCore.Routing.IRouter> 实例将选择是否通过将 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) 设置为非 NULL <xref:Microsoft.AspNetCore.Http.RequestDelegate> 来处理请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1357">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="2e9d7-1358">如果路由为请求设置处理程序，将停止路由处理，并调用处理程序来处理该请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1358">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="2e9d7-1359">如果未找到用于处理请求的路由处理程序，中间件会将请求传递给请求管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1359">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="2e9d7-1360"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的主要输入是与当前请求关联的 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1360">The primary input to <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="2e9d7-1361">[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 和 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) 是路由匹配后设置的输出。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1361">The [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="2e9d7-1362">调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的匹配还可根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1362">A match that calls <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> also sets the properties of the [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="2e9d7-1363">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1363">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="2e9d7-1364">这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1364">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="2e9d7-1365">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1365">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="2e9d7-1366">提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1366"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="2e9d7-1367">这些值是开发者定义的，不会影响通过任何方式路由的行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1367">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="2e9d7-1368">此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1368">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="2e9d7-1369">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1369">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="2e9d7-1370">路由可以相互嵌套。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1370">Routes can be nested inside of one another.</span></span> <span data-ttu-id="2e9d7-1371"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1371">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="2e9d7-1372">通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1372">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="2e9d7-1373"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1373">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation"></a><span data-ttu-id="2e9d7-1374">URL 生成</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1374">URL generation</span></span>

<span data-ttu-id="2e9d7-1375">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1375">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="2e9d7-1376">这需要考虑处理程序与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1376">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="2e9d7-1377">URL 遵循类似的迭代过程，但开头是将用户或框架代码调用到路由集合的 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1377">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="2e9d7-1378">每个路由按顺序调用其 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法，直到返回非 NULL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1378">Each *route* has its <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="2e9d7-1379"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的主输入有：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1379">The primary inputs to <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> are:</span></span>

* [<span data-ttu-id="2e9d7-1380">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1380">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [<span data-ttu-id="2e9d7-1381">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1381">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [<span data-ttu-id="2e9d7-1382">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1382">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

<span data-ttu-id="2e9d7-1383">路由主要使用 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 和 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 提供的路由值来确定是否可能生成 URL 以及要包括哪些值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1383">Routes primarily use the route values provided by <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> and <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="2e9d7-1384"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 是通过匹配当前请求而生成的路由值集。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1384">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="2e9d7-1385">与此相反，<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 是指定如何为当前操作生成所需 URL 的路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1385">In contrast, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="2e9d7-1386">如果路由应获取与当前上下文关联的服务或其他数据时，则提供 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1386">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="2e9d7-1387">将 [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) 视为 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) 的一组替代。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1387">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="2e9d7-1388">URL 生成尝试重复使用当前请求中的路由值，以便使用相同路由或路由值生成链接的 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1388">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="2e9d7-1389"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的输出是 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1389">The output of <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is a <xref:Microsoft.AspNetCore.Routing.VirtualPathData>.</span></span> <span data-ttu-id="2e9d7-1390"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> 是 <xref:Microsoft.AspNetCore.Routing.RouteData> 的并行值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1390"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> is a parallel of <xref:Microsoft.AspNetCore.Routing.RouteData>.</span></span> <span data-ttu-id="2e9d7-1391"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> 包含输出 URL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 以及路由应该设置的某些其他属性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1391"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> contains the <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="2e9d7-1392">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 属性包含路由生成的虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1392">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="2e9d7-1393">你可能需要进一步处理路径，具体取决于你的需求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1393">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="2e9d7-1394">如果要在 HTML 中呈现生成的 URL，请预置应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1394">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="2e9d7-1395">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) 是对已成功生成 URL 的路由的引用。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1395">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="2e9d7-1396">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 属性是生成 URL 的路由的其他相关数据的字典。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1396">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="2e9d7-1397">这是 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 的并性值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1397">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

### <a name="create-routes"></a><span data-ttu-id="2e9d7-1398">创建路由</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1398">Create routes</span></span>

<span data-ttu-id="2e9d7-1399">路由提供 <xref:Microsoft.AspNetCore.Routing.Route> 类，作为 <xref:Microsoft.AspNetCore.Routing.IRouter> 的标准实现。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1399">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="2e9d7-1400"><xref:Microsoft.AspNetCore.Routing.Route> 使用 route template 语法来定义模式，以便在调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 时匹配 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1400"><xref:Microsoft.AspNetCore.Routing.Route> uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="2e9d7-1401">调用 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 时，<xref:Microsoft.AspNetCore.Routing.Route> 使用同一路由模板生成 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1401"><xref:Microsoft.AspNetCore.Routing.Route> uses the same route template to generate a URL when <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is called.</span></span>

<span data-ttu-id="2e9d7-1402">大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1402">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="2e9d7-1403">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1403">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="2e9d7-1404"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1404"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="2e9d7-1405"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1405"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="2e9d7-1406">默认处理程序是 `IRouter`，该处理程序可能无法处理该请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1406">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="2e9d7-1407">例如，ASP.NET Core MVC 通常被配置为默认处理程序，仅处理与可用控制器和操作匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1407">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="2e9d7-1408">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1408">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="2e9d7-1409">以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1409">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2e9d7-1410">此模板与 URL 路径相匹配，并且提取路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1410">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="2e9d7-1411">例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1411">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="2e9d7-1412">路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1412">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="2e9d7-1413">路由参数已命名。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1413">Route parameters are named.</span></span> <span data-ttu-id="2e9d7-1414">参数通过将参数名称括在大括号 `{ ... }` 中来定义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1414">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="2e9d7-1415">上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1415">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="2e9d7-1416">这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1416">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="2e9d7-1417">路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1417">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="2e9d7-1418">路由参数名称后面的问号 (`?`) 定义了可选参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1418">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="2e9d7-1419">路由匹配时，具有默认值的路由参数始终会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1419">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="2e9d7-1420">如果没有相应的 URL 路径段，则可选参数不会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1420">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="2e9d7-1421">有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1421">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="2e9d7-1422">在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1422">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="2e9d7-1423">此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1423">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="2e9d7-1424">路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1424">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="2e9d7-1425">在此示例中，路由值 `id` 必须可转换为整数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1425">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="2e9d7-1426">有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1426">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="2e9d7-1427">其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1427">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="2e9d7-1428">这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1428">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="2e9d7-1429">以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1429">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

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
> <span data-ttu-id="2e9d7-1430">定义约束和默认值的内联语法对于简单路由很方便。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1430">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="2e9d7-1431">但是，存在内联语法不支持的方案，例如数据令牌。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1431">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="2e9d7-1432">以下示例演示了一些其他方案：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1432">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="2e9d7-1433">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1433">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="2e9d7-1434">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1434">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="2e9d7-1435">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1435">Default values can be specified in the route template.</span></span> <span data-ttu-id="2e9d7-1436">根据路由参数名称前的星号 (`*`) 外观，`article` 路由参数被定义为 catch-all。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1436">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="2e9d7-1437">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1437">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="2e9d7-1438">以下示例添加了路由约束和数据令牌：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1438">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="2e9d7-1439">上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1439">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="2e9d7-1441">路由类 URL 生成</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1441">Route class URL generation</span></span>

<span data-ttu-id="2e9d7-1442"><xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1442">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="2e9d7-1443">从逻辑上来说，这是匹配 URL 路径的反向过程。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1443">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="2e9d7-1444">为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1444">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="2e9d7-1445">将生成什么值？</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1445">What values would be produced?</span></span> <span data-ttu-id="2e9d7-1446">这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1446">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="2e9d7-1447">以下示例使用常规 ASP.NET Core MVC 默认路由：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1447">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="2e9d7-1448">使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1448">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="2e9d7-1449">路由值将替换为相应的路由参数，以形成 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1449">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="2e9d7-1450">由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1450">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="2e9d7-1451">使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1451">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="2e9d7-1452">提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1452">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="2e9d7-1453">已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1453">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="2e9d7-1454">使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1454">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="2e9d7-1455">有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1455">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="2e9d7-1456">使用路由中间件</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1456">Use routing middleware</span></span>

<span data-ttu-id="2e9d7-1457">引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1457">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="2e9d7-1458">将路由添加到 `Startup.ConfigureServices` 中的服务容器：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1458">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="2e9d7-1459">必须在 `Startup.Configure` 方法中配置路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1459">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="2e9d7-1460">该示例应用使用以下 API：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1460">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="2e9d7-1461"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>：仅匹配 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1461"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="2e9d7-1462">下表显示了具有给定 URI 的响应。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1462">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="2e9d7-1463">URI</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1463">URI</span></span>                    | <span data-ttu-id="2e9d7-1464">响应</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1464">Response</span></span>                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | <span data-ttu-id="2e9d7-1465">Hello!</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1465">Hello!</span></span> <span data-ttu-id="2e9d7-1466">Route values: [operation, create], [id, 3]</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1466">Route values: [operation, create], [id, 3]</span></span> |
| `/package/track/-3`    | <span data-ttu-id="2e9d7-1467">Hello!</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1467">Hello!</span></span> <span data-ttu-id="2e9d7-1468">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1468">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/-3/`   | <span data-ttu-id="2e9d7-1469">Hello!</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1469">Hello!</span></span> <span data-ttu-id="2e9d7-1470">Route values: [operation, track], [id, -3]</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1470">Route values: [operation, track], [id, -3]</span></span> |
| `/package/track/`      | <span data-ttu-id="2e9d7-1471">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1471">The request falls through, no match.</span></span>              |
| `GET /hello/Joe`       | <span data-ttu-id="2e9d7-1472">Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1472">Hi, Joe!</span></span>                                          |
| `POST /hello/Joe`      | <span data-ttu-id="2e9d7-1473">请求失败，仅匹配 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1473">The request falls through, matches HTTP GET only.</span></span> |
| `GET /hello/Joe/Smith` | <span data-ttu-id="2e9d7-1474">请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1474">The request falls through, no match.</span></span>              |

<span data-ttu-id="2e9d7-1475">如果要配置单个路由，请在 `IRouter` 实例中调用 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> 传入。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1475">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="2e9d7-1476">无需使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1476">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

<span data-ttu-id="2e9d7-1477">框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1477">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

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

<span data-ttu-id="2e9d7-1478">一些列出的方法（如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>）需要 <xref:Microsoft.AspNetCore.Http.RequestDelegate>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1478">Some of listed methods, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, require a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="2e9d7-1479">路由匹配时，<xref:Microsoft.AspNetCore.Http.RequestDelegate> 会用作路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1479">The <xref:Microsoft.AspNetCore.Http.RequestDelegate> is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="2e9d7-1480">此系列中的其他方法允许配置中间件管道，将其用作路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1480">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="2e9d7-1481">如果 `Map*` 方法不接受处理程序（例如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>），则它将使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1481">If the `Map*` method doesn't accept a handler, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

<span data-ttu-id="2e9d7-1482">`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1482">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="2e9d7-1483">有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1483">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="2e9d7-1484">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1484">Route template reference</span></span>

<span data-ttu-id="2e9d7-1485">如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1485">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="2e9d7-1486">可在路由段中定义多个路由参数，但必须由文本值隔开。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1486">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="2e9d7-1487">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1487">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="2e9d7-1488">这些路由参数必须具有名称，且可能指定了其他属性。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1488">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="2e9d7-1489">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1489">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="2e9d7-1490">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1490">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="2e9d7-1491">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1491">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="2e9d7-1492">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1492">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="2e9d7-1493">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1493">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="2e9d7-1494">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1494">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="2e9d7-1495">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1495">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="2e9d7-1496">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1496">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="2e9d7-1497">可以使用星号 (`*`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1497">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="2e9d7-1498">这称为 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1498">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="2e9d7-1499">例如，`blog/{*slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1499">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="2e9d7-1500">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1500">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="2e9d7-1501">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1501">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="2e9d7-1502">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1502">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="2e9d7-1503">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1503">Note the escaped forward slash.</span></span>

<span data-ttu-id="2e9d7-1504">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1504">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="2e9d7-1505">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1505">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="2e9d7-1506">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1506">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="2e9d7-1507">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1507">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="2e9d7-1508">可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1508">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="2e9d7-1509">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1509">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="2e9d7-1510">在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1510">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="2e9d7-1511">如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1511">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="2e9d7-1512">通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1512">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="2e9d7-1513">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1513">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="2e9d7-1514">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1514">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="2e9d7-1515">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1515">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="2e9d7-1516">下表演示了示例路由模板及其行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1516">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="2e9d7-1517">路由模板</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1517">Route Template</span></span>                           | <span data-ttu-id="2e9d7-1518">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1518">Example Matching URI</span></span>    | <span data-ttu-id="2e9d7-1519">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1519">The request URI&hellip;</span></span>                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | <span data-ttu-id="2e9d7-1520">仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1520">Only matches the single path `/hello`.</span></span>                                     |
| `{Page=Home}`                            | `/`                     | <span data-ttu-id="2e9d7-1521">匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1521">Matches and sets `Page` to `Home`.</span></span>                                         |
| `{Page=Home}`                            | `/Contact`              | <span data-ttu-id="2e9d7-1522">匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1522">Matches and sets `Page` to `Contact`.</span></span>                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | <span data-ttu-id="2e9d7-1523">映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1523">Maps to the `Products` controller and `List` action.</span></span>                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | <span data-ttu-id="2e9d7-1524">映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1524">Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | <span data-ttu-id="2e9d7-1525">映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1525">Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="2e9d7-1526">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1526">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="2e9d7-1527">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1527">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="2e9d7-1528">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1528">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="2e9d7-1529">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1529">Route constraint reference</span></span>

<span data-ttu-id="2e9d7-1530">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1530">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="2e9d7-1531">路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1531">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="2e9d7-1532">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1532">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="2e9d7-1533">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1533">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="2e9d7-1534">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1534">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="2e9d7-1535">请勿将约束用于“输入验证”。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1535">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="2e9d7-1536">如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求” 。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1536">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="2e9d7-1537">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1537">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="2e9d7-1538">下表演示示例路由约束及其预期行为。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1538">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="2e9d7-1539">约束</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1539">constraint</span></span> | <span data-ttu-id="2e9d7-1540">示例</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1540">Example</span></span> | <span data-ttu-id="2e9d7-1541">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1541">Example Matches</span></span> | <span data-ttu-id="2e9d7-1542">说明</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1542">Notes</span></span> |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | <span data-ttu-id="2e9d7-1543">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1543">`123456789`, `-123456789`</span></span> | <span data-ttu-id="2e9d7-1544">匹配任何整数</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1544">Matches any integer</span></span> |
| `bool` | `{active:bool}` | <span data-ttu-id="2e9d7-1545">`true`，`FALSE`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1545">`true`, `FALSE`</span></span> | <span data-ttu-id="2e9d7-1546">匹配 `true`或 `false`（区分大小写）</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1546">Matches `true` or `false` (case-insensitive)</span></span> |
| `datetime` | `{dob:datetime}` | <span data-ttu-id="2e9d7-1547">`2016-12-31`，`2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1547">`2016-12-31`, `2016-12-31 7:32pm`</span></span> | <span data-ttu-id="2e9d7-1548">在固定区域性中匹配有效的 `DateTime` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1548">Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-1549">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1549">See  preceding warning.</span></span>|
| `decimal` | `{price:decimal}` | <span data-ttu-id="2e9d7-1550">`49.99`，`-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1550">`49.99`, `-1,000.01`</span></span> | <span data-ttu-id="2e9d7-1551">在固定区域性中匹配有效的 `decimal` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1551">Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-1552">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1552">See  preceding warning.</span></span>|
| `double` | `{weight:double}` | <span data-ttu-id="2e9d7-1553">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1553">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="2e9d7-1554">在固定区域性中匹配有效的 `double` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1554">Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-1555">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1555">See  preceding warning.</span></span>|
| `float` | `{weight:float}` | <span data-ttu-id="2e9d7-1556">`1.234`，`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1556">`1.234`, `-1,001.01e8`</span></span> | <span data-ttu-id="2e9d7-1557">在固定区域性中匹配有效的 `float` 值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1557">Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="2e9d7-1558">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1558">See  preceding warning.</span></span>|
| `guid` | `{id:guid}` | <span data-ttu-id="2e9d7-1559">`CD2C1638-1638-72D5-1638-DEADBEEF1638`，`{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1559">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="2e9d7-1560">匹配有效的 `Guid` 值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1560">Matches a valid `Guid` value</span></span> |
| `long` | `{ticks:long}` | <span data-ttu-id="2e9d7-1561">`123456789`，`-123456789`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1561">`123456789`, `-123456789`</span></span> | <span data-ttu-id="2e9d7-1562">匹配有效的 `long` 值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1562">Matches a valid `long` value</span></span> |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | <span data-ttu-id="2e9d7-1563">字符串必须至少为 4 个字符</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1563">String must be at least 4 characters</span></span> |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | <span data-ttu-id="2e9d7-1564">字符串不得超过 8 个字符</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1564">String must be no more than 8 characters</span></span> |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | <span data-ttu-id="2e9d7-1565">字符串必须正好为 12 个字符</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1565">String must be exactly 12 characters long</span></span> |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | <span data-ttu-id="2e9d7-1566">字符串必须至少为 8 个字符，且不得超过 16 个字符</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1566">String must be at least 8 and no more than 16 characters long</span></span> |
| `min(value)` | `{age:min(18)}` | `19` | <span data-ttu-id="2e9d7-1567">整数值必须至少为 18</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1567">Integer value must be at least 18</span></span> |
| `max(value)` | `{age:max(120)}` | `91` | <span data-ttu-id="2e9d7-1568">整数值不得超过 120</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1568">Integer value must be no more than 120</span></span> |
| `range(min,max)` | `{age:range(18,120)}` | `91` | <span data-ttu-id="2e9d7-1569">整数值必须至少为 18，且不得超过 120</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1569">Integer value must be at least 18 but no more than 120</span></span> |
| `alpha` | `{name:alpha}` | `Rick` | <span data-ttu-id="2e9d7-1570">字符串必须由一个或多个字母字符（`a`-`z`，区分大小写）组成</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1570">String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive)</span></span> |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | <span data-ttu-id="2e9d7-1571">字符串必须匹配正则表达式（参见有关定义正则表达式的提示）</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1571">String must match the regular expression (see tips about defining a regular expression)</span></span> |
| `required` | `{name:required}` | `Rick` | <span data-ttu-id="2e9d7-1572">用于强制在 URL 生成过程中存在非参数值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1572">Used to enforce that a non-parameter value is present during URL generation</span></span> |

<span data-ttu-id="2e9d7-1573">可向单个参数应用多个由冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1573">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="2e9d7-1574">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1574">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="2e9d7-1575">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1575">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="2e9d7-1576">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1576">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="2e9d7-1577">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1577">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="2e9d7-1578">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1578">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="2e9d7-1579">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1579">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="2e9d7-1580">正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1580">Regular expressions</span></span>

<span data-ttu-id="2e9d7-1581">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1581">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="2e9d7-1582">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1582">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="2e9d7-1583">正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1583">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="2e9d7-1584">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1584">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="2e9d7-1585">要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1585">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="2e9d7-1586">要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1586">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="2e9d7-1587">下表展示了正则表达式和转义版本。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1587">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="2e9d7-1588">正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1588">Regular Expression</span></span>    | <span data-ttu-id="2e9d7-1589">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1589">Escaped Regular Expression</span></span>     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

<span data-ttu-id="2e9d7-1590">路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1590">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="2e9d7-1591">表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1591">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="2e9d7-1592">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1592">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="2e9d7-1593">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1593">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="2e9d7-1594">下表提供了示例并说明了它们匹配或匹配失败的原因。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1594">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="2e9d7-1595">表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1595">Expression</span></span>   | <span data-ttu-id="2e9d7-1596">String</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1596">String</span></span>    | <span data-ttu-id="2e9d7-1597">匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1597">Match</span></span> | <span data-ttu-id="2e9d7-1598">注释</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1598">Comment</span></span>               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-1599">hello</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1599">hello</span></span>     | <span data-ttu-id="2e9d7-1600">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1600">Yes</span></span>   | <span data-ttu-id="2e9d7-1601">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1601">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-1602">123abc456</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1602">123abc456</span></span> | <span data-ttu-id="2e9d7-1603">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1603">Yes</span></span>   | <span data-ttu-id="2e9d7-1604">子字符串匹配</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1604">Substring matches</span></span>     |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-1605">mz</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1605">mz</span></span>        | <span data-ttu-id="2e9d7-1606">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1606">Yes</span></span>   | <span data-ttu-id="2e9d7-1607">匹配表达式</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1607">Matches expression</span></span>    |
| `[a-z]{2}`   | <span data-ttu-id="2e9d7-1608">MZ</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1608">MZ</span></span>        | <span data-ttu-id="2e9d7-1609">是</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1609">Yes</span></span>   | <span data-ttu-id="2e9d7-1610">不区分大小写</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1610">Not case sensitive</span></span>    |
| `^[a-z]{2}$` | <span data-ttu-id="2e9d7-1611">hello</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1611">hello</span></span>     | <span data-ttu-id="2e9d7-1612">否</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1612">No</span></span>    | <span data-ttu-id="2e9d7-1613">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1613">See `^` and `$` above</span></span> |
| `^[a-z]{2}$` | <span data-ttu-id="2e9d7-1614">123abc456</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1614">123abc456</span></span> | <span data-ttu-id="2e9d7-1615">否</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1615">No</span></span>    | <span data-ttu-id="2e9d7-1616">参阅上述 `^` 和 `$`</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1616">See `^` and `$` above</span></span> |

<span data-ttu-id="2e9d7-1617">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1617">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="2e9d7-1618">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1618">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="2e9d7-1619">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1619">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="2e9d7-1620">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1620">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="2e9d7-1621">已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1621">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="2e9d7-1622">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1622">Custom Route Constraints</span></span>

<span data-ttu-id="2e9d7-1623">除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1623">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="2e9d7-1624"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1624">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="2e9d7-1625">若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1625">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="2e9d7-1626"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1626">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="2e9d7-1627">应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1627">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="2e9d7-1628">例如：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1628">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="2e9d7-1629">然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1629">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="2e9d7-1630">例如：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1630">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a><span data-ttu-id="2e9d7-1631">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1631">URL generation reference</span></span>

<span data-ttu-id="2e9d7-1632">以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1632">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="2e9d7-1633">上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1633">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="2e9d7-1634">字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1634">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="2e9d7-1635">有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1635">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="2e9d7-1636"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1636">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="2e9d7-1637">由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1637">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="2e9d7-1638">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1638">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="2e9d7-1639">在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1639">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="2e9d7-1640">忽略与参数不匹配的环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1640">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="2e9d7-1641">在显式提供的值会覆盖环境值的情况下，也会忽略环境值。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1641">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="2e9d7-1642">在 URL 中将从左到右进行匹配。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1642">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="2e9d7-1643">显式提供但与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1643">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="2e9d7-1644">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1644">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="2e9d7-1645">环境值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1645">Ambient Values</span></span>                     | <span data-ttu-id="2e9d7-1646">显式值</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1646">Explicit Values</span></span>                        | <span data-ttu-id="2e9d7-1647">结果</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1647">Result</span></span>                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| <span data-ttu-id="2e9d7-1648">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1648">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-1649">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1649">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="2e9d7-1650">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1650">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-1651">控制器 =“Order”，操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1651">controller = "Order", action = "About"</span></span> | `/Order/About`          |
| <span data-ttu-id="2e9d7-1652">控制器 = “Home”，颜色 = “Red”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1652">controller = "Home", color = "Red"</span></span> | <span data-ttu-id="2e9d7-1653">操作 =“About”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1653">action = "About"</span></span>                       | `/Home/About`           |
| <span data-ttu-id="2e9d7-1654">控制器 =“Home”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1654">controller = "Home"</span></span>                | <span data-ttu-id="2e9d7-1655">操作 =“About”，颜色 =“Red”</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1655">action = "About", color = "Red"</span></span>        | `/Home/About?color=Red` |

<span data-ttu-id="2e9d7-1656">如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1656">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="2e9d7-1657">当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1657">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="2e9d7-1658">复杂段</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1658">Complex segments</span></span>

<span data-ttu-id="2e9d7-1659">复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1659">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="2e9d7-1660">请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1660">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="2e9d7-1661">ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。</span><span class="sxs-lookup"><span data-stu-id="2e9d7-1661">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>

::: moniker-end
