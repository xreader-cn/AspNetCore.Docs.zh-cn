---
<span data-ttu-id="6d72c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-102">'Blazor'</span></span>
- <span data-ttu-id="6d72c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-103">'Identity'</span></span>
- <span data-ttu-id="6d72c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-105">'Razor'</span></span>
- <span data-ttu-id="6d72c-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-106">'SignalR' uid:</span></span> 

---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="6d72c-107">ASP.NET Core 中的路由</span><span class="sxs-lookup"><span data-stu-id="6d72c-107">Routing in ASP.NET Core</span></span>

<span data-ttu-id="6d72c-108">作者：[Ryan Nowak](https://github.com/rynowak)、[Kirk Larkinn](https://twitter.com/serpent5) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6d72c-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6d72c-109">路由负责匹配传入的 HTTP 请求，然后将这些请求发送到应用的可执行终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-109">Routing is responsible for matching incoming HTTP requests and dispatching those requests to the app's executable endpoints.</span></span> <span data-ttu-id="6d72c-110">[终结点](#endpoint)是应用的可执行请求处理代码单元。</span><span class="sxs-lookup"><span data-stu-id="6d72c-110">[Endpoints](#endpoint) are the app's units of executable request-handling code.</span></span> <span data-ttu-id="6d72c-111">终结点在应用中进行定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-111">Endpoints are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="6d72c-112">终结点匹配过程可以从请求的 URL 中提取值，并为请求处理提供这些值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-112">The endpoint matching process can extract values from the request's URL and provide those values for request processing.</span></span> <span data-ttu-id="6d72c-113">通过使用应用中的终结点信息，路由还能生成映射到终结点的 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-113">Using endpoint information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="6d72c-114">应用可以使用以下内容配置路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-114">Apps can configure routing using:</span></span>

- <span data-ttu-id="6d72c-115">Controllers</span><span class="sxs-lookup"><span data-stu-id="6d72c-115">Controllers</span></span>
- Razor<span data-ttu-id="6d72c-116"> Pages</span><span class="sxs-lookup"><span data-stu-id="6d72c-116"> Pages</span></span>
- SignalR
- <span data-ttu-id="6d72c-117">gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="6d72c-117">gRPC Services</span></span>
- <span data-ttu-id="6d72c-118">启用终结点的[中间件](xref:fundamentals/middleware/index)，例如[运行状况检查](xref:host-and-deploy/health-checks)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-118">Endpoint-enabled [middleware](xref:fundamentals/middleware/index) such as [Health Checks](xref:host-and-deploy/health-checks).</span></span>
- <span data-ttu-id="6d72c-119">通过路由注册的委托和 Lambda。</span><span class="sxs-lookup"><span data-stu-id="6d72c-119">Delegates and lambdas registered with routing.</span></span>

<span data-ttu-id="6d72c-120">本文档介绍 ASP.NET Core 路由的较低级别详细信息。</span><span class="sxs-lookup"><span data-stu-id="6d72c-120">This document covers low-level details of ASP.NET Core routing.</span></span> <span data-ttu-id="6d72c-121">有关配置路由的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="6d72c-121">For information on configuring routing:</span></span>

* <span data-ttu-id="6d72c-122">对于控制器，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-122">For controllers, see <xref:mvc/controllers/routing>.</span></span>
* <span data-ttu-id="6d72c-123">对于 Razor Pages 约定，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-123">For Razor Pages conventions, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="6d72c-124">本文档中所述的终结点路由系统适用于 ASP.NET Core 3.0 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="6d72c-124">The endpoint routing system described in this document applies to ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="6d72c-125">有关以前基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由系统信息，请使用以下方法之一选择 ASP.NET Core 2.1 版本：</span><span class="sxs-lookup"><span data-stu-id="6d72c-125">For information on the previous routing system based on <xref:Microsoft.AspNetCore.Routing.IRouter>, select the ASP.NET Core 2.1 version using one of the following approaches:</span></span>

* <span data-ttu-id="6d72c-126">以前版本的版本选择器。</span><span class="sxs-lookup"><span data-stu-id="6d72c-126">The version selector for a previous version.</span></span>
* <span data-ttu-id="6d72c-127">选择 [ASP.NET Core 2.1 路由](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-127">Select [ASP.NET Core 2.1 routing](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="6d72c-128">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6d72c-128">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6d72c-129">此文档的下载示例由特定 `Startup` 类启用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-129">The download samples for this document are enabled by a specific `Startup` class.</span></span> <span data-ttu-id="6d72c-130">若要运行特定的示例，请修改 Program.cs，以便调用所需的 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="6d72c-130">To run a specific sample, modify *Program.cs* to call the desired `Startup` class.</span></span>

## <a name="routing-basics"></a><span data-ttu-id="6d72c-131">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="6d72c-131">Routing basics</span></span>

<span data-ttu-id="6d72c-132">所有 ASP.NET Core 模板都包括生成的代码中的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-132">All ASP.NET Core templates include routing in the generated code.</span></span> <span data-ttu-id="6d72c-133">路由在 `Startup.Configure` 中的[中间件](xref:fundamentals/middleware/index)管道中进行注册。</span><span class="sxs-lookup"><span data-stu-id="6d72c-133">Routing is registered in the [middleware](xref:fundamentals/middleware/index) pipeline in `Startup.Configure`.</span></span>

<span data-ttu-id="6d72c-134">以下代码演示路由的基本示例：</span><span class="sxs-lookup"><span data-stu-id="6d72c-134">The following code shows a basic example of routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

<span data-ttu-id="6d72c-135">路由使用一对由 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> 和 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 注册的中间件：</span><span class="sxs-lookup"><span data-stu-id="6d72c-135">Routing uses a pair of middleware, registered by <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>:</span></span>

* <span data-ttu-id="6d72c-136">`UseRouting` 向中间件管道添加路由匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-136">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="6d72c-137">此中间件会查看应用中定义的终结点集，并根据请求选择[最佳匹配](#urlm)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-137">This middleware looks at the set of endpoints defined in the app, and selects the [best match](#urlm) based on the request.</span></span>
* <span data-ttu-id="6d72c-138">`UseEndpoints` 向中间件管道添加终结点执行。</span><span class="sxs-lookup"><span data-stu-id="6d72c-138">`UseEndpoints` adds endpoint execution to the middleware pipeline.</span></span> <span data-ttu-id="6d72c-139">它会运行与所选终结点关联的委托。</span><span class="sxs-lookup"><span data-stu-id="6d72c-139">It runs the delegate associated with the selected endpoint.</span></span>

<span data-ttu-id="6d72c-140">前面的示例包含使用 [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) 方法的单一路由到代码终结点：</span><span class="sxs-lookup"><span data-stu-id="6d72c-140">The preceding example includes a single *route to code* endpoint using the [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) method:</span></span>

* <span data-ttu-id="6d72c-141">当 HTTP `GET` 请求发送到根 URL `/` 时：</span><span class="sxs-lookup"><span data-stu-id="6d72c-141">When an HTTP `GET` request is sent to the root URL `/`:</span></span>
  * <span data-ttu-id="6d72c-142">将执行显示的请求委托。</span><span class="sxs-lookup"><span data-stu-id="6d72c-142">The request delegate shown executes.</span></span>
  * <span data-ttu-id="6d72c-143">`Hello World!` 会写入 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="6d72c-143">`Hello World!` is written to the HTTP response.</span></span> <span data-ttu-id="6d72c-144">默认情况下，根 URL `/` 为 `https://localhost:5001/`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-144">By default, the root URL `/` is `https://localhost:5001/`.</span></span>
* <span data-ttu-id="6d72c-145">如果请求方法不是 `GET` 或根 URL 不是 `/`，则无路由匹配，并返回 HTTP 404。</span><span class="sxs-lookup"><span data-stu-id="6d72c-145">If the request method is not `GET` or the root URL is not `/`, no route matches and an HTTP 404 is returned.</span></span>

### <a name="endpoint"></a><span data-ttu-id="6d72c-146">终结点</span><span class="sxs-lookup"><span data-stu-id="6d72c-146">Endpoint</span></span>

<a name="endpoint"></a>

<span data-ttu-id="6d72c-147">`MapGet` 方法用于定义终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-147">The `MapGet` method is used to define an **endpoint**.</span></span> <span data-ttu-id="6d72c-148">终结点可以：</span><span class="sxs-lookup"><span data-stu-id="6d72c-148">An endpoint is something that can be:</span></span>

* <span data-ttu-id="6d72c-149">通过匹配 URL 和 HTTP 方法来选择。</span><span class="sxs-lookup"><span data-stu-id="6d72c-149">Selected, by matching the URL and HTTP method.</span></span>
* <span data-ttu-id="6d72c-150">通过运行委托来执行。</span><span class="sxs-lookup"><span data-stu-id="6d72c-150">Executed, by running the delegate.</span></span>

<span data-ttu-id="6d72c-151">可通过应用匹配和执行的终结点在 `UseEndpoints` 中进行配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-151">Endpoints that can be matched and executed by the app are configured in `UseEndpoints`.</span></span> <span data-ttu-id="6d72c-152">例如，<xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>、<xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> 和[类似的方法](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions)将请求委托连接到路由系统。</span><span class="sxs-lookup"><span data-stu-id="6d72c-152">For example, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, and [similar methods](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) connect request delegates to the routing system.</span></span>
<span data-ttu-id="6d72c-153">其他方法可用于将 ASP.NET Core 框架功能连接到路由系统：</span><span class="sxs-lookup"><span data-stu-id="6d72c-153">Additional methods can be used to connect ASP.NET Core framework features to the routing system:</span></span>
- <span data-ttu-id="6d72c-154">[用于 Razor Pages 的 MapRazorPages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span><span class="sxs-lookup"><span data-stu-id="6d72c-154">[MapRazorPages for Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span></span>
- [<span data-ttu-id="6d72c-155">用于控制器的 MapControllers</span><span class="sxs-lookup"><span data-stu-id="6d72c-155">MapControllers for controllers</span></span>](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- <span data-ttu-id="6d72c-156">[用于 SignalR 的 MapHub\<THub>](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span><span class="sxs-lookup"><span data-stu-id="6d72c-156">[MapHub\<THub> for SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span></span> 
- [<span data-ttu-id="6d72c-157">用于 gRPC 的 MapGrpcService\<TService></span><span class="sxs-lookup"><span data-stu-id="6d72c-157">MapGrpcService\<TService> for gRPC</span></span>](xref:grpc/aspnetcore)

<span data-ttu-id="6d72c-158">下面的示例演示如何使用更复杂的路由模板进行路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-158">The following example shows routing with a more sophisticated route template:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

<span data-ttu-id="6d72c-159">`/hello/{name:alpha}` 字符串是一个路由模板。</span><span class="sxs-lookup"><span data-stu-id="6d72c-159">The string `/hello/{name:alpha}` is a **route template**.</span></span> <span data-ttu-id="6d72c-160">用于配置终结点的匹配方式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-160">It is used to configure how the endpoint is matched.</span></span> <span data-ttu-id="6d72c-161">在这种情况下，模板将匹配：</span><span class="sxs-lookup"><span data-stu-id="6d72c-161">In this case, the template matches:</span></span>

* <span data-ttu-id="6d72c-162">类似 `/hello/Ryan` 的 URL</span><span class="sxs-lookup"><span data-stu-id="6d72c-162">A URL like `/hello/Ryan`</span></span>
* <span data-ttu-id="6d72c-163">以 `/hello/` 开头、后跟一系列字母字符的任何 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-163">Any URL path that begins with `/hello/` followed by a sequence of alphabetic characters.</span></span>  <span data-ttu-id="6d72c-164">`:alpha` 应用仅匹配字母字符的路由约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-164">`:alpha` applies a route constraint that matches only alphabetic characters.</span></span> <span data-ttu-id="6d72c-165">[路由约束](#route-constraint-reference)将在本文档的后面详细介绍。</span><span class="sxs-lookup"><span data-stu-id="6d72c-165">[Route constraints](#route-constraint-reference) are explained later in this document.</span></span>

<span data-ttu-id="6d72c-166">URL 路径的第二段 `{name:alpha}`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-166">The second segment of the URL path, `{name:alpha}`:</span></span>

* <span data-ttu-id="6d72c-167">绑定到 `name` 参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-167">Is bound to the `name` parameter.</span></span>
* <span data-ttu-id="6d72c-168">捕获并存储在 [HttpRequest. RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*) 中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-168">Is captured and stored in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).</span></span>

<span data-ttu-id="6d72c-169">本文档中所述的终结点路由系统是新版本 ASP.NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="6d72c-169">The endpoint routing system described in this document is new as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="6d72c-170">但是，所有版本的 ASP.NET Core 都支持相同的路由模板功能和路由约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-170">However, all versions of ASP.NET Core support the same set of route template features and route constraints.</span></span>

<span data-ttu-id="6d72c-171">下面的示例演示如何通过[运行状况检查](xref:host-and-deploy/health-checks)和授权进行路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-171">The following example shows routing with [health checks](xref:host-and-deploy/health-checks) and authorization:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="6d72c-172">前面的示例展示了如何：</span><span class="sxs-lookup"><span data-stu-id="6d72c-172">The preceding example demonstrates how:</span></span>

* <span data-ttu-id="6d72c-173">将授权中间件与路由一起使用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-173">The authorization middleware can be used with routing.</span></span>
* <span data-ttu-id="6d72c-174">将终结点用于配置授权行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-174">Endpoints can be used to configure authorization behavior.</span></span>

<span data-ttu-id="6d72c-175"><xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> 调用添加运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-175">The <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> call adds a health check endpoint.</span></span> <span data-ttu-id="6d72c-176">将 <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> 链接到此调用会将授权策略附加到该终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-176">Chaining <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> on to this call attaches an authorization policy to the endpoint.</span></span>

<span data-ttu-id="6d72c-177">调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 和 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> 会添加身份验证和授权中间件。</span><span class="sxs-lookup"><span data-stu-id="6d72c-177">Calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> adds the authentication and authorization middleware.</span></span> <span data-ttu-id="6d72c-178">这些中间件位于 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> 和 `UseEndpoints` 之间，因此它们可以：</span><span class="sxs-lookup"><span data-stu-id="6d72c-178">These middleware are placed between <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and `UseEndpoints` so that they can:</span></span>

* <span data-ttu-id="6d72c-179">查看 `UseRouting` 选择的终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-179">See which endpoint was selected by `UseRouting`.</span></span>
* <span data-ttu-id="6d72c-180">将 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 发送到终结点之前应用授权策略。</span><span class="sxs-lookup"><span data-stu-id="6d72c-180">Apply an authorization policy before <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> dispatches to the endpoint.</span></span>

<a name="metadata"></a>

### <a name="endpoint-metadata"></a><span data-ttu-id="6d72c-181">终结点元数据</span><span class="sxs-lookup"><span data-stu-id="6d72c-181">Endpoint metadata</span></span>

<span data-ttu-id="6d72c-182">前面的示例中有两个终结点，但只有运行状况检查终结点附加了授权策略。</span><span class="sxs-lookup"><span data-stu-id="6d72c-182">In the preceding example, there are two endpoints, but only the health check endpoint has an authorization policy attached.</span></span> <span data-ttu-id="6d72c-183">如果请求与运行状况检查终结点 `/healthz` 匹配，则执行授权检查。</span><span class="sxs-lookup"><span data-stu-id="6d72c-183">If the request matches the health check endpoint, `/healthz`, an authorization check is performed.</span></span> <span data-ttu-id="6d72c-184">这表明，终结点可以附加额外的数据。</span><span class="sxs-lookup"><span data-stu-id="6d72c-184">This demonstrates that endpoints can have extra data attached to them.</span></span> <span data-ttu-id="6d72c-185">此额外数据称为终结点元数据：</span><span class="sxs-lookup"><span data-stu-id="6d72c-185">This extra data is called endpoint **metadata**:</span></span>

* <span data-ttu-id="6d72c-186">可以通过路由感知中间件来处理元数据。</span><span class="sxs-lookup"><span data-stu-id="6d72c-186">The metadata can be processed by routing-aware middleware.</span></span>
* <span data-ttu-id="6d72c-187">元数据可以是任意的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-187">The metadata can be of any .NET type.</span></span>

## <a name="routing-concepts"></a><span data-ttu-id="6d72c-188">路由概念</span><span class="sxs-lookup"><span data-stu-id="6d72c-188">Routing concepts</span></span>

<span data-ttu-id="6d72c-189">路由系统通过添加功能强大的终结点概念，构建在中间件管道之上。</span><span class="sxs-lookup"><span data-stu-id="6d72c-189">The routing system builds on top of the middleware pipeline by adding the powerful **endpoint** concept.</span></span> <span data-ttu-id="6d72c-190">终结点代表应用的功能单元，在路由、授权和任意数量的 ASP.NET Core 系统方面彼此不同。</span><span class="sxs-lookup"><span data-stu-id="6d72c-190">Endpoints represent units of the app's functionality that are distinct from each other in terms of routing, authorization, and any number of ASP.NET Core's systems.</span></span>

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a><span data-ttu-id="6d72c-191">ASP.NET Core 终结点定义</span><span class="sxs-lookup"><span data-stu-id="6d72c-191">ASP.NET Core endpoint definition</span></span>

<span data-ttu-id="6d72c-192">ASP.NET Core 终结点是：</span><span class="sxs-lookup"><span data-stu-id="6d72c-192">An ASP.NET Core endpoint is:</span></span>

* <span data-ttu-id="6d72c-193">可执行：具有 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-193">Executable: Has a <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.</span></span>
* <span data-ttu-id="6d72c-194">可扩展：具有[元数据](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*)集合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-194">Extensible: Has a [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) collection.</span></span>
* <span data-ttu-id="6d72c-195">Selectable:可选择性包含[路由信息](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-195">Selectable: Optionally, has [routing information](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).</span></span>
* <span data-ttu-id="6d72c-196">可枚举：可通过从 [DI](xref:fundamentals/dependency-injection) 中检索 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> 来列出终结点集合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-196">Enumerable: The collection of endpoints can be listed by retrieving the <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> from [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6d72c-197">以下代码显示了如何检索和检查与当前请求匹配的终结点：</span><span class="sxs-lookup"><span data-stu-id="6d72c-197">The following code shows how to retrieve and inspect the endpoint matching the current request:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

<span data-ttu-id="6d72c-198">如果选择了终结点，可从 `HttpContext` 中进行检索。</span><span class="sxs-lookup"><span data-stu-id="6d72c-198">The endpoint, if selected, can be retrieved from the `HttpContext`.</span></span> <span data-ttu-id="6d72c-199">可以检查其属性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-199">Its properties can be inspected.</span></span> <span data-ttu-id="6d72c-200">终结点对象是不可变的，并且在创建后无法修改。</span><span class="sxs-lookup"><span data-stu-id="6d72c-200">Endpoint objects are immutable and cannot be modified after creation.</span></span> <span data-ttu-id="6d72c-201">最常见的终结点类型是 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-201">The most common type of endpoint is a <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>.</span></span> <span data-ttu-id="6d72c-202">`RouteEndpoint` 包括允许自己被路由系统选择的信息。</span><span class="sxs-lookup"><span data-stu-id="6d72c-202">`RouteEndpoint` includes information that allows it to be to selected by the routing system.</span></span>

<span data-ttu-id="6d72c-203">在前面的代码中，[app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) 配置了一个内联[中间件](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-203">In the preceding code, [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configures an in-line [middleware](xref:fundamentals/middleware/index).</span></span>

<a name="mt"></a>

<span data-ttu-id="6d72c-204">下面的代码显示，根据管道中调用 `app.Use` 的位置，可能不存在终结点：</span><span class="sxs-lookup"><span data-stu-id="6d72c-204">The following code shows that, depending on where `app.Use` is called in the pipeline, there may not be an endpoint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

<span data-ttu-id="6d72c-205">前面的示例添加了 `Console.WriteLine` 语句，这些语句显示是否已选择终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-205">This preceding sample adds `Console.WriteLine` statements that display whether or not an endpoint has been selected.</span></span> <span data-ttu-id="6d72c-206">为清楚起见，该示例将显示名称分配给提供的 `/` 终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-206">For clarity, the sample assigns a display name to the provided `/` endpoint.</span></span>

<span data-ttu-id="6d72c-207">使用 `/` URL 运行此代码将显示：</span><span class="sxs-lookup"><span data-stu-id="6d72c-207">Running this code with a URL of `/` displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

<span data-ttu-id="6d72c-208">使用任何其他 URL 运行此代码将显示：</span><span class="sxs-lookup"><span data-stu-id="6d72c-208">Running this code with any other URL displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

<span data-ttu-id="6d72c-209">此输出说明：</span><span class="sxs-lookup"><span data-stu-id="6d72c-209">This output demonstrates that:</span></span>

* <span data-ttu-id="6d72c-210">调用 `UseRouting` 之前，终结点始终为 null。</span><span class="sxs-lookup"><span data-stu-id="6d72c-210">The endpoint is always null before `UseRouting` is called.</span></span>
* <span data-ttu-id="6d72c-211">如果找到匹配项，则 `UseRouting` 和 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 之间的终结点为非 null。</span><span class="sxs-lookup"><span data-stu-id="6d72c-211">If a match is found, the endpoint is non-null between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="6d72c-212">如果找到匹配项，则 `UseEndpoints` 中间件即为终端。</span><span class="sxs-lookup"><span data-stu-id="6d72c-212">The `UseEndpoints` middleware is **terminal** when a match is found.</span></span> <span data-ttu-id="6d72c-213">稍后会在本文档中定义[终端中间件](#tm)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-213">[Terminal middleware](#tm) is defined later in this document.</span></span>
* <span data-ttu-id="6d72c-214">仅当找不到匹配项时才执行 `UseEndpoints` 后的中间件。</span><span class="sxs-lookup"><span data-stu-id="6d72c-214">The middleware after `UseEndpoints` execute only when no match is found.</span></span>

<span data-ttu-id="6d72c-215">`UseRouting` 中间件使用 [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) 方法将终结点附加到当前上下文。</span><span class="sxs-lookup"><span data-stu-id="6d72c-215">The `UseRouting` middleware uses the [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) method to attach the endpoint to the current context.</span></span> <span data-ttu-id="6d72c-216">可以将 `UseRouting` 中间件替换为自定义逻辑，同时仍可获得使用终结点的益处。</span><span class="sxs-lookup"><span data-stu-id="6d72c-216">It's possible to replace the `UseRouting` middleware with custom logic and still get the benefits of using endpoints.</span></span> <span data-ttu-id="6d72c-217">终结点是中间件等低级别基元，不与路由实现耦合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-217">Endpoints are a low-level primitive like middleware, and aren't coupled to the routing implementation.</span></span> <span data-ttu-id="6d72c-218">大多数应用都不需要将 `UseRouting` 替换为自定义逻辑。</span><span class="sxs-lookup"><span data-stu-id="6d72c-218">Most apps don't need to replace `UseRouting` with custom logic.</span></span>

<span data-ttu-id="6d72c-219">`UseEndpoints` 中间件旨在与 `UseRouting` 中间件配合使用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-219">The `UseEndpoints` middleware is designed to be used in tandem with the `UseRouting` middleware.</span></span> <span data-ttu-id="6d72c-220">执行终结点的核心逻辑并不复杂。</span><span class="sxs-lookup"><span data-stu-id="6d72c-220">The core logic to execute an endpoint isn't complicated.</span></span> <span data-ttu-id="6d72c-221">使用 <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> 检索终结点，然后调用其 <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> 属性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-221">Use <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> to retrieve the endpoint, and then invoke its <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> property.</span></span>

<span data-ttu-id="6d72c-222">下面的代码演示中间件如何影响或响应路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-222">The following code demonstrates how middleware can influence or react to routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="6d72c-223">前面的示例演示两个重要概念：</span><span class="sxs-lookup"><span data-stu-id="6d72c-223">The preceding example demonstrates two important concepts:</span></span>

* <span data-ttu-id="6d72c-224">中间件可以在 `UseRouting` 之前运行，以修改路由操作的数据。</span><span class="sxs-lookup"><span data-stu-id="6d72c-224">Middleware can run before `UseRouting` to modify the data that routing operates upon.</span></span>
    * <span data-ttu-id="6d72c-225">通常，在路由之前出现的中间件会修改请求的某些属性，如 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>、<xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> 或 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-225">Usually middleware that appears before routing modifies some property of the request, such as <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>, or <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.</span></span>
* <span data-ttu-id="6d72c-226">中间件可以在 `UseRouting` 和 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 之间运行，以便在执行终结点前处理路由结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-226">Middleware can run between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> to process the results of routing before the endpoint is executed.</span></span>
    * <span data-ttu-id="6d72c-227">在 `UseRouting` 和 `UseEndpoints` 之间运行的中间件：</span><span class="sxs-lookup"><span data-stu-id="6d72c-227">Middleware that runs between `UseRouting` and `UseEndpoints`:</span></span>
      * <span data-ttu-id="6d72c-228">通常会检查元数据以了解终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-228">Usually inspects metadata to understand the endpoints.</span></span>
      * <span data-ttu-id="6d72c-229">通常会根据 `UseAuthorization` 和 `UseCors` 做出安全决策。</span><span class="sxs-lookup"><span data-stu-id="6d72c-229">Often makes security decisions, as done by `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="6d72c-230">中间件和元数据的组合允许按终结点配置策略。</span><span class="sxs-lookup"><span data-stu-id="6d72c-230">The combination of middleware and metadata allows configuring policies per-endpoint.</span></span>

<span data-ttu-id="6d72c-231">前面的代码显示了支持按终结点策略的自定义中间件示例。</span><span class="sxs-lookup"><span data-stu-id="6d72c-231">The preceding code shows an example of a custom middleware that supports per-endpoint policies.</span></span> <span data-ttu-id="6d72c-232">中间件将访问敏感数据的审核日志写入控制台。</span><span class="sxs-lookup"><span data-stu-id="6d72c-232">The middleware writes an *audit log* of access to sensitive data to the console.</span></span> <span data-ttu-id="6d72c-233">可以将中间件配置为审核具有 `AuditPolicyAttribute` 元数据的终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-233">The middleware can be configured to *audit* an endpoint with the `AuditPolicyAttribute` metadata.</span></span> <span data-ttu-id="6d72c-234">此示例演示选择加入模式，其中仅审核标记为敏感的终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-234">This sample demonstrates an *opt-in* pattern where only endpoints that are marked as sensitive are audited.</span></span> <span data-ttu-id="6d72c-235">例如，可以反向定义此逻辑，从而审核未标记为安全的所有内容。</span><span class="sxs-lookup"><span data-stu-id="6d72c-235">It's possible to define this logic in reverse, auditing everything that isn't marked as safe, for example.</span></span> <span data-ttu-id="6d72c-236">终结点元数据系统非常灵活。</span><span class="sxs-lookup"><span data-stu-id="6d72c-236">The endpoint metadata system is flexible.</span></span> <span data-ttu-id="6d72c-237">此逻辑可以以任何适合用例的方法进行设计。</span><span class="sxs-lookup"><span data-stu-id="6d72c-237">This logic could be designed in whatever way suits the use case.</span></span>

<span data-ttu-id="6d72c-238">前面的示例代码旨在演示终结点的基本概念。</span><span class="sxs-lookup"><span data-stu-id="6d72c-238">The preceding sample code is intended to demonstrate the basic concepts of endpoints.</span></span> <span data-ttu-id="6d72c-239">该示例不应在生产环境中使用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-239">**The sample is not intended for production use**.</span></span> <span data-ttu-id="6d72c-240">审核日志中间件的更完整版本如下：</span><span class="sxs-lookup"><span data-stu-id="6d72c-240">A more complete version of an *audit log* middleware would:</span></span>

* <span data-ttu-id="6d72c-241">记录到文件或数据库。</span><span class="sxs-lookup"><span data-stu-id="6d72c-241">Log to a file or database.</span></span>
* <span data-ttu-id="6d72c-242">包括详细信息，如用户、IP 地址、敏感终结点的名称等。</span><span class="sxs-lookup"><span data-stu-id="6d72c-242">Include details such as the user, IP address, name of the sensitive endpoint, and more.</span></span>

<span data-ttu-id="6d72c-243">审核策略元数据 `AuditPolicyAttribute` 定义为一个 `Attribute`，便于和基于类的框架（如控制器和 SignalR）结合使用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-243">The audit policy metadata `AuditPolicyAttribute` is defined as an `Attribute` for easier use with class-based frameworks such as controllers and SignalR.</span></span> <span data-ttu-id="6d72c-244">使用路由到代码时：</span><span class="sxs-lookup"><span data-stu-id="6d72c-244">When using *route to code*:</span></span>

* <span data-ttu-id="6d72c-245">元数据附有生成器 API。</span><span class="sxs-lookup"><span data-stu-id="6d72c-245">Metadata is attached with a builder API.</span></span>
* <span data-ttu-id="6d72c-246">基于类的框架在创建终结点时，包含了相应方法和类的所有特性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-246">Class-based frameworks include all attributes on the corresponding method and class when creating endpoints.</span></span>

<span data-ttu-id="6d72c-247">对于元数据类型，最佳做法是将它们定义为接口或特性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-247">The best practices for metadata types are to define them either as interfaces or attributes.</span></span> <span data-ttu-id="6d72c-248">接口和特性允许代码重用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-248">Interfaces and attributes allow code reuse.</span></span> <span data-ttu-id="6d72c-249">元数据系统非常灵活，无任何限制。</span><span class="sxs-lookup"><span data-stu-id="6d72c-249">The metadata system is flexible and doesn't impose any limitations.</span></span>

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a><span data-ttu-id="6d72c-250">比较终端中间件和路由</span><span class="sxs-lookup"><span data-stu-id="6d72c-250">Comparing a terminal middleware and routing</span></span>

<span data-ttu-id="6d72c-251">下面的代码示例对比使用中间件和使用路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-251">The following code sample contrasts using middleware with using routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="6d72c-252">使用 `Approach 1:` 显示的中间件样式是终端中间件。</span><span class="sxs-lookup"><span data-stu-id="6d72c-252">The style of middleware shown with `Approach 1:` is **terminal middleware**.</span></span> <span data-ttu-id="6d72c-253">之所以称之为终端中间件，是因为它执行匹配的操作：</span><span class="sxs-lookup"><span data-stu-id="6d72c-253">It's called terminal middleware because it does a matching operation:</span></span>

* <span data-ttu-id="6d72c-254">前面示例中的匹配操作是用于中间件的 `Path == "/"` 和用于路由的 `Path == "/Movie"`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-254">The matching operation in the preceding sample is `Path == "/"` for the middleware and `Path == "/Movie"` for routing.</span></span>
* <span data-ttu-id="6d72c-255">如果匹配成功，它将执行一些功能并返回，而不是调用 `next` 中间件。</span><span class="sxs-lookup"><span data-stu-id="6d72c-255">When a match is successful, it executes some functionality and returns, rather than invoking the `next` middleware.</span></span>

<span data-ttu-id="6d72c-256">之所以称之为终端中间件，是因为它会终止搜索，执行一些功能，然后返回。</span><span class="sxs-lookup"><span data-stu-id="6d72c-256">It's called terminal middleware because it terminates the search, executes some functionality, and then returns.</span></span>

<span data-ttu-id="6d72c-257">比较终端中间件和路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-257">Comparing a terminal middleware and routing:</span></span>
* <span data-ttu-id="6d72c-258">这两种方法都允许终止处理管道：</span><span class="sxs-lookup"><span data-stu-id="6d72c-258">Both approaches allow terminating the processing pipeline:</span></span>
    * <span data-ttu-id="6d72c-259">中间件通过返回而不是调用 `next` 来终止管道。</span><span class="sxs-lookup"><span data-stu-id="6d72c-259">Middleware terminates the pipeline by returning rather than invoking `next`.</span></span>
    * <span data-ttu-id="6d72c-260">终结点始终是终端。</span><span class="sxs-lookup"><span data-stu-id="6d72c-260">Endpoints are always terminal.</span></span>
* <span data-ttu-id="6d72c-261">终端中间件允许在管道中的任意位置放置中间件：</span><span class="sxs-lookup"><span data-stu-id="6d72c-261">Terminal middleware allows positioning the middleware at an arbitrary place in the pipeline:</span></span>
    * <span data-ttu-id="6d72c-262">终结点在 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 位置执行。</span><span class="sxs-lookup"><span data-stu-id="6d72c-262">Endpoints execute at the position of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="6d72c-263">终端中间件允许任意代码确定中间件匹配的时间：</span><span class="sxs-lookup"><span data-stu-id="6d72c-263">Terminal middleware allows arbitrary code to determine when the middleware matches:</span></span>
    * <span data-ttu-id="6d72c-264">自定义路由匹配代码可能比较复杂，且难以正确编写。</span><span class="sxs-lookup"><span data-stu-id="6d72c-264">Custom route matching code can be verbose and difficult to write correctly.</span></span>
    * <span data-ttu-id="6d72c-265">路由为典型应用提供了简单的解决方案。</span><span class="sxs-lookup"><span data-stu-id="6d72c-265">Routing provides straightforward solutions for typical apps.</span></span> <span data-ttu-id="6d72c-266">大多数应用不需要自定义路由匹配代码。</span><span class="sxs-lookup"><span data-stu-id="6d72c-266">Most apps don't require custom route matching code.</span></span>
* <span data-ttu-id="6d72c-267">带有中间件的终结点接口，如 `UseAuthorization` 和 `UseCors`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-267">Endpoints interface with middleware such as `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="6d72c-268">通过 `UseAuthorization` 或 `UseCors` 使用终端中间件需要与授权系统进行手动交互。</span><span class="sxs-lookup"><span data-stu-id="6d72c-268">Using a terminal middleware with `UseAuthorization` or `UseCors` requires manual interfacing with the authorization system.</span></span>

<span data-ttu-id="6d72c-269">[终结点](#endpoint)定义以下两者：</span><span class="sxs-lookup"><span data-stu-id="6d72c-269">An [endpoint](#endpoint) defines both:</span></span>

* <span data-ttu-id="6d72c-270">用于处理请求的委托。</span><span class="sxs-lookup"><span data-stu-id="6d72c-270">A delegate to process requests.</span></span>
* <span data-ttu-id="6d72c-271">任意元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-271">A collection of arbitrary metadata.</span></span> <span data-ttu-id="6d72c-272">元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-272">The metadata is used to implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="6d72c-273">终端中间件可以是一种有效的工具，但可能需要：</span><span class="sxs-lookup"><span data-stu-id="6d72c-273">Terminal middleware can be an effective tool, but can require:</span></span>

* <span data-ttu-id="6d72c-274">大量的编码和测试。</span><span class="sxs-lookup"><span data-stu-id="6d72c-274">A significant amount of coding and testing.</span></span>
* <span data-ttu-id="6d72c-275">手动与其他系统集成，以实现所需的灵活性级别。</span><span class="sxs-lookup"><span data-stu-id="6d72c-275">Manual integration with other systems to achieve the desired level of flexibility.</span></span>

<span data-ttu-id="6d72c-276">请考虑在写入终端中间件之前与路由集成。</span><span class="sxs-lookup"><span data-stu-id="6d72c-276">Consider integrating with routing before writing a terminal middleware.</span></span>

<span data-ttu-id="6d72c-277">与 [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) 或 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 相集成的现有终端中间件通常会转换为路由感知终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-277">Existing terminal middleware that integrates with [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) or <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> can usually be turned into a routing aware endpoint.</span></span> <span data-ttu-id="6d72c-278">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) 演示了路由器软件的模式：</span><span class="sxs-lookup"><span data-stu-id="6d72c-278">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) demonstrates the pattern for router-ware:</span></span>
* <span data-ttu-id="6d72c-279">在 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> 上编写扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-279">Write an extension method on <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span>
* <span data-ttu-id="6d72c-280">使用 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*> 创建嵌套中间件管道。</span><span class="sxs-lookup"><span data-stu-id="6d72c-280">Create a nested middleware pipeline using <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.</span></span>
* <span data-ttu-id="6d72c-281">将中间件附加到新管道。</span><span class="sxs-lookup"><span data-stu-id="6d72c-281">Attach the middleware to the new pipeline.</span></span> <span data-ttu-id="6d72c-282">在本例中为 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-282">In this case, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.</span></span>
* <span data-ttu-id="6d72c-283"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> 将中间件管道附加到 <xref:Microsoft.AspNetCore.Http.RequestDelegate>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-283"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> the middleware pipeline into a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="6d72c-284">调用 `Map` 并提供新的中间件管道。</span><span class="sxs-lookup"><span data-stu-id="6d72c-284">Call `Map` and provide the new middleware pipeline.</span></span>
* <span data-ttu-id="6d72c-285">从扩展方法返回由 `Map` 提供的生成器对象。</span><span class="sxs-lookup"><span data-stu-id="6d72c-285">Return the builder object provided by `Map` from the extension method.</span></span>

<span data-ttu-id="6d72c-286">下面的代码演示了 [MapHealthChecks](xref:host-and-deploy/health-checks) 的使用方法：</span><span class="sxs-lookup"><span data-stu-id="6d72c-286">The following code shows use of [MapHealthChecks](xref:host-and-deploy/health-checks):</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

<span data-ttu-id="6d72c-287">前面的示例说明了为什么返回生成器对象很重要。</span><span class="sxs-lookup"><span data-stu-id="6d72c-287">The preceding sample shows why returning the builder object is important.</span></span> <span data-ttu-id="6d72c-288">返回生成器对象后，应用开发者可以配置策略，如终结点的授权。</span><span class="sxs-lookup"><span data-stu-id="6d72c-288">Returning the builder object allows the app developer to configure policies such as authorization for the endpoint.</span></span> <span data-ttu-id="6d72c-289">在此示例中，运行状况检查中间件不与授权系统直接集成。</span><span class="sxs-lookup"><span data-stu-id="6d72c-289">In this example, the health checks middleware has no direct integration with the authorization system.</span></span>

<span data-ttu-id="6d72c-290">元数据系统的创建目的是为了响应扩展性创建者使用终端中间件时遇到的问题。</span><span class="sxs-lookup"><span data-stu-id="6d72c-290">The metadata system was created in response to the problems encountered by extensibility authors using terminal middleware.</span></span> <span data-ttu-id="6d72c-291">对于每个中间件，实现自己与授权系统的集成都会出现问题。</span><span class="sxs-lookup"><span data-stu-id="6d72c-291">It's problematic for each middleware to implement its own integration with the authorization system.</span></span>

<a name="urlm"></a>

### <a name="url-matching"></a><span data-ttu-id="6d72c-292">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="6d72c-292">URL matching</span></span>

* <span data-ttu-id="6d72c-293">是路由将传入请求匹配到[终结点](#endpoint)的过程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-293">Is the process by which routing matches an incoming request to an [endpoint](#endpoint).</span></span>
* <span data-ttu-id="6d72c-294">基于 URL 路径中的数据和标头。</span><span class="sxs-lookup"><span data-stu-id="6d72c-294">Is based on data in the URL path and headers.</span></span>
* <span data-ttu-id="6d72c-295">可进行扩展，以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="6d72c-295">Can be extended to consider any data in the request.</span></span>

<span data-ttu-id="6d72c-296">当路由中间件执行时，它会设置 `Endpoint`，并将值从当前请求路由到 <xref:Microsoft.AspNetCore.Http.HttpContext> 上的[请求功能](xref:fundamentals/request-features)：</span><span class="sxs-lookup"><span data-stu-id="6d72c-296">When a routing middleware executes, it sets an `Endpoint` and route values to a [request feature](xref:fundamentals/request-features) on the <xref:Microsoft.AspNetCore.Http.HttpContext> from the current request:</span></span>

* <span data-ttu-id="6d72c-297">调用 [GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) 获取终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-297">Calling [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) gets the endpoint.</span></span>
* <span data-ttu-id="6d72c-298">`HttpRequest.RouteValues` 将获取路由值的集合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-298">`HttpRequest.RouteValues` gets the collection of route values.</span></span>

<span data-ttu-id="6d72c-299">在路由中间件之后运行的[中间件](xref:fundamentals/middleware/index)可以检查终结点并采取措施。</span><span class="sxs-lookup"><span data-stu-id="6d72c-299">[Middleware](xref:fundamentals/middleware/index) running after the routing middleware can inspect the endpoint and take action.</span></span> <span data-ttu-id="6d72c-300">例如，授权中间件可以在终结点的元数据集合中询问授权策略。</span><span class="sxs-lookup"><span data-stu-id="6d72c-300">For example, an authorization middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span> <span data-ttu-id="6d72c-301">请求处理管道中的所有中间件执行后，将调用所选终结点的委托。</span><span class="sxs-lookup"><span data-stu-id="6d72c-301">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>

<span data-ttu-id="6d72c-302">终结点路由中的路由系统负责所有的调度决策。</span><span class="sxs-lookup"><span data-stu-id="6d72c-302">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="6d72c-303">中间件基于所选终结点应用策略，因此重要的是：</span><span class="sxs-lookup"><span data-stu-id="6d72c-303">Because the middleware applies policies based on the selected endpoint, it's important that:</span></span>

* <span data-ttu-id="6d72c-304">任何可能影响发送或安全策略应用的决定都应在路由系统中做出。</span><span class="sxs-lookup"><span data-stu-id="6d72c-304">Any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

> [!WARNING]
> <span data-ttu-id="6d72c-305">对于后向兼容性，执行 Controller 或 Razor Pages 终结点委托时，应根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-305">For backwards-compatibility, when a Controller or Razor Pages endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>
>
> <span data-ttu-id="6d72c-306">在未来的版本中，会将 `RouteContext` 类型标记为已过时：</span><span class="sxs-lookup"><span data-stu-id="6d72c-306">The `RouteContext` type will be marked obsolete in a future release:</span></span>
>
> * <span data-ttu-id="6d72c-307">将 `RouteData.Values` 迁移到 `HttpRequest.RouteValues`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-307">Migrate `RouteData.Values` to `HttpRequest.RouteValues`.</span></span>
> * <span data-ttu-id="6d72c-308">迁移 `RouteData.DataTokens` 以从终结点元数据检索 [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-308">Migrate `RouteData.DataTokens` to retrieve [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) from the endpoint metadata.</span></span>

<span data-ttu-id="6d72c-309">URL 匹配在可配置的阶段集中运行。</span><span class="sxs-lookup"><span data-stu-id="6d72c-309">URL matching operates in a configurable set of phases.</span></span> <span data-ttu-id="6d72c-310">在每个阶段中，输出为一组匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-310">In each phase, the output is a set of matches.</span></span> <span data-ttu-id="6d72c-311">下一阶段可以进一步缩小这一组匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-311">The set of matches can be narrowed down further by the next phase.</span></span> <span data-ttu-id="6d72c-312">路由实现不保证匹配终结点的处理顺序。</span><span class="sxs-lookup"><span data-stu-id="6d72c-312">The routing implementation does not guarantee a processing order for matching endpoints.</span></span> <span data-ttu-id="6d72c-313">所有可能的匹配项一次性处理。</span><span class="sxs-lookup"><span data-stu-id="6d72c-313">**All** possible matches are processed at once.</span></span> <span data-ttu-id="6d72c-314">URL 匹配阶段按以下顺序出现。</span><span class="sxs-lookup"><span data-stu-id="6d72c-314">The URL matching phases occur in the following order.</span></span> <span data-ttu-id="6d72c-315">ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="6d72c-315">ASP.NET Core:</span></span>

1. <span data-ttu-id="6d72c-316">针对终结点集及其路由模板处理 URL 路径，收集所有匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-316">Processes the URL path against the set of endpoints and their route templates, collecting **all** of the matches.</span></span>
1. <span data-ttu-id="6d72c-317">采用前面的列表并删除在应用路由约束时失败的匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-317">Takes the preceding list and removes matches that fail with route constraints applied.</span></span>
1. <span data-ttu-id="6d72c-318">采用前面的列表并删除 [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) 实例集失败的匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-318">Takes the preceding list and removes matches that fail the set of [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) instances.</span></span>
1. <span data-ttu-id="6d72c-319">使用 [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) 从前面的列表中做出最终决定。</span><span class="sxs-lookup"><span data-stu-id="6d72c-319">Uses the [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) to make a final decision from the preceding list.</span></span>

<span data-ttu-id="6d72c-320">根据以下内容设置终结点列表的优先级：</span><span class="sxs-lookup"><span data-stu-id="6d72c-320">The list of endpoints is prioritized according to:</span></span>

* <span data-ttu-id="6d72c-321">[RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span><span class="sxs-lookup"><span data-stu-id="6d72c-321">The [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span></span>
* <span data-ttu-id="6d72c-322">[路由模板优先顺序](#rtp)</span><span class="sxs-lookup"><span data-stu-id="6d72c-322">The [route template precedence](#rtp)</span></span>

<span data-ttu-id="6d72c-323">在每个阶段中处理所有匹配的终结点，直到达到 <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-323">All matching endpoints are processed in each phase until the <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> is reached.</span></span> <span data-ttu-id="6d72c-324">`EndpointSelector` 是最后一个阶段。</span><span class="sxs-lookup"><span data-stu-id="6d72c-324">The `EndpointSelector` is the final phase.</span></span> <span data-ttu-id="6d72c-325">它从匹配项中选择最高优先级终结点作为最佳匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-325">It chooses the highest priority endpoint from the matches as the best match.</span></span> <span data-ttu-id="6d72c-326">如果存在具有与最佳匹配相同优先级的其他匹配项，则会引发不明确的匹配异常。</span><span class="sxs-lookup"><span data-stu-id="6d72c-326">If there are other matches with the same priority as the best match, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="6d72c-327">路由优先顺序基于更具体的路由模板（优先级更高）进行计算。</span><span class="sxs-lookup"><span data-stu-id="6d72c-327">The route precedence is computed based on a **more specific** route template being given a higher priority.</span></span> <span data-ttu-id="6d72c-328">例如，考虑模板 `/hello` 和 `/{message}`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-328">For example, consider the templates `/hello` and `/{message}`:</span></span>

* <span data-ttu-id="6d72c-329">两者都匹配 URL 路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-329">Both match the URL path `/hello`.</span></span>
* <span data-ttu-id="6d72c-330">`/hello` 更具体，因此优先级更高。</span><span class="sxs-lookup"><span data-stu-id="6d72c-330">`/hello`  is more specific and therefore higher priority.</span></span>

<span data-ttu-id="6d72c-331">通常，路由优先顺序非常适合为实践操作中使用的各种 URL 方案选择最佳匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-331">In general, route precedence does a good job of choosing the best match for the kinds of URL schemes used in practice.</span></span> <span data-ttu-id="6d72c-332">仅在必要时才使用 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> 来避免多义性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-332">Use <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> only when necessary to avoid an ambiguity.</span></span>

<span data-ttu-id="6d72c-333">由于路由提供的扩展性种类，路由系统无法提前计算不明确的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-333">Due to the kinds of extensibility provided by routing, it isn't possible for the routing system to compute ahead of time the ambiguous routes.</span></span> <span data-ttu-id="6d72c-334">假设有一个示例，例如路由模板 `/{message:alpha}` 和 `/{message:int}`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-334">Consider an example such as the route templates `/{message:alpha}` and `/{message:int}`:</span></span>

* <span data-ttu-id="6d72c-335">`alpha` 约束仅匹配字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="6d72c-335">The `alpha` constraint matches only alphabetic characters.</span></span>
* <span data-ttu-id="6d72c-336">`int` 约束仅匹配数字。</span><span class="sxs-lookup"><span data-stu-id="6d72c-336">The `int` constraint matches only numbers.</span></span>
* <span data-ttu-id="6d72c-337">这些模板具有相同的路由优先顺序，但没有两者均匹配的单一 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-337">These templates have the same route precedence, but there's no single URL they both match.</span></span>
* <span data-ttu-id="6d72c-338">如果路由系统在启动时报告了多义性错误，则会阻止此有效用例。</span><span class="sxs-lookup"><span data-stu-id="6d72c-338">If the routing system reported an ambiguity error at startup, it would block this valid use case.</span></span>

> [!WARNING]
>
> <span data-ttu-id="6d72c-339"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 内的操作顺序并不会影响路由行为，但有一个例外。</span><span class="sxs-lookup"><span data-stu-id="6d72c-339">The order of operations inside <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> doesn't influence the behavior of routing, with one exception.</span></span> <span data-ttu-id="6d72c-340"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 和 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 会根据调用顺序，自动将顺序值分配给其终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-340"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="6d72c-341">这会模拟控制器的长时间行为，而无需路由系统提供与早期路由实现相同的保证。</span><span class="sxs-lookup"><span data-stu-id="6d72c-341">This simulates long-time behavior of controllers without the routing system providing the same guarantees as older routing implementations.</span></span>
>
> <span data-ttu-id="6d72c-342">在路由的早期实现中，可以实现具有依赖于路由处理顺序的路由扩展性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-342">In the legacy implementation of routing, it's possible to implement routing extensibility that has a dependency on the order in which routes are processed.</span></span> <span data-ttu-id="6d72c-343">ASP.NET Core 3.0 及更高版本中的终结点路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-343">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>
> 
> * <span data-ttu-id="6d72c-344">没有路由的概念。</span><span class="sxs-lookup"><span data-stu-id="6d72c-344">Doesn't have a concept of routes.</span></span>
> * <span data-ttu-id="6d72c-345">不提供顺序保证。</span><span class="sxs-lookup"><span data-stu-id="6d72c-345">Doesn't provide ordering guarantees.</span></span> <span data-ttu-id="6d72c-346">同时处理所有终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-346">All endpoints are processed at once.</span></span>
>
> <span data-ttu-id="6d72c-347">如果这表示你无法使用旧版路由系统，请[提出 GitHub 问题以获取帮助](https://github.com/dotnet/aspnetcore/issues)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-347">If this means you're stuck using the legacy routing system, [open a GitHub issue for assistance](https://github.com/dotnet/aspnetcore/issues).</span></span>

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a><span data-ttu-id="6d72c-348">路由模板优先顺序和终结点选择顺序</span><span class="sxs-lookup"><span data-stu-id="6d72c-348">Route template precedence and endpoint selection order</span></span>

<span data-ttu-id="6d72c-349">[路由模板优先顺序](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16)是一种系统，该系统根据每个路由模板的具体程度为其分配值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-349">[Route template precedence](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) is a system that assigns each route template a value based on how specific it is.</span></span> <span data-ttu-id="6d72c-350">路由模板优先顺序：</span><span class="sxs-lookup"><span data-stu-id="6d72c-350">Route template precedence:</span></span>

* <span data-ttu-id="6d72c-351">无需在常见情况下调整终结点的顺序。</span><span class="sxs-lookup"><span data-stu-id="6d72c-351">Avoids the need to adjust the order of endpoints in common cases.</span></span>
* <span data-ttu-id="6d72c-352">尝试匹配路由行为的常识性预期。</span><span class="sxs-lookup"><span data-stu-id="6d72c-352">Attempts to match the common-sense expectations of routing behavior.</span></span>

<span data-ttu-id="6d72c-353">例如，考虑模板 `/Products/List` 和 `/Products/{id}`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-353">For example, consider templates `/Products/List` and `/Products/{id}`.</span></span> <span data-ttu-id="6d72c-354">我们可合理地假设，对于 URL 路径 `/Products/List`，`/Products/List` 匹配项比 `/Products/{id}` 更好。</span><span class="sxs-lookup"><span data-stu-id="6d72c-354">It would be reasonable to assume that `/Products/List` is a better match than `/Products/{id}` for the URL path `/Products/List`.</span></span> <span data-ttu-id="6d72c-355">这种假设合理是因为文本段 `/List` 比参数段 `/{id}` 具有更好的优先顺序。</span><span class="sxs-lookup"><span data-stu-id="6d72c-355">The works because the literal segment `/List` is considered to have better precedence than the parameter segment `/{id}`.</span></span>

<span data-ttu-id="6d72c-356">优先顺序工作原理的详细信息与路由模板的定义方式相耦合：</span><span class="sxs-lookup"><span data-stu-id="6d72c-356">The details of how precedence works are coupled to how route templates are defined:</span></span>

* <span data-ttu-id="6d72c-357">具有更多段的模板则更具体。</span><span class="sxs-lookup"><span data-stu-id="6d72c-357">Templates with more segments are considered more specific.</span></span>
* <span data-ttu-id="6d72c-358">带有文本的段比参数段更具体。</span><span class="sxs-lookup"><span data-stu-id="6d72c-358">A segment with literal text is considered more specific than a parameter segment.</span></span>
* <span data-ttu-id="6d72c-359">具有约束的参数段比没有约束的参数段更具体。</span><span class="sxs-lookup"><span data-stu-id="6d72c-359">A parameter segment with a constraint is considered more specific than one without.</span></span>
* <span data-ttu-id="6d72c-360">复杂段与具有约束的参数段同样具体。</span><span class="sxs-lookup"><span data-stu-id="6d72c-360">A complex segment is considered as specific as a parameter segment with a constraint.</span></span>
* <span data-ttu-id="6d72c-361">catch-all 参数是最不具体的参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-361">Catch-all parameters are the least specific.</span></span> <span data-ttu-id="6d72c-362">有关 catch-all 路由的重要信息，请参阅 [路由模板参考](#rtr) 中的“catch-all”。</span><span class="sxs-lookup"><span data-stu-id="6d72c-362">See **catch-all** in the [Route template reference](#rtr) for important information on catch-all routes.</span></span>

<span data-ttu-id="6d72c-363">有关确切值的参考，请参阅 [GitHub 上的源代码](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-363">See the [source code on GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) for a reference of exact values.</span></span>

<a name="lg"></a>

### <a name="url-generation-concepts"></a><span data-ttu-id="6d72c-364">URL 生成概念</span><span class="sxs-lookup"><span data-stu-id="6d72c-364">URL generation concepts</span></span>

<span data-ttu-id="6d72c-365">URL 生成：</span><span class="sxs-lookup"><span data-stu-id="6d72c-365">URL generation:</span></span>

* <span data-ttu-id="6d72c-366">是指路由基于一系列路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-366">Is the process by which routing can create a URL path based on a set of route values.</span></span>
* <span data-ttu-id="6d72c-367">允许终结点与访问它们的 URL 之间存在逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="6d72c-367">Allows for a logical separation between endpoints and the URLs that access them.</span></span>

<span data-ttu-id="6d72c-368">终结点路由包含 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API。</span><span class="sxs-lookup"><span data-stu-id="6d72c-368">Endpoint routing includes the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API.</span></span> <span data-ttu-id="6d72c-369">`LinkGenerator` 是 [DI](xref:fundamentals/dependency-injection) 中可用的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="6d72c-369">`LinkGenerator` is a singleton service available from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6d72c-370">`LinkGenerator` API 可在执行请求的上下文之外使用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-370">The `LinkGenerator` API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="6d72c-371">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) 和依赖 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的方案（如[标记帮助程序](xref:mvc/views/tag-helpers/intro)、HTML 帮助程序和[操作结果](xref:mvc/controllers/actions)）在内部使用 `LinkGenerator` API 提供链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="6d72c-371">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the `LinkGenerator` API internally to provide link generating capabilities.</span></span>

<span data-ttu-id="6d72c-372">链接生成器基于“地址”和“地址方案”概念 。</span><span class="sxs-lookup"><span data-stu-id="6d72c-372">The link generator is backed by the concept of an **address** and **address schemes**.</span></span> <span data-ttu-id="6d72c-373">地址方案是确定哪些终结点用于链接生成的方式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-373">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="6d72c-374">例如，许多用户熟悉的来自控制器或 Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-374">For example, the route name and route values scenarios many users are familiar with from controllers and Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="6d72c-375">链接生成器可以通过以下扩展方法链接到控制器或 Razor Pages：</span><span class="sxs-lookup"><span data-stu-id="6d72c-375">The link generator can link to controllers and Razor Pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="6d72c-376">这些方法的重载接受包含 `HttpContext` 的参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-376">Overloads of these methods accept arguments that include the `HttpContext`.</span></span> <span data-ttu-id="6d72c-377">这些方法在功能上等同于 [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) 和 [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)，但提供了更大的灵活性和更多选项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-377">These methods are functionally equivalent to [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) and [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), but offer additional flexibility and options.</span></span>

<span data-ttu-id="6d72c-378">`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-378">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page`, in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="6d72c-379">`GetUri*` 方法始终生成包含方案和主机的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-379">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="6d72c-380">接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-380">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="6d72c-381">除非重写，否则将使用来自执行请求的[环境](#ambient)路由值、URL 基路径、方案和主机。</span><span class="sxs-lookup"><span data-stu-id="6d72c-381">The [ambient](#ambient) route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="6d72c-382">使用地址调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-382"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="6d72c-383">生成 URI 的过程分两步进行：</span><span class="sxs-lookup"><span data-stu-id="6d72c-383">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="6d72c-384">将地址绑定到与地址匹配的终结点列表。</span><span class="sxs-lookup"><span data-stu-id="6d72c-384">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="6d72c-385">计算每个终结点的 <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern>，直到找到与提供的值匹配的路由模式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-385">Each endpoint's <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="6d72c-386">输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。</span><span class="sxs-lookup"><span data-stu-id="6d72c-386">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="6d72c-387">对任何类型的地址，<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法均支持标准链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="6d72c-387">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="6d72c-388">使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作：</span><span class="sxs-lookup"><span data-stu-id="6d72c-388">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type:</span></span>

| <span data-ttu-id="6d72c-389">扩展方法</span><span class="sxs-lookup"><span data-stu-id="6d72c-389">Extension Method</span></span> | <span data-ttu-id="6d72c-390">描述</span><span class="sxs-lookup"><span data-stu-id="6d72c-390">Description</span></span> |
| ---
<span data-ttu-id="6d72c-391">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-391">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-392">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-392">'Blazor'</span></span>
- <span data-ttu-id="6d72c-393">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-393">'Identity'</span></span>
- <span data-ttu-id="6d72c-394">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-394">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-395">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-395">'Razor'</span></span>
- <span data-ttu-id="6d72c-396">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-396">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-397">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-397">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-398">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-398">'Blazor'</span></span>
- <span data-ttu-id="6d72c-399">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-399">'Identity'</span></span>
- <span data-ttu-id="6d72c-400">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-400">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-401">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-401">'Razor'</span></span>
- <span data-ttu-id="6d72c-402">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-402">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-403">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-403">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-404">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-404">'Blazor'</span></span>
- <span data-ttu-id="6d72c-405">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-405">'Identity'</span></span>
- <span data-ttu-id="6d72c-406">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-406">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-407">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-407">'Razor'</span></span>
- <span data-ttu-id="6d72c-408">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-408">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-409">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-409">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-410">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-410">'Blazor'</span></span>
- <span data-ttu-id="6d72c-411">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-411">'Identity'</span></span>
- <span data-ttu-id="6d72c-412">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-412">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-413">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-413">'Razor'</span></span>
- <span data-ttu-id="6d72c-414">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-414">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-416">'Blazor'</span></span>
- <span data-ttu-id="6d72c-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-417">'Identity'</span></span>
- <span data-ttu-id="6d72c-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-419">'Razor'</span></span>
- <span data-ttu-id="6d72c-420">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-422">'Blazor'</span></span>
- <span data-ttu-id="6d72c-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-423">'Identity'</span></span>
- <span data-ttu-id="6d72c-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-425">'Razor'</span></span>
- <span data-ttu-id="6d72c-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-426">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-427">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-427">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-428">'Blazor'</span></span>
- <span data-ttu-id="6d72c-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-429">'Identity'</span></span>
- <span data-ttu-id="6d72c-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-431">'Razor'</span></span>
- <span data-ttu-id="6d72c-432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-434">'Blazor'</span></span>
- <span data-ttu-id="6d72c-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-435">'Identity'</span></span>
- <span data-ttu-id="6d72c-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-437">'Razor'</span></span>
- <span data-ttu-id="6d72c-438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-440">'Blazor'</span></span>
- <span data-ttu-id="6d72c-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-441">'Identity'</span></span>
- <span data-ttu-id="6d72c-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-443">'Razor'</span></span>
- <span data-ttu-id="6d72c-444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-444">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-445">------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 根据提供的值生成具有绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-445">------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generates a URI with an absolute path based on the provided values.</span></span> <span data-ttu-id="6d72c-446">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 根据提供的值生成绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-446">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="6d72c-447">请注意有关调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列含义：</span><span class="sxs-lookup"><span data-stu-id="6d72c-447">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="6d72c-448">对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-448">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="6d72c-449">如果未验证传入请求的 `Host` 标头，则可能以视图或页面中 URI 的形式将不受信任的请求输入发送回客户端。</span><span class="sxs-lookup"><span data-stu-id="6d72c-449">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view or page.</span></span> <span data-ttu-id="6d72c-450">建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="6d72c-450">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="6d72c-451">在中间件中将 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="6d72c-451">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="6d72c-452">`Map*` 会更改执行请求的基路径，这会影响链接生成的输出。</span><span class="sxs-lookup"><span data-stu-id="6d72c-452">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="6d72c-453">所有 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允许指定基路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-453">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="6d72c-454">指定一个空的基路径来撤消 `Map*` 对链接生成的影响。</span><span class="sxs-lookup"><span data-stu-id="6d72c-454">Specify an empty base path to undo the `Map*` affect on link generation.</span></span>

### <a name="middleware-example"></a><span data-ttu-id="6d72c-455">中间件示例</span><span class="sxs-lookup"><span data-stu-id="6d72c-455">Middleware example</span></span>

<span data-ttu-id="6d72c-456">在以下示例中，中间件使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 创建列出存储产品的操作方法的链接。</span><span class="sxs-lookup"><span data-stu-id="6d72c-456">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create a link to an action method that lists store products.</span></span> <span data-ttu-id="6d72c-457">应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器：</span><span class="sxs-lookup"><span data-stu-id="6d72c-457">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a><span data-ttu-id="6d72c-458">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-458">Route template reference</span></span>

<span data-ttu-id="6d72c-459">如果路由找到匹配项，`{}` 内的令牌定义绑定的路由参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-459">Tokens within `{}` define route parameters that are bound if the route is matched.</span></span> <span data-ttu-id="6d72c-460">可在路由段中定义多个路由参数，但必须用文本值隔开这些路由参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-460">More than one route parameter can be defined in a route segment, but route parameters  must be separated by a literal value.</span></span> <span data-ttu-id="6d72c-461">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-461">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span>  <span data-ttu-id="6d72c-462">路由参数必须具有名称，且可能指定了其他特性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-462">Route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="6d72c-463">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="6d72c-463">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="6d72c-464">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-464">Text matching is case-insensitive and based on the decoded representation of the URL's path.</span></span> <span data-ttu-id="6d72c-465">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="6d72c-465">To match a literal route parameter delimiter `{` or `}`, escape the delimiter by repeating the character.</span></span> <span data-ttu-id="6d72c-466">例如 `{{` 或 `}}`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-466">For example `{{` or `}}`.</span></span>

<span data-ttu-id="6d72c-467">星号 `*` 或双星号 `**`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-467">Asterisk `*` or double asterisk `**`:</span></span>

* <span data-ttu-id="6d72c-468">可用作路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-468">Can be used as a prefix to a route parameter to bind to the rest of the URI.</span></span>
* <span data-ttu-id="6d72c-469">称为 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-469">Are called a **catch-all** parameters.</span></span> <span data-ttu-id="6d72c-470">例如，`blog/{**slug}`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-470">For example, `blog/{**slug}`:</span></span>
  * <span data-ttu-id="6d72c-471">匹配以 `/blog` 开头并在其后面包含任何值的任何 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-471">Matches any URI that starts with `/blog` and has any value following it.</span></span>
  * <span data-ttu-id="6d72c-472">`/blog` 后的值分配给 [slug](https://developer.mozilla.org/docs/Glossary/Slug) 路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-472">The value following `/blog` is assigned to the [slug](https://developer.mozilla.org/docs/Glossary/Slug) route value.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<span data-ttu-id="6d72c-473">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-473">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="6d72c-474">使用路由生成 URL（包括路径分隔符 `/`）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="6d72c-474">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator `/` characters.</span></span> <span data-ttu-id="6d72c-475">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-475">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="6d72c-476">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="6d72c-476">Note the escaped forward slash.</span></span> <span data-ttu-id="6d72c-477">要往返路径分隔符，请使用 `**` 路由参数前缀。</span><span class="sxs-lookup"><span data-stu-id="6d72c-477">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="6d72c-478">`{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-478">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="6d72c-479">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-479">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="6d72c-480">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-480">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="6d72c-481">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-481">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="6d72c-482">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随 `.` 是可选的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-482">If only a value for `filename` exists in the URL, the route matches because the trailing `.` is  optional.</span></span> <span data-ttu-id="6d72c-483">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="6d72c-483">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="6d72c-484">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-484">Route parameters may have **default values** designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="6d72c-485">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-485">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="6d72c-486">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-486">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="6d72c-487">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-487">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name.</span></span> <span data-ttu-id="6d72c-488">例如 `id?`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-488">For example, `id?`.</span></span> <span data-ttu-id="6d72c-489">可选值和默认路由参数之间的差异是：</span><span class="sxs-lookup"><span data-stu-id="6d72c-489">The difference between optional values and default route parameters is:</span></span>

* <span data-ttu-id="6d72c-490">具有默认值的路由参数始终生成一个值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-490">A route parameter with a default value always produces a value.</span></span>
* <span data-ttu-id="6d72c-491">仅当请求 URL 提供值时，可选参数才具有值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-491">An optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="6d72c-492">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-492">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="6d72c-493">在路由参数后面添加一个 `:` 和约束名称可指定路由参数上的内联约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-493">Adding `:` and constraint name after the route parameter name specifies an inline constraint on a route parameter.</span></span> <span data-ttu-id="6d72c-494">如果约束需要参数，将以在约束名称后括在括号 `(...)` 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="6d72c-494">If the constraint requires arguments, they're enclosed in parentheses `(...)` after the constraint name.</span></span> <span data-ttu-id="6d72c-495">通过追加另一个 `:` 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-495">Multiple *inline constraints* can be specified by appending another `:` and constraint name.</span></span>

<span data-ttu-id="6d72c-496">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-496">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="6d72c-497">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-497">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="6d72c-498">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-498">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="6d72c-499">路由参数还可以具有参数转换器。</span><span class="sxs-lookup"><span data-stu-id="6d72c-499">Route parameters may also have parameter transformers.</span></span> <span data-ttu-id="6d72c-500">参数转换器在生成链接以及将操作和页面匹配到 URI 时转换参数的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-500">Parameter transformers transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="6d72c-501">与约束类似，可在路由参数名称后面添加 `:` 和转换器名称，将参数变换器内联添加到路径参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-501">Like constraints, parameter transformers can be added inline to a route parameter by adding a `:` and transformer name after the route parameter name.</span></span> <span data-ttu-id="6d72c-502">例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。</span><span class="sxs-lookup"><span data-stu-id="6d72c-502">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="6d72c-503">有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-503">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="6d72c-504">下表演示了示例路由模板及其行为：</span><span class="sxs-lookup"><span data-stu-id="6d72c-504">The following table demonstrates example route templates and their behavior:</span></span>

| <span data-ttu-id="6d72c-505">路由模板</span><span class="sxs-lookup"><span data-stu-id="6d72c-505">Route Template</span></span>                           | <span data-ttu-id="6d72c-506">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="6d72c-506">Example Matching URI</span></span>    | <span data-ttu-id="6d72c-507">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="6d72c-507">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="6d72c-508">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-508">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-509">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-509">'Blazor'</span></span>
- <span data-ttu-id="6d72c-510">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-510">'Identity'</span></span>
- <span data-ttu-id="6d72c-511">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-511">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-512">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-512">'Razor'</span></span>
- <span data-ttu-id="6d72c-513">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-513">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-514">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-514">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-515">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-515">'Blazor'</span></span>
- <span data-ttu-id="6d72c-516">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-516">'Identity'</span></span>
- <span data-ttu-id="6d72c-517">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-517">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-518">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-518">'Razor'</span></span>
- <span data-ttu-id="6d72c-519">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-519">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-520">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-520">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-521">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-521">'Blazor'</span></span>
- <span data-ttu-id="6d72c-522">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-522">'Identity'</span></span>
- <span data-ttu-id="6d72c-523">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-523">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-524">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-524">'Razor'</span></span>
- <span data-ttu-id="6d72c-525">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-525">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-526">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-526">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-527">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-527">'Blazor'</span></span>
- <span data-ttu-id="6d72c-528">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-528">'Identity'</span></span>
- <span data-ttu-id="6d72c-529">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-529">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-530">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-530">'Razor'</span></span>
- <span data-ttu-id="6d72c-531">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-531">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-532">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-532">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-533">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-533">'Blazor'</span></span>
- <span data-ttu-id="6d72c-534">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-534">'Identity'</span></span>
- <span data-ttu-id="6d72c-535">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-535">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-536">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-536">'Razor'</span></span>
- <span data-ttu-id="6d72c-537">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-537">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-538">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-538">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-539">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-539">'Blazor'</span></span>
- <span data-ttu-id="6d72c-540">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-540">'Identity'</span></span>
- <span data-ttu-id="6d72c-541">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-541">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-542">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-542">'Razor'</span></span>
- <span data-ttu-id="6d72c-543">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-543">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-544">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-544">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-545">'Blazor'</span></span>
- <span data-ttu-id="6d72c-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-546">'Identity'</span></span>
- <span data-ttu-id="6d72c-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-548">'Razor'</span></span>
- <span data-ttu-id="6d72c-549">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-550">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-550">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-551">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-551">'Blazor'</span></span>
- <span data-ttu-id="6d72c-552">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-552">'Identity'</span></span>
- <span data-ttu-id="6d72c-553">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-553">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-554">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-554">'Razor'</span></span>
- <span data-ttu-id="6d72c-555">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-555">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-556">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-556">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-557">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-557">'Blazor'</span></span>
- <span data-ttu-id="6d72c-558">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-558">'Identity'</span></span>
- <span data-ttu-id="6d72c-559">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-559">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-560">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-560">'Razor'</span></span>
- <span data-ttu-id="6d72c-561">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-561">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-562">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-562">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-563">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-563">'Blazor'</span></span>
- <span data-ttu-id="6d72c-564">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-564">'Identity'</span></span>
- <span data-ttu-id="6d72c-565">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-565">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-566">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-566">'Razor'</span></span>
- <span data-ttu-id="6d72c-567">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-567">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-568">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-568">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-569">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-569">'Blazor'</span></span>
- <span data-ttu-id="6d72c-570">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-570">'Identity'</span></span>
- <span data-ttu-id="6d72c-571">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-571">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-572">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-572">'Razor'</span></span>
- <span data-ttu-id="6d72c-573">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-573">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-574">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-574">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-575">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-575">'Blazor'</span></span>
- <span data-ttu-id="6d72c-576">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-576">'Identity'</span></span>
- <span data-ttu-id="6d72c-577">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-577">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-578">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-578">'Razor'</span></span>
- <span data-ttu-id="6d72c-579">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-579">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-580">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-580">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-581">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-581">'Blazor'</span></span>
- <span data-ttu-id="6d72c-582">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-582">'Identity'</span></span>
- <span data-ttu-id="6d72c-583">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-583">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-584">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-584">'Razor'</span></span>
- <span data-ttu-id="6d72c-585">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-585">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-586">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-586">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-587">'Blazor'</span></span>
- <span data-ttu-id="6d72c-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-588">'Identity'</span></span>
- <span data-ttu-id="6d72c-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-590">'Razor'</span></span>
- <span data-ttu-id="6d72c-591">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-592">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-592">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-593">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-593">'Blazor'</span></span>
- <span data-ttu-id="6d72c-594">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-594">'Identity'</span></span>
- <span data-ttu-id="6d72c-595">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-595">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-596">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-596">'Razor'</span></span>
- <span data-ttu-id="6d72c-597">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-597">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-598">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-598">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-599">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-599">'Blazor'</span></span>
- <span data-ttu-id="6d72c-600">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-600">'Identity'</span></span>
- <span data-ttu-id="6d72c-601">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-601">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-602">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-602">'Razor'</span></span>
- <span data-ttu-id="6d72c-603">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-603">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-604">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-604">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-605">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-605">'Blazor'</span></span>
- <span data-ttu-id="6d72c-606">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-606">'Identity'</span></span>
- <span data-ttu-id="6d72c-607">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-607">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-608">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-608">'Razor'</span></span>
- <span data-ttu-id="6d72c-609">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-609">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-611">'Blazor'</span></span>
- <span data-ttu-id="6d72c-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-612">'Identity'</span></span>
- <span data-ttu-id="6d72c-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-614">'Razor'</span></span>
- <span data-ttu-id="6d72c-615">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-615">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-616">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-616">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-617">'Blazor'</span></span>
- <span data-ttu-id="6d72c-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-618">'Identity'</span></span>
- <span data-ttu-id="6d72c-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-620">'Razor'</span></span>
- <span data-ttu-id="6d72c-621">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-623">'Blazor'</span></span>
- <span data-ttu-id="6d72c-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-624">'Identity'</span></span>
- <span data-ttu-id="6d72c-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-626">'Razor'</span></span>
- <span data-ttu-id="6d72c-627">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-629">'Blazor'</span></span>
- <span data-ttu-id="6d72c-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-630">'Identity'</span></span>
- <span data-ttu-id="6d72c-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-632">'Razor'</span></span>
- <span data-ttu-id="6d72c-633">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-635">'Blazor'</span></span>
- <span data-ttu-id="6d72c-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-636">'Identity'</span></span>
- <span data-ttu-id="6d72c-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-638">'Razor'</span></span>
- <span data-ttu-id="6d72c-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-641">'Blazor'</span></span>
- <span data-ttu-id="6d72c-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-642">'Identity'</span></span>
- <span data-ttu-id="6d72c-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-644">'Razor'</span></span>
- <span data-ttu-id="6d72c-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-647">'Blazor'</span></span>
- <span data-ttu-id="6d72c-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-648">'Identity'</span></span>
- <span data-ttu-id="6d72c-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-650">'Razor'</span></span>
- <span data-ttu-id="6d72c-651">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-653">'Blazor'</span></span>
- <span data-ttu-id="6d72c-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-654">'Identity'</span></span>
- <span data-ttu-id="6d72c-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-656">'Razor'</span></span>
- <span data-ttu-id="6d72c-657">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-657">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-658">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-658">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-659">'Blazor'</span></span>
- <span data-ttu-id="6d72c-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-660">'Identity'</span></span>
- <span data-ttu-id="6d72c-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-662">'Razor'</span></span>
- <span data-ttu-id="6d72c-663">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-663">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-664">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-664">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-665">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-665">'Blazor'</span></span>
- <span data-ttu-id="6d72c-666">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-666">'Identity'</span></span>
- <span data-ttu-id="6d72c-667">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-667">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-668">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-668">'Razor'</span></span>
- <span data-ttu-id="6d72c-669">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-669">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-670">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-670">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-671">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-671">'Blazor'</span></span>
- <span data-ttu-id="6d72c-672">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-672">'Identity'</span></span>
- <span data-ttu-id="6d72c-673">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-673">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-674">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-674">'Razor'</span></span>
- <span data-ttu-id="6d72c-675">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-675">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-676">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-676">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-677">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-677">'Blazor'</span></span>
- <span data-ttu-id="6d72c-678">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-678">'Identity'</span></span>
- <span data-ttu-id="6d72c-679">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-679">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-680">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-680">'Razor'</span></span>
- <span data-ttu-id="6d72c-681">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-681">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-683">'Blazor'</span></span>
- <span data-ttu-id="6d72c-684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-684">'Identity'</span></span>
- <span data-ttu-id="6d72c-685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-685">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-686">'Razor'</span></span>
- <span data-ttu-id="6d72c-687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-689">'Blazor'</span></span>
- <span data-ttu-id="6d72c-690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-690">'Identity'</span></span>
- <span data-ttu-id="6d72c-691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-691">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-692">'Razor'</span></span>
- <span data-ttu-id="6d72c-693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-695">'Blazor'</span></span>
- <span data-ttu-id="6d72c-696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-696">'Identity'</span></span>
- <span data-ttu-id="6d72c-697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-697">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-698">'Razor'</span></span>
- <span data-ttu-id="6d72c-699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-701">'Blazor'</span></span>
- <span data-ttu-id="6d72c-702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-702">'Identity'</span></span>
- <span data-ttu-id="6d72c-703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-703">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-704">'Razor'</span></span>
- <span data-ttu-id="6d72c-705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-707">'Blazor'</span></span>
- <span data-ttu-id="6d72c-708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-708">'Identity'</span></span>
- <span data-ttu-id="6d72c-709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-709">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-710">'Razor'</span></span>
- <span data-ttu-id="6d72c-711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-713">'Blazor'</span></span>
- <span data-ttu-id="6d72c-714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-714">'Identity'</span></span>
- <span data-ttu-id="6d72c-715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-715">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-716">'Razor'</span></span>
- <span data-ttu-id="6d72c-717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-719">'Blazor'</span></span>
- <span data-ttu-id="6d72c-720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-720">'Identity'</span></span>
- <span data-ttu-id="6d72c-721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-721">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-722">'Razor'</span></span>
- <span data-ttu-id="6d72c-723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-725">'Blazor'</span></span>
- <span data-ttu-id="6d72c-726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-726">'Identity'</span></span>
- <span data-ttu-id="6d72c-727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-727">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-728">'Razor'</span></span>
- <span data-ttu-id="6d72c-729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-731">'Blazor'</span></span>
- <span data-ttu-id="6d72c-732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-732">'Identity'</span></span>
- <span data-ttu-id="6d72c-733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-733">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-734">'Razor'</span></span>
- <span data-ttu-id="6d72c-735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-737">'Blazor'</span></span>
- <span data-ttu-id="6d72c-738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-738">'Identity'</span></span>
- <span data-ttu-id="6d72c-739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-739">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-740">'Razor'</span></span>
- <span data-ttu-id="6d72c-741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-741">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-742">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-742">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-743">'Blazor'</span></span>
- <span data-ttu-id="6d72c-744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-744">'Identity'</span></span>
- <span data-ttu-id="6d72c-745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-745">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-746">'Razor'</span></span>
- <span data-ttu-id="6d72c-747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-749">'Blazor'</span></span>
- <span data-ttu-id="6d72c-750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-750">'Identity'</span></span>
- <span data-ttu-id="6d72c-751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-751">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-752">'Razor'</span></span>
- <span data-ttu-id="6d72c-753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-755">'Blazor'</span></span>
- <span data-ttu-id="6d72c-756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-756">'Identity'</span></span>
- <span data-ttu-id="6d72c-757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-757">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-758">'Razor'</span></span>
- <span data-ttu-id="6d72c-759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-761">'Blazor'</span></span>
- <span data-ttu-id="6d72c-762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-762">'Identity'</span></span>
- <span data-ttu-id="6d72c-763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-763">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-764">'Razor'</span></span>
- <span data-ttu-id="6d72c-765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-767">'Blazor'</span></span>
- <span data-ttu-id="6d72c-768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-768">'Identity'</span></span>
- <span data-ttu-id="6d72c-769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-769">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-770">'Razor'</span></span>
- <span data-ttu-id="6d72c-771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-773">'Blazor'</span></span>
- <span data-ttu-id="6d72c-774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-774">'Identity'</span></span>
- <span data-ttu-id="6d72c-775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-775">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-776">'Razor'</span></span>
- <span data-ttu-id="6d72c-777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-779">'Blazor'</span></span>
- <span data-ttu-id="6d72c-780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-780">'Identity'</span></span>
- <span data-ttu-id="6d72c-781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-781">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-782">'Razor'</span></span>
- <span data-ttu-id="6d72c-783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-785">'Blazor'</span></span>
- <span data-ttu-id="6d72c-786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-786">'Identity'</span></span>
- <span data-ttu-id="6d72c-787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-787">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-788">'Razor'</span></span>
- <span data-ttu-id="6d72c-789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-791">'Blazor'</span></span>
- <span data-ttu-id="6d72c-792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-792">'Identity'</span></span>
- <span data-ttu-id="6d72c-793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-793">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-794">'Razor'</span></span>
- <span data-ttu-id="6d72c-795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-797">'Blazor'</span></span>
- <span data-ttu-id="6d72c-798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-798">'Identity'</span></span>
- <span data-ttu-id="6d72c-799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-799">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-800">'Razor'</span></span>
- <span data-ttu-id="6d72c-801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-803">'Blazor'</span></span>
- <span data-ttu-id="6d72c-804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-804">'Identity'</span></span>
- <span data-ttu-id="6d72c-805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-805">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-806">'Razor'</span></span>
- <span data-ttu-id="6d72c-807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-807">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-808">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-808">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-809">'Blazor'</span></span>
- <span data-ttu-id="6d72c-810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-810">'Identity'</span></span>
- <span data-ttu-id="6d72c-811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-811">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-812">'Razor'</span></span>
- <span data-ttu-id="6d72c-813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-815">'Blazor'</span></span>
- <span data-ttu-id="6d72c-816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-816">'Identity'</span></span>
- <span data-ttu-id="6d72c-817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-817">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-818">'Razor'</span></span>
- <span data-ttu-id="6d72c-819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-821">'Blazor'</span></span>
- <span data-ttu-id="6d72c-822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-822">'Identity'</span></span>
- <span data-ttu-id="6d72c-823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-823">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-824">'Razor'</span></span>
- <span data-ttu-id="6d72c-825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-827">'Blazor'</span></span>
- <span data-ttu-id="6d72c-828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-828">'Identity'</span></span>
- <span data-ttu-id="6d72c-829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-829">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-830">'Razor'</span></span>
- <span data-ttu-id="6d72c-831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-833">'Blazor'</span></span>
- <span data-ttu-id="6d72c-834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-834">'Identity'</span></span>
- <span data-ttu-id="6d72c-835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-835">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-836">'Razor'</span></span>
- <span data-ttu-id="6d72c-837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-839">'Blazor'</span></span>
- <span data-ttu-id="6d72c-840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-840">'Identity'</span></span>
- <span data-ttu-id="6d72c-841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-841">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-842">'Razor'</span></span>
- <span data-ttu-id="6d72c-843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-845">'Blazor'</span></span>
- <span data-ttu-id="6d72c-846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-846">'Identity'</span></span>
- <span data-ttu-id="6d72c-847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-847">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-848">'Razor'</span></span>
- <span data-ttu-id="6d72c-849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-851">'Blazor'</span></span>
- <span data-ttu-id="6d72c-852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-852">'Identity'</span></span>
- <span data-ttu-id="6d72c-853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-853">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-854">'Razor'</span></span>
- <span data-ttu-id="6d72c-855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-857">'Blazor'</span></span>
- <span data-ttu-id="6d72c-858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-858">'Identity'</span></span>
- <span data-ttu-id="6d72c-859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-859">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-860">'Razor'</span></span>
- <span data-ttu-id="6d72c-861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-863">'Blazor'</span></span>
- <span data-ttu-id="6d72c-864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-864">'Identity'</span></span>
- <span data-ttu-id="6d72c-865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-865">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-866">'Razor'</span></span>
- <span data-ttu-id="6d72c-867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-869">'Blazor'</span></span>
- <span data-ttu-id="6d72c-870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-870">'Identity'</span></span>
- <span data-ttu-id="6d72c-871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-871">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-872">'Razor'</span></span>
- <span data-ttu-id="6d72c-873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-875">'Blazor'</span></span>
- <span data-ttu-id="6d72c-876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-876">'Identity'</span></span>
- <span data-ttu-id="6d72c-877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-877">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-878">'Razor'</span></span>
- <span data-ttu-id="6d72c-879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-879">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-880">------------------------------------- | | `hello`                                  | `/hello`                | 仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-880">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="6d72c-881">| | `{Page=Home}`                            | `/`                     | 匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-881">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="6d72c-882">| | `{Page=Home}`                            | `/Contact`              | 匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-882">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="6d72c-883">| | `{controller}/{action}/{id?}`            | `/Products/List`        | 映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="6d72c-883">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="6d72c-884">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | 映射到 `Products` 控制器和 `Details` 操作，并将 `id` 设置为 123。</span><span class="sxs-lookup"><span data-stu-id="6d72c-884">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action with`id` set to 123.</span></span> <span data-ttu-id="6d72c-885">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | 映射到 `Home` 控制器和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-885">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method.</span></span> <span data-ttu-id="6d72c-886">`id` 将被忽略。</span><span class="sxs-lookup"><span data-stu-id="6d72c-886">`id` is ignored.</span></span>        <span data-ttu-id="6d72c-887">| | `{controller=Home}/{action=Index}/{id?}` | `/Products`         | 映射到 `Products` 控制器和 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-887">| | `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Maps to the `Products` controller and `Index` method.</span></span> <span data-ttu-id="6d72c-888">`id` 将被忽略。</span><span class="sxs-lookup"><span data-stu-id="6d72c-888">`id` is ignored.</span></span>        |

<span data-ttu-id="6d72c-889">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-889">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="6d72c-890">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-890">Constraints and defaults can also be specified outside the route template.</span></span>

### <a name="complex-segments"></a><span data-ttu-id="6d72c-891">复杂段</span><span class="sxs-lookup"><span data-stu-id="6d72c-891">Complex segments</span></span>

<span data-ttu-id="6d72c-892">复杂段通过[非贪婪](#greedy)的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="6d72c-892">Complex segments are processed by matching up literal delimiters from right to left in a [non-greedy](#greedy) way.</span></span> <span data-ttu-id="6d72c-893">例如，`[Route("/a{b}c{d}")]` 是一个复杂段。</span><span class="sxs-lookup"><span data-stu-id="6d72c-893">For example, `[Route("/a{b}c{d}")]` is a complex segment.</span></span>
<span data-ttu-id="6d72c-894">复杂段以一种特定的方式工作，必须理解这种方式才能成功地使用它们。</span><span class="sxs-lookup"><span data-stu-id="6d72c-894">Complex segments work in a particular way that must be understood to use them successfully.</span></span> <span data-ttu-id="6d72c-895">本部分中的示例演示了为什么复杂段只有在分隔符文本没有出现在参数值中时才真正有效。</span><span class="sxs-lookup"><span data-stu-id="6d72c-895">The example in this section demonstrates why complex segments only really work well when the delimiter text doesn't appear inside the parameter values.</span></span> <span data-ttu-id="6d72c-896">对于更复杂的情况，需要使用 [regex](/dotnet/standard/base-types/regular-expressions)，然后手动提取值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-896">Using a [regex](/dotnet/standard/base-types/regular-expressions) and then manually extracting the values is needed for more complex cases.</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="6d72c-897">这是使用模板 `/a{b}c{d}` 和 URL 路径 `/abcd` 执行路由的步骤摘要。</span><span class="sxs-lookup"><span data-stu-id="6d72c-897">This is a summary of the steps that routing performs with the template `/a{b}c{d}` and the URL path `/abcd`.</span></span> <span data-ttu-id="6d72c-898">`|` 有助于可视化算法的工作方式：</span><span class="sxs-lookup"><span data-stu-id="6d72c-898">The `|` is used to help visualize how the algorithm works:</span></span>

* <span data-ttu-id="6d72c-899">从右到左的第一个文本是 `c`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-899">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="6d72c-900">因此从右侧搜索 `/abcd`，并查找 `/ab|c|d`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-900">So `/abcd` is searched from right and finds `/ab|c|d`.</span></span>
* <span data-ttu-id="6d72c-901">右侧的所有内容 (`d`) 现在都与路由参数 `{d}` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-901">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="6d72c-902">从右到左的下一个文本是 `a`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-902">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="6d72c-903">从我们停止的地方开始搜索 `/ab|c|d`，然后 `/|a|b|c|d` 找到 `a`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-903">So `/ab|c|d` is searched starting where we left off, then `a` is found `/|a|b|c|d`.</span></span>
* <span data-ttu-id="6d72c-904">右侧的值 (`b`) 现在与路由参数 `{b}` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-904">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="6d72c-905">没有剩余的文本，并且没有剩余的路由模板，因此这是一个匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-905">There is no remaining text and no remaining route template, so this is a match.</span></span>

<span data-ttu-id="6d72c-906">下面是使用相同模板 `/a{b}c{d}` 和 URL 路径 `/aabcd` 的负面案例示例。</span><span class="sxs-lookup"><span data-stu-id="6d72c-906">Here's an example of a negative case using the same template `/a{b}c{d}` and the URL path `/aabcd`.</span></span> <span data-ttu-id="6d72c-907">`|` 有助于可视化算法的工作方式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-907">The `|` is used to help visualize how the algorithm works.</span></span> <span data-ttu-id="6d72c-908">该案例不是匹配项，可由相同算法解释：</span><span class="sxs-lookup"><span data-stu-id="6d72c-908">This case isn't a match, which is explained by the same algorithm:</span></span>
* <span data-ttu-id="6d72c-909">从右到左的第一个文本是 `c`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-909">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="6d72c-910">因此从右侧搜索 `/aabcd`，并查找 `/aab|c|d`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-910">So `/aabcd` is searched from right and finds `/aab|c|d`.</span></span>
* <span data-ttu-id="6d72c-911">右侧的所有内容 (`d`) 现在都与路由参数 `{d}` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-911">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="6d72c-912">从右到左的下一个文本是 `a`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-912">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="6d72c-913">从我们停止的地方开始搜索 `/aab|c|d`，然后 `/a|a|b|c|d` 找到 `a`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-913">So `/aab|c|d` is searched starting where we left off, then `a` is found `/a|a|b|c|d`.</span></span>
* <span data-ttu-id="6d72c-914">右侧的值 (`b`) 现在与路由参数 `{b}` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-914">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="6d72c-915">此时还有剩余的文本 `a`，但是算法已经耗尽了要解析的路由模板，所以这不是一个匹配项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-915">At this point there is remaining text `a`, but the algorithm has run out of route template to parse, so this is not a match.</span></span>

<span data-ttu-id="6d72c-916">匹配算法是[非贪婪](#greedy)算法：</span><span class="sxs-lookup"><span data-stu-id="6d72c-916">Since the matching algorithm is [non-greedy](#greedy):</span></span>

* <span data-ttu-id="6d72c-917">它匹配每个步骤中最小可能文本量。</span><span class="sxs-lookup"><span data-stu-id="6d72c-917">It matches the smallest amount of text possible in each step.</span></span>
* <span data-ttu-id="6d72c-918">如果分隔符值出现在参数值内，则会导致不匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-918">Any case where the delimiter value appears inside the parameter values results in not matching.</span></span>

<span data-ttu-id="6d72c-919">正则表达式可以更好地控制它们的匹配行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-919">Regular expressions provide much more control over their matching behavior.</span></span>

<a name="greedy"></a>

<span data-ttu-id="6d72c-920">贪婪匹配（也称为[懒惰匹配](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)）匹配最大可能字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-920">Greedy matching, also know as [lazy matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), matches the largest possible string.</span></span> <span data-ttu-id="6d72c-921">非贪婪匹配最小可能字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-921">Non-greedy matches the smallest possible string.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="6d72c-922">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-922">Route constraint reference</span></span>

<span data-ttu-id="6d72c-923">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-923">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="6d72c-924">路径约束通常检查通过路径模板关联的路径值，并对该值是否为可接受做出对/错决定。</span><span class="sxs-lookup"><span data-stu-id="6d72c-924">Route constraints generally inspect the route value associated via the route template and make a true or false decision about whether the value is acceptable.</span></span> <span data-ttu-id="6d72c-925">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-925">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="6d72c-926">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-926">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="6d72c-927">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="6d72c-927">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="6d72c-928">请勿将约束用于输入验证。</span><span class="sxs-lookup"><span data-stu-id="6d72c-928">Don't use constraints for input validation.</span></span> <span data-ttu-id="6d72c-929">如果约束用于输入验证，则无效的输入将导致 `404`（找不到页面）响应。</span><span class="sxs-lookup"><span data-stu-id="6d72c-929">If constraints are used for input validation, invalid input results in a `404` Not Found response.</span></span> <span data-ttu-id="6d72c-930">无效输入可能生成包含相应错误消息的 `400` 错误请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-930">Invalid input should produce a `400` Bad Request with an appropriate error message.</span></span> <span data-ttu-id="6d72c-931">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入。</span><span class="sxs-lookup"><span data-stu-id="6d72c-931">Route constraints are used to disambiguate similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="6d72c-932">下表演示示例路由约束及其预期行为：</span><span class="sxs-lookup"><span data-stu-id="6d72c-932">The following table demonstrates example route constraints and their expected behavior:</span></span>

| <span data-ttu-id="6d72c-933">约束</span><span class="sxs-lookup"><span data-stu-id="6d72c-933">constraint</span></span> | <span data-ttu-id="6d72c-934">示例</span><span class="sxs-lookup"><span data-stu-id="6d72c-934">Example</span></span> | <span data-ttu-id="6d72c-935">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="6d72c-935">Example Matches</span></span> | <span data-ttu-id="6d72c-936">说明</span><span class="sxs-lookup"><span data-stu-id="6d72c-936">Notes</span></span> |
| ---
<span data-ttu-id="6d72c-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-938">'Blazor'</span></span>
- <span data-ttu-id="6d72c-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-939">'Identity'</span></span>
- <span data-ttu-id="6d72c-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-941">'Razor'</span></span>
- <span data-ttu-id="6d72c-942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-944">'Blazor'</span></span>
- <span data-ttu-id="6d72c-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-945">'Identity'</span></span>
- <span data-ttu-id="6d72c-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-947">'Razor'</span></span>
- <span data-ttu-id="6d72c-948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-950">'Blazor'</span></span>
- <span data-ttu-id="6d72c-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-951">'Identity'</span></span>
- <span data-ttu-id="6d72c-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-953">'Razor'</span></span>
- <span data-ttu-id="6d72c-954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-954">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-955">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-955">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-956">'Blazor'</span></span>
- <span data-ttu-id="6d72c-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-957">'Identity'</span></span>
- <span data-ttu-id="6d72c-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-959">'Razor'</span></span>
- <span data-ttu-id="6d72c-960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-960">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-961">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-961">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-962">'Blazor'</span></span>
- <span data-ttu-id="6d72c-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-963">'Identity'</span></span>
- <span data-ttu-id="6d72c-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-965">'Razor'</span></span>
- <span data-ttu-id="6d72c-966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-968">'Blazor'</span></span>
- <span data-ttu-id="6d72c-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-969">'Identity'</span></span>
- <span data-ttu-id="6d72c-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-971">'Razor'</span></span>
- <span data-ttu-id="6d72c-972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-974">'Blazor'</span></span>
- <span data-ttu-id="6d72c-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-975">'Identity'</span></span>
- <span data-ttu-id="6d72c-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-977">'Razor'</span></span>
- <span data-ttu-id="6d72c-978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-980">'Blazor'</span></span>
- <span data-ttu-id="6d72c-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-981">'Identity'</span></span>
- <span data-ttu-id="6d72c-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-983">'Razor'</span></span>
- <span data-ttu-id="6d72c-984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-986">'Blazor'</span></span>
- <span data-ttu-id="6d72c-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-987">'Identity'</span></span>
- <span data-ttu-id="6d72c-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-989">'Razor'</span></span>
- <span data-ttu-id="6d72c-990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-990">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-991">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | 匹配任何整数 | | `bool` | `{active:bool}` | `true`, `FALSE` | 匹配 `true` 或 `false`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-991">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer | | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false`.</span></span> <span data-ttu-id="6d72c-992">不区分大小写 | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 在固定区域性中匹配有效的 `DateTime` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-992">Case-insensitive | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="6d72c-993">请参阅前面的警告。</span><span class="sxs-lookup"><span data-stu-id="6d72c-993">See preceding warning.</span></span> <span data-ttu-id="6d72c-994">| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 在固定区域性中匹配有效的 `decimal` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-994">| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="6d72c-995">请参阅前面的警告。| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 在固定区域性中匹配有效的 `double` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-995">See preceding warning.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="6d72c-996">请参阅前面的警告。| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 在固定区域性中匹配有效的 `float` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-996">See preceding warning.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="6d72c-997">请参阅前面的警告。| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | 匹配有效的 `Guid` 值 | | `long` | `{ticks:long}` | `123456789`, `-123456789` | 匹配有效的 `long` 值 | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字符串必须至少为 4 个字符 | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 字符串不得超过 8 个字符 | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字符串必须正好为 12 个字符 | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字符串必须至少为 8 个字符，且不得超过 16 个字符 | | `min(value)` | `{age:min(18)}` | `19` | 整数值必须至少为 18 | | `max(value)` | `{age:max(120)}` | `91` | 整数值不得超过 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | 整数值必须至少为 18，且不得超过 120 | | `alpha` | `{name:alpha}` | `Rick` | 字符串必须由一个或多个字母字符（`a`-`z`，不区分大小写）组成。</span><span class="sxs-lookup"><span data-stu-id="6d72c-997">See preceding warning.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Matches a valid `Guid` value | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String must be no more than 8 characters | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and no more than 16 characters long | | `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18 | | `max(value)` | `{age:max(120)}` | `91` | Integer value must be no more than 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 but no more than 120 | | `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters, `a`-`z` and case-insensitive.</span></span> <span data-ttu-id="6d72c-998">| | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字符串必须与正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-998">| | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression.</span></span> <span data-ttu-id="6d72c-999">请参阅有关定义正则表达式的提示。</span><span class="sxs-lookup"><span data-stu-id="6d72c-999">See tips about defining a regular expression.</span></span> <span data-ttu-id="6d72c-1000">| | `required` | `{name:required}` | `Rick` | 用于强制在 URL 生成过程中存在非参数值 |</span><span class="sxs-lookup"><span data-stu-id="6d72c-1000">| | `required` | `{name:required}` | `Rick` | Used to enforce that a non-parameter value is present during URL generation |</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="6d72c-1001">可向单个参数应用多个用冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1001">Multiple, colon delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="6d72c-1002">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1002">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="6d72c-1003">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1003">Route constraints that verify the URL and are converted to a CLR type always use the invariant culture.</span></span> <span data-ttu-id="6d72c-1004">例如，转换为 CLR 类型 `int` 或 `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1004">For example, conversion to the CLR type `int` or `DateTime`.</span></span> <span data-ttu-id="6d72c-1005">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1005">These constraints assume that the URL is not localizable.</span></span> <span data-ttu-id="6d72c-1006">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1006">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="6d72c-1007">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1007">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="6d72c-1008">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1008">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

### <a name="regular-expressions-in-constraints"></a><span data-ttu-id="6d72c-1009">约束中的正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-1009">Regular expressions in constraints</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="6d72c-1010">使用 `regex(...)` 路由约束可以将正则表达式指定为内联约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1010">Regular expressions can be specified as inline constraints using the `regex(...)` route constraint.</span></span> <span data-ttu-id="6d72c-1011"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 系列中的方法还接受约束的对象文字。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1011">Methods in the <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> family also accept an object literal of constraints.</span></span> <span data-ttu-id="6d72c-1012">如果使用该窗体，则字符串值将解释为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1012">If that form is used, string values are interpreted as regular expressions.</span></span>

<span data-ttu-id="6d72c-1013">下面的代码使用内联 regex 约束：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1013">The following code uses an inline regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

<span data-ttu-id="6d72c-1014">下面的代码使用对象文字来指定 regex 约束：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1014">The following code uses an object literal to specify a regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

<span data-ttu-id="6d72c-1015">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1015">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="6d72c-1016">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1016">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="6d72c-1017">正则表达式与路由和 C# 语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1017">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="6d72c-1018">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1018">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="6d72c-1019">若要在内联约束中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，请使用以下某项：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1019">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in an inline constraint, use one of the following:</span></span>

* <span data-ttu-id="6d72c-1020">将字符串中提供的 `\` 字符替换为 C# 源文件中的 `\\` 字符，以便对 `\` 字符串转义字符进行转义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1020">Replace `\` characters provided in the string as `\\` characters in the C# source file in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="6d72c-1021">[逐字字符串文本](/dotnet/csharp/language-reference/keywords/string)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1021">[Verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="6d72c-1022">要对路由参数分隔符 `{`、`}`、`[`、`]` 进行转义，请将表达式（例如 `{{`、`}}`、`[[`、`]]`）中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1022">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression, for example, `{{`, `}}`, `[[`, `]]`.</span></span> <span data-ttu-id="6d72c-1023">下表展示了正则表达式及其转义的版本：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1023">The following table shows a regular expression and its escaped version:</span></span>

| <span data-ttu-id="6d72c-1024">正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-1024">Regular expression</span></span>    | <span data-ttu-id="6d72c-1025">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-1025">Escaped regular expression</span></span>     |
| ---
<span data-ttu-id="6d72c-1026">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1026">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1027">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1027">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1028">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1028">'Identity'</span></span>
- <span data-ttu-id="6d72c-1029">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1029">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1030">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1030">'Razor'</span></span>
- <span data-ttu-id="6d72c-1031">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1031">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1032">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1032">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1033">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1033">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1034">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1034">'Identity'</span></span>
- <span data-ttu-id="6d72c-1035">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1035">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1036">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1036">'Razor'</span></span>
- <span data-ttu-id="6d72c-1037">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1037">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1038">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1038">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1039">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1039">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1040">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1040">'Identity'</span></span>
- <span data-ttu-id="6d72c-1041">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1041">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1042">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1042">'Razor'</span></span>
- <span data-ttu-id="6d72c-1043">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1043">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1044">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1044">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1045">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1045">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1046">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1046">'Identity'</span></span>
- <span data-ttu-id="6d72c-1047">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1047">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1048">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1048">'Razor'</span></span>
- <span data-ttu-id="6d72c-1049">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1049">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1050">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1050">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1051">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1051">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1052">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1052">'Identity'</span></span>
- <span data-ttu-id="6d72c-1053">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1053">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1054">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1054">'Razor'</span></span>
- <span data-ttu-id="6d72c-1055">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1055">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1056">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1056">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1057">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1057">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1058">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1058">'Identity'</span></span>
- <span data-ttu-id="6d72c-1059">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1059">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1060">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1060">'Razor'</span></span>
- <span data-ttu-id="6d72c-1061">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1061">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1062">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1062">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1063">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1063">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1064">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1064">'Identity'</span></span>
- <span data-ttu-id="6d72c-1065">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1065">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1066">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1066">'Razor'</span></span>
- <span data-ttu-id="6d72c-1067">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1067">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1068">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1068">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1069">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1069">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1070">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1070">'Identity'</span></span>
- <span data-ttu-id="6d72c-1071">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1071">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1072">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1072">'Razor'</span></span>
- <span data-ttu-id="6d72c-1073">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1073">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1074">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1074">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1075">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1075">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1076">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1076">'Identity'</span></span>
- <span data-ttu-id="6d72c-1077">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1077">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1078">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1078">'Razor'</span></span>
- <span data-ttu-id="6d72c-1079">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1079">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1080">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1080">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1081">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1081">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1082">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1082">'Identity'</span></span>
- <span data-ttu-id="6d72c-1083">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1083">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1084">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1084">'Razor'</span></span>
- <span data-ttu-id="6d72c-1085">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1085">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1086">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1086">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1087">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1087">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1088">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1088">'Identity'</span></span>
- <span data-ttu-id="6d72c-1089">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1089">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1090">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1090">'Razor'</span></span>
- <span data-ttu-id="6d72c-1091">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1091">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1092">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1092">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1093">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1093">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1094">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1094">'Identity'</span></span>
- <span data-ttu-id="6d72c-1095">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1095">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1096">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1096">'Razor'</span></span>
- <span data-ttu-id="6d72c-1097">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1097">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1098">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1098">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1099">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1099">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1100">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1100">'Identity'</span></span>
- <span data-ttu-id="6d72c-1101">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1101">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1102">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1102">'Razor'</span></span>
- <span data-ttu-id="6d72c-1103">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1103">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1104">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1104">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1105">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1105">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1106">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1106">'Identity'</span></span>
- <span data-ttu-id="6d72c-1107">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1107">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1108">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1108">'Razor'</span></span>
- <span data-ttu-id="6d72c-1109">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1109">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1110">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1110">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1111">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1111">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1112">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1112">'Identity'</span></span>
- <span data-ttu-id="6d72c-1113">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1113">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1114">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1114">'Razor'</span></span>
- <span data-ttu-id="6d72c-1115">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1115">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1116">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1116">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1117">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1117">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1118">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1118">'Identity'</span></span>
- <span data-ttu-id="6d72c-1119">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1119">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1120">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1120">'Razor'</span></span>
- <span data-ttu-id="6d72c-1121">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1121">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1122">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1122">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1123">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1123">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1124">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1124">'Identity'</span></span>
- <span data-ttu-id="6d72c-1125">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1125">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1126">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1126">'Razor'</span></span>
- <span data-ttu-id="6d72c-1127">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1127">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1128">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1128">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1129">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1129">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1130">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1130">'Identity'</span></span>
- <span data-ttu-id="6d72c-1131">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1131">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1132">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1132">'Razor'</span></span>
- <span data-ttu-id="6d72c-1133">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1133">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1134">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1134">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1135">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1136">'Identity'</span></span>
- <span data-ttu-id="6d72c-1137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1137">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1138">'Razor'</span></span>
- <span data-ttu-id="6d72c-1139">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1140">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1140">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1141">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1142">'Identity'</span></span>
- <span data-ttu-id="6d72c-1143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1143">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1144">'Razor'</span></span>
- <span data-ttu-id="6d72c-1145">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1146">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1146">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1147">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1148">'Identity'</span></span>
- <span data-ttu-id="6d72c-1149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1149">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1150">'Razor'</span></span>
- <span data-ttu-id="6d72c-1151">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1151">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1152">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="6d72c-1152">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="6d72c-1153">路由中使用的正则表达式通常以 `^` 字符开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1153">Regular expressions used in routing often start with the `^` character and match the starting position of the string.</span></span> <span data-ttu-id="6d72c-1154">表达式通常以 `$` 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1154">The expressions often end with the `$` character and match the end of the string.</span></span> <span data-ttu-id="6d72c-1155">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1155">The `^` and `$` characters ensure that the regular expression matches the entire route parameter value.</span></span> <span data-ttu-id="6d72c-1156">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1156">Without the `^` and `$` characters, the regular expression matches any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="6d72c-1157">下表提供了示例并说明了它们匹配或匹配失败的原因：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1157">The following table provides examples and explains why they match or fail to match:</span></span>

| <span data-ttu-id="6d72c-1158">表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-1158">Expression</span></span>   | <span data-ttu-id="6d72c-1159">String</span><span class="sxs-lookup"><span data-stu-id="6d72c-1159">String</span></span>    | <span data-ttu-id="6d72c-1160">匹配</span><span class="sxs-lookup"><span data-stu-id="6d72c-1160">Match</span></span> | <span data-ttu-id="6d72c-1161">注释</span><span class="sxs-lookup"><span data-stu-id="6d72c-1161">Comment</span></span>               |
| ---
<span data-ttu-id="6d72c-1162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1163">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1164">'Identity'</span></span>
- <span data-ttu-id="6d72c-1165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1165">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1166">'Razor'</span></span>
- <span data-ttu-id="6d72c-1167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1169">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1170">'Identity'</span></span>
- <span data-ttu-id="6d72c-1171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1171">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1172">'Razor'</span></span>
- <span data-ttu-id="6d72c-1173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1175">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1176">'Identity'</span></span>
- <span data-ttu-id="6d72c-1177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1177">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1178">'Razor'</span></span>
- <span data-ttu-id="6d72c-1179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1179">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1180">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1180">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1181">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1182">'Identity'</span></span>
- <span data-ttu-id="6d72c-1183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1183">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1184">'Razor'</span></span>
- <span data-ttu-id="6d72c-1185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1185">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1186">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1186">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1187">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1188">'Identity'</span></span>
- <span data-ttu-id="6d72c-1189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1189">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1190">'Razor'</span></span>
- <span data-ttu-id="6d72c-1191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1193">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1194">'Identity'</span></span>
- <span data-ttu-id="6d72c-1195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1195">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1196">'Razor'</span></span>
- <span data-ttu-id="6d72c-1197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1197">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1198">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1198">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1199">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1200">'Identity'</span></span>
- <span data-ttu-id="6d72c-1201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1201">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1202">'Razor'</span></span>
- <span data-ttu-id="6d72c-1203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1205">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1206">'Identity'</span></span>
- <span data-ttu-id="6d72c-1207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1207">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1208">'Razor'</span></span>
- <span data-ttu-id="6d72c-1209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1211">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1212">'Identity'</span></span>
- <span data-ttu-id="6d72c-1213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1213">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1214">'Razor'</span></span>
- <span data-ttu-id="6d72c-1215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1217">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1218">'Identity'</span></span>
- <span data-ttu-id="6d72c-1219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1219">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1220">'Razor'</span></span>
- <span data-ttu-id="6d72c-1221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1223">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1224">'Identity'</span></span>
- <span data-ttu-id="6d72c-1225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1225">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1226">'Razor'</span></span>
- <span data-ttu-id="6d72c-1227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1229">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1230">'Identity'</span></span>
- <span data-ttu-id="6d72c-1231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1231">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1232">'Razor'</span></span>
- <span data-ttu-id="6d72c-1233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1235">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1236">'Identity'</span></span>
- <span data-ttu-id="6d72c-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1238">'Razor'</span></span>
- <span data-ttu-id="6d72c-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1241">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1242">'Identity'</span></span>
- <span data-ttu-id="6d72c-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1244">'Razor'</span></span>
- <span data-ttu-id="6d72c-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1245">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1246">---------- | | `[a-z]{2}`   | hello     | 是   | 子字符串匹配      | | `[a-z]{2}`   | 123abc456 | 是   | 子字符串匹配     | | `[a-z]{2}`   | mz        | 是   | 匹配表达式    | | `[a-z]{2}`   | MZ        | 是   | 不区分大小写    | | `^[a-z]{2}$` | hello     | 否    | 请参阅上述的 `^` 和 `$` | | `^[a-z]{2}$` | 123abc456 | 否    | 请参阅上述的 `^` 和 `$` |</span><span class="sxs-lookup"><span data-stu-id="6d72c-1246">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="6d72c-1247">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1247">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="6d72c-1248">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1248">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="6d72c-1249">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1249">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="6d72c-1250">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1250">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="6d72c-1251">已传递到约束字典且不匹配任何已知约束的约束也将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1251">Constraints that are passed in the constraints dictionary that don't match one of the known constraints are also treated as regular expressions.</span></span> <span data-ttu-id="6d72c-1252">模板内传递且不匹配任何已知约束的约束将不被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1252">Constraints that are passed  within a template that don't match one of the known constraints are not treated as regular expressions.</span></span>

### <a name="custom-route-constraints"></a><span data-ttu-id="6d72c-1253">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="6d72c-1253">Custom route constraints</span></span>

<span data-ttu-id="6d72c-1254">可实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1254">Custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="6d72c-1255">`IRouteConstraint` 接口包含 <xref:System.Web.Routing.IRouteConstraint.Match*>，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1255">The `IRouteConstraint` interface contains <xref:System.Web.Routing.IRouteConstraint.Match*>, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="6d72c-1256">很少需要自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1256">Custom route constraints are rarely needed.</span></span> <span data-ttu-id="6d72c-1257">在实现自定义路由约束之前，请考虑替代方法，如模型绑定。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1257">Before implementing a custom route constraint, consider alternatives, such as model binding.</span></span>

<span data-ttu-id="6d72c-1258">ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) 文件夹提供了创建约束的经典示例。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1258">The ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) folder provides good examples of creating a constraints.</span></span> <span data-ttu-id="6d72c-1259">例如 [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1259">For example, [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).</span></span>

<span data-ttu-id="6d72c-1260">若要使用自定义 `IRouteConstraint`，必须在服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1260">To use a custom `IRouteConstraint`, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the service container.</span></span> <span data-ttu-id="6d72c-1261">`ConstraintMap` 是将路由约束键映射到验证这些约束的 `IRouteConstraint` 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1261">A `ConstraintMap` is a dictionary that maps route constraint keys to `IRouteConstraint` implementations that validate those constraints.</span></span> <span data-ttu-id="6d72c-1262">应用的 `ConstraintMap` 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1262">An app's `ConstraintMap` can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="6d72c-1263">例如：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1263">For example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

<span data-ttu-id="6d72c-1264">前面的约束应用于以下代码：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1264">The preceding constraint is applied in the following code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<span data-ttu-id="6d72c-1265">实现 `MyCustomConstraint` 可防止将 `0` 应用于路由参数：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1265">The implementation of `MyCustomConstraint` prevents `0` being applied to a route parameter:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="6d72c-1266">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1266">The preceding code:</span></span>

* <span data-ttu-id="6d72c-1267">阻止路由的 `{id}` 段中的 `0`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1267">Prevents `0` in the `{id}` segment of the route.</span></span>
* <span data-ttu-id="6d72c-1268">显示以提供实现自定义约束的基本示例。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1268">Is shown to provide a basic example of implementing a custom constraint.</span></span> <span data-ttu-id="6d72c-1269">不应在产品应用中使用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1269">It should not be used in a production app.</span></span>

<span data-ttu-id="6d72c-1270">下面的代码是防止处理包含 `0` 的 `id` 的更好方法：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1270">The following code is a better approach to preventing an `id` containing a `0` from being processed:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="6d72c-1271">前面的代码与 `MyCustomConstraint` 方法相比具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1271">The preceding code has the following advantages over the `MyCustomConstraint` approach:</span></span>

* <span data-ttu-id="6d72c-1272">它不需要自定义约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1272">It doesn't require a custom constraint.</span></span>
* <span data-ttu-id="6d72c-1273">当路由参数包括 `0` 时，它将返回更具描述性的错误。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1273">It returns a more descriptive error when the route parameter includes `0`.</span></span>

## <a name="parameter-transformer-reference"></a><span data-ttu-id="6d72c-1274">参数转换器参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-1274">Parameter transformer reference</span></span>

<span data-ttu-id="6d72c-1275">参数转换器：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1275">Parameter transformers:</span></span>

* <span data-ttu-id="6d72c-1276">使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 生成链接时执行。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1276">Execute when generating a link using <xref:Microsoft.AspNetCore.Routing.LinkGenerator>.</span></span>
* <span data-ttu-id="6d72c-1277">实现 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1277">Implement <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.</span></span>
* <span data-ttu-id="6d72c-1278">使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1278">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="6d72c-1279">获取参数的路由值并将其转换为新的字符串值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1279">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="6d72c-1280">在生成的链接中使用转换后的值的结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1280">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="6d72c-1281">例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1281">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="6d72c-1282">请考虑以下 `IOutboundParameterTransformer` 实现：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1282">Consider the following `IOutboundParameterTransformer` implementation:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

<span data-ttu-id="6d72c-1283">若要在路由模式中使用参数转换器，请在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1283">To use a parameter transformer in a route pattern, configure it using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

<span data-ttu-id="6d72c-1284">ASP.NET Core 框架使用参数转化器来转换进行终结点解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1284">The ASP.NET Core framework uses parameter transformers to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="6d72c-1285">例如，参数转换器转换用于匹配 `area`、`controller`、`action` 和 `page` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1285">For example, parameter transformers transform the route values used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="6d72c-1286">使用上述路由模板，可将操作 `SubscriptionManagementController.GetAll` 与 URI `/subscription-management/get-all` 相匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1286">With the preceding route template, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="6d72c-1287">参数转换器不会更改用于生成链接的路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1287">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="6d72c-1288">例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1288">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="6d72c-1289">对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1289">ASP.NET Core provides API conventions for using parameter transformers with generated routes:</span></span>

* <span data-ttu-id="6d72c-1290"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC 约定将指定的参数转换器应用于应用中的所有特性路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1290">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="6d72c-1291">在替换属性路径令牌时，参数转换器将转换这些令牌。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1291">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="6d72c-1292">有关详细信息，请参阅[使用参数转换器自定义标记替换](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1292">For more information, see [Use a parameter transformer to customize token replacement](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* Razor<span data-ttu-id="6d72c-1293"> Pages 使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API 约定。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1293"> Pages uses the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API convention.</span></span> <span data-ttu-id="6d72c-1294">此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1294">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="6d72c-1295">参数转换器转换 Razor Pages 路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1295">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="6d72c-1296">有关详细信息，请参阅[使用参数转换器自定义页面路由](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1296">For more information, see [Use a parameter transformer to customize page routes](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

<a name="ugr"></a>

## <a name="url-generation-reference"></a><span data-ttu-id="6d72c-1297">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-1297">URL generation reference</span></span>

<span data-ttu-id="6d72c-1298">本部分包含 URL 生成实现的算法的参考。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1298">This section contains a reference for the algorithm implemented by URL generation.</span></span> <span data-ttu-id="6d72c-1299">在实践中，最复杂的 URL 生成示例使用控制器或 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1299">In practice, most complex examples of URL generation use controllers or Razor Pages.</span></span> <span data-ttu-id="6d72c-1300">有关其他信息，请参阅[控制器中的路由](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1300">See  [routing in controllers](xref:mvc/controllers/routing) for additional information.</span></span>

<span data-ttu-id="6d72c-1301">URL 生成过程首先调用 [GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) 或类似方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1301">The URL generation process begins with a call to [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) or a similar method.</span></span> <span data-ttu-id="6d72c-1302">此方法提供了一个地址、一组路由值以及有关 `HttpContext` 中当前请求的可选信息。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1302">The method is provided with an address, a set of route values, and optionally information about the current request from `HttpContext`.</span></span>

<span data-ttu-id="6d72c-1303">第一步是使用地址解析一组候选终结点，该终结点使用与该地址类型匹配的 [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1303">The first step is to use the address to resolve a set of candidate endpoints using an [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) that matches the address's type.</span></span>

<span data-ttu-id="6d72c-1304">地址方案找到一组候选项后，就会以迭代方式对终结点进行排序和处理，直到 URL 生成操作成功。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1304">Once of set of candidates is found by the address scheme, the endpoints are ordered and processed iteratively until a URL generation operation succeeds.</span></span> <span data-ttu-id="6d72c-1305">URL 生成不检查多义性，返回的第一个结果就是最终结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1305">URL generation does **not** check for ambiguities, the first result returned is the final result.</span></span>

### <a name="troubleshooting-url-generation-with-logging"></a><span data-ttu-id="6d72c-1306">使用日志记录对 URL 生成进行故障排除</span><span class="sxs-lookup"><span data-stu-id="6d72c-1306">Troubleshooting URL generation with logging</span></span>

<span data-ttu-id="6d72c-1307">对 URL 生成进行故障排除的第一步是将 `Microsoft.AspNetCore.Routing` 的日志记录级别设置为 `TRACE`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1307">The first step in troubleshooting URL generation is setting the logging level of `Microsoft.AspNetCore.Routing` to `TRACE`.</span></span> <span data-ttu-id="6d72c-1308">`LinkGenerator` 记录有关其处理的许多详细信息，有助于排查问题。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1308">`LinkGenerator` logs many details about its processing which can be useful to troubleshoot problems.</span></span>

<span data-ttu-id="6d72c-1309">有关 URL 生成的详细信息，请参阅 [URL 生成参考](#ugr)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1309">See [URL generation reference](#ugr) for details on URL generation.</span></span>

### <a name="addresses"></a><span data-ttu-id="6d72c-1310">地址</span><span class="sxs-lookup"><span data-stu-id="6d72c-1310">Addresses</span></span>

<span data-ttu-id="6d72c-1311">地址是 URL 生成中的概念，用于将链接生成器中的调用绑定到一组终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1311">Addresses are the concept in URL generation used to bind a call into the link generator to a set of candidate endpoints.</span></span>

<span data-ttu-id="6d72c-1312">地址是一个可扩展的概念，默认情况下有两种实现：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1312">Addresses are an extensible concept that come with two implementations by default:</span></span>

* <span data-ttu-id="6d72c-1313">使用终结点名称 (`string`) 作为地址：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1313">Using *endpoint name* (`string`) as the address:</span></span>
    * <span data-ttu-id="6d72c-1314">提供与 MVC 的路由名称相似的功能。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1314">Provides similar functionality to MVC's route name.</span></span>
    * <span data-ttu-id="6d72c-1315">使用 <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> 元数据类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1315">Uses the <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> metadata type.</span></span>
    * <span data-ttu-id="6d72c-1316">根据所有注册的终结点的元数据解析提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1316">Resolves the provided string against the metadata of all registered endpoints.</span></span>
    * <span data-ttu-id="6d72c-1317">如果多个终结点使用相同的名称，启动时会引发异常。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1317">Throws an exception on startup if multiple endpoints use the same name.</span></span>
    * <span data-ttu-id="6d72c-1318">建议用于控制器和 Razor Pages 之外的常规用途。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1318">Recommended for general-purpose use outside of controllers and Razor Pages.</span></span>
* <span data-ttu-id="6d72c-1319">使用路由值 (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) 作为地址：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1319">Using *route values* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) as the address:</span></span>
    * <span data-ttu-id="6d72c-1320">提供与控制器和 Razor Pages 生成旧 URL 相同的功能。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1320">Provides similar functionality to controllers and Razor Pages legacy URL generation.</span></span>
    * <span data-ttu-id="6d72c-1321">扩展和调试非常复杂。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1321">Very complex to extend and debug.</span></span>
    * <span data-ttu-id="6d72c-1322">提供 `IUrlHelper`、标记帮助程序、HTML 帮助程序、操作结果等所使用的实现。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1322">Provides the implementation used by `IUrlHelper`, Tag Helpers, HTML Helpers, Action Results, etc.</span></span>

<span data-ttu-id="6d72c-1323">地址方案的作用是根据任意条件在地址和匹配终结点之间建立关联：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1323">The role of the address scheme is to make the association between the address and matching endpoints by arbitrary criteria:</span></span>

* <span data-ttu-id="6d72c-1324">终结点名称方案执行基本字典查找。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1324">The endpoint name scheme performs a basic dictionary lookup.</span></span>
* <span data-ttu-id="6d72c-1325">路由值方案有一个复杂的子集算法，这是集算法的最佳子集。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1325">The route values scheme has a complex best subset of set algorithm.</span></span>

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a><span data-ttu-id="6d72c-1326">环境值和显式值</span><span class="sxs-lookup"><span data-stu-id="6d72c-1326">Ambient values and explicit values</span></span>

<span data-ttu-id="6d72c-1327">从当前请求开始，路由将访问当前请求 `HttpContext.Request.RouteValues` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1327">From the current request, routing accesses the route values of the current request `HttpContext.Request.RouteValues`.</span></span> <span data-ttu-id="6d72c-1328">与当前请求关联的值称为环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1328">The values associated with the current request are referred to as the **ambient values**.</span></span> <span data-ttu-id="6d72c-1329">为清楚起见，文档是指作为显式值传入方法的路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1329">For the purpose of clarity, the documentation refers to the route values passed in to methods as **explicit values**.</span></span>

<span data-ttu-id="6d72c-1330">下面的示例演示了环境值和显式值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1330">The following example shows ambient values and explicit values.</span></span> <span data-ttu-id="6d72c-1331">它将提供当前请求中的环境值和显式值：`{ id = 17, }`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1331">It provides ambient values from the current request and explicit values: `{ id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

<span data-ttu-id="6d72c-1332">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1332">The preceding code:</span></span>

* <span data-ttu-id="6d72c-1333">返回 `/Widget/Index/17`</span><span class="sxs-lookup"><span data-stu-id="6d72c-1333">Returns `/Widget/Index/17`</span></span>
* <span data-ttu-id="6d72c-1334">通过 [DI](xref:fundamentals/dependency-injection) 获取 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1334">Gets <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6d72c-1335">下面的代码不提供环境值和显式值：`{ controller = "Home", action = "Subscribe", id = 17, }`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1335">The following code provides no ambient values and explicit values: `{ controller = "Home", action = "Subscribe", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

<span data-ttu-id="6d72c-1336">前面的方法返回 `/Home/Subscribe/17`</span><span class="sxs-lookup"><span data-stu-id="6d72c-1336">The preceding  method returns `/Home/Subscribe/17`</span></span>

<span data-ttu-id="6d72c-1337">`WidgetController` 中的下列代码返回 `/Widget/Subscribe/17`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1337">The following code in the `WidgetController` returns `/Widget/Subscribe/17`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

<span data-ttu-id="6d72c-1338">下面的代码提供当前请求中的环境值和显式值中的控制器：`{ action = "Edit", id = 17, }`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1338">The following code provides the controller from ambient values in the current request and explicit values: `{ action = "Edit", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

<span data-ttu-id="6d72c-1339">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1339">In the preceding code:</span></span>

* <span data-ttu-id="6d72c-1340">返回 `/Gadget/Edit/17`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1340">`/Gadget/Edit/17` is returned.</span></span>
* <span data-ttu-id="6d72c-1341"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> 获取 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1341"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> gets the <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.</span></span>
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
<span data-ttu-id="6d72c-1342">生成一个 URL，其中包含操作方法的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1342">generates a URL with an absolute path for an action method.</span></span> <span data-ttu-id="6d72c-1343">URL 包含指定的 `action` 名称和 `route` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1343">The URL contains the specified `action` name and `route` values.</span></span>

<span data-ttu-id="6d72c-1344">下面的代码提供当前请求中的环境值和显式值：`{ page = "./Edit, id = 17, }`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1344">The following code provides ambient values from the current request and explicit values: `{ page = "./Edit, id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="6d72c-1345">当“编辑 Razor”页包含以下页面指令时，前面的代码会将 `url` 设置为 `/Edit/17`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1345">The preceding code sets `url` to  `/Edit/17` when the Edit Razor Page contains the following page directive:</span></span>

 `@page "{id:int}"`

<span data-ttu-id="6d72c-1346">如果“编辑”页面不包含 `"{id:int}"` 路由模板，`url` 为 `/Edit?id=17`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1346">If the Edit page doesn't contain the `"{id:int}"` route template, `url` is `/Edit?id=17`.</span></span>

<span data-ttu-id="6d72c-1347">除了此处所述的规则外，MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的行为还增加了一层复杂性：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1347">The behavior of MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> adds a layer of complexity in addition to the rules described here:</span></span>

* <span data-ttu-id="6d72c-1348">`IUrlHelper` 始终将当前请求中的路由值作为环境值提供。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1348">`IUrlHelper` always provides the route values from the current request as ambient values.</span></span>
* <span data-ttu-id="6d72c-1349">[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) 始终将当前 `action` 和 `controller` 路由值复制为显式值，除非由开发者重写。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1349">[IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) always copies the current `action` and `controller` route values as explicit values unless overridden by the developer.</span></span>
* <span data-ttu-id="6d72c-1350">[IUrlHelper](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) 始终将当前 `page` 路由值复制为显式值，除非重写。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1350">[IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) always copies the current `page` route value as an explicit value unless overridden.</span></span> <!--by the user-->
* <span data-ttu-id="6d72c-1351">`IUrlHelper.Page` 始终替代当前 `handler` 路由值，且 `null` 为显式值，除非重写。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1351">`IUrlHelper.Page` always overrides the current `handler` route value with `null` as an explicit values unless overridden.</span></span>

<span data-ttu-id="6d72c-1352">用户常常对环境值的行为详细信息感到惊讶，因为 MVC 似乎不遵循自己的规则。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1352">Users are often surprised by the behavioral details of ambient values, because MVC doesn't seem to follow its own rules.</span></span> <span data-ttu-id="6d72c-1353">出于历史和兼容性原因，某些路由值（例如 `action`、`controller`、`page` 和 `handler`）具有其自己的特殊行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1353">For historical and compatibility reasons, certain route values such as `action`, `controller`, `page`, and `handler` have their own special-case behavior.</span></span>

<span data-ttu-id="6d72c-1354">`LinkGenerator.GetPathByAction` 和 `LinkGenerator.GetPathByPage` 提供的等效功能与 `IUrlHelper` 的兼容性异常相同。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1354">The equivalent functionality provided by `LinkGenerator.GetPathByAction` and `LinkGenerator.GetPathByPage` duplicates these anomalies of `IUrlHelper` for compatibility.</span></span>

### <a name="url-generation-process"></a><span data-ttu-id="6d72c-1355">URL 生成过程</span><span class="sxs-lookup"><span data-stu-id="6d72c-1355">URL generation process</span></span>

<span data-ttu-id="6d72c-1356">找到候选终结点集后，URL 生成算法将：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1356">Once the set of candidate endpoints are found, the URL generation algorithm:</span></span>

* <span data-ttu-id="6d72c-1357">以迭代方式处理终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1357">Processes the endpoints iteratively.</span></span>
* <span data-ttu-id="6d72c-1358">返回第一个成功的结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1358">Returns the first successful result.</span></span>

<span data-ttu-id="6d72c-1359">此过程的第一步称为路由值失效。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1359">The first step in this process is called **route value invalidation**.</span></span>  <span data-ttu-id="6d72c-1360">路由值失效是路由决定应使用环境值中的哪些路由值以及应忽略哪些路由值的过程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1360">Route value invalidation is the process by which routing decides which route values from the ambient values should be used and which should be ignored.</span></span> <span data-ttu-id="6d72c-1361">将考虑每个环境值，要么与显式值组合，要么忽略它们。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1361">Each ambient value is considered and either combined with the explicit values, or ignored.</span></span>

<span data-ttu-id="6d72c-1362">考虑环境值角色的最佳方式是在某些常见情况下，尝试保存应用程序开发者的键入内容。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1362">The best way to think about the role of ambient values is that they attempt to save application developers typing, in some common cases.</span></span> <span data-ttu-id="6d72c-1363">就传统意义而言，环境值非常有用的情况与 MVC 相关：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1363">Traditionally, the scenarios where ambient values are helpful are related to MVC:</span></span>

* <span data-ttu-id="6d72c-1364">链接到同一控制器中的其他操作时，不需要指定控制器名称。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1364">When linking to another action in the same controller, the controller name doesn't need to be specified.</span></span>
* <span data-ttu-id="6d72c-1365">链接到同一区域中的另一个控制器时，不需要指定区域名称。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1365">When linking to another controller in the same area, the area name doesn't need to be specified.</span></span>
* <span data-ttu-id="6d72c-1366">链接到相同的操作方法时，不需要指定路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1366">When linking to the same action method, route values don't need to be specified.</span></span>
* <span data-ttu-id="6d72c-1367">链接到应用的其他部分时，不需要在应用的该部分中传递无意义的路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1367">When linking to another part of the app, you don't want to carry over route values that have no meaning in that part of the app.</span></span>

<span data-ttu-id="6d72c-1368">如果不了解路由值失效，通常就会导致对返回 `null` 的 `LinkGenerator` 或 `IUrlHelper` 执行调用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1368">Calls to `LinkGenerator` or `IUrlHelper` that return `null` are usually caused by not understanding route value invalidation.</span></span> <span data-ttu-id="6d72c-1369">显式指定更多路由值，对路由值失效进行故障排除，以查看是否解决了问题。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1369">Troubleshoot route value invalidation by explicitly specifying more of the route values to see if that solves the problem.</span></span>

<span data-ttu-id="6d72c-1370">路由值失效的前提是应用的 URL 方案是分层的，并按从左到右的顺序组成层次结构。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1370">Route value invalidation works on the assumption that the app's URL scheme is hierarchical, with a hierarchy formed from left-to-right.</span></span> <span data-ttu-id="6d72c-1371">请考虑基本控制器路由模板 `{controller}/{action}/{id?}`，以直观地了解实践操作中该模板的工作方式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1371">Consider the basic controller route template `{controller}/{action}/{id?}` to get an intuitive sense of how this works in practice.</span></span> <span data-ttu-id="6d72c-1372">对值进行更改会使右侧显示的所有路由值都失效 。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1372">A **change** to a value **invalidates** all of the route values that appear to the right.</span></span> <span data-ttu-id="6d72c-1373">这反映了关于层次结构的假设。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1373">This reflects the assumption about hierarchy.</span></span> <span data-ttu-id="6d72c-1374">如果应用的 `id` 有一个环境值，则操作会为 `controller` 指定一个不同的值：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1374">If the app has an ambient value for `id`, and the operation specifies a different value for the `controller`:</span></span>

* <span data-ttu-id="6d72c-1375">`id` 不会重复使用，因为 `{controller}` 在 `{id?}` 的左侧。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1375">`id` won't be reused because `{controller}` is to the left of `{id?}`.</span></span>

<span data-ttu-id="6d72c-1376">演示此原则的一些示例如下：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1376">Some examples demonstrating this principle:</span></span>

* <span data-ttu-id="6d72c-1377">如果显式值包含 `id` 的值，则将忽略 `id` 的环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1377">If the explicit values contain a value for `id`, the ambient value for `id` is ignored.</span></span> <span data-ttu-id="6d72c-1378">可以使用 `controller` 和 `action` 的环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1378">The ambient values for `controller` and `action` can be used.</span></span>
* <span data-ttu-id="6d72c-1379">如果显式值包含 `action` 的值，则将忽略 `action` 的任何环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1379">If the explicit values contain a value for `action`, any ambient value for `action` is ignored.</span></span> <span data-ttu-id="6d72c-1380">可以使用 `controller` 的环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1380">The ambient values for `controller` can be used.</span></span> <span data-ttu-id="6d72c-1381">如果 `action` 的显式值不同于 `action` 的环境值，则不会使用 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1381">If the explicit value for `action` is different from the ambient value for `action`, the `id` value won't be used.</span></span>  <span data-ttu-id="6d72c-1382">如果 `action` 的显式值与 `action` 的环境值相同，则可以使用 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1382">If the explicit value for `action` is the same as the ambient value for `action`, the `id` value can be used.</span></span>
* <span data-ttu-id="6d72c-1383">如果显式值包含 `controller` 的值，则将忽略 `controller` 的任何环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1383">If the explicit values contain a value for `controller`, any ambient value for `controller` is ignored.</span></span> <span data-ttu-id="6d72c-1384">如果 `controller` 的显式值不同于 `controller` 的环境值，则不会使用 `action` 和 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1384">If the explicit value for `controller` is different from the ambient value for `controller`, the `action` and `id` values won't be used.</span></span> <span data-ttu-id="6d72c-1385">如果 `controller` 的显式值与 `controller` 的环境值相同，则可以使用 `action` 和 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1385">If the explicit value for `controller` is the same as the ambient value for `controller`, the `action` and `id` values can be used.</span></span>

<span data-ttu-id="6d72c-1386">由于存在特性路由和专用传统路由，此过程将变得更加复杂。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1386">This process is further complicated by the existence of attribute routes and dedicated conventional routes.</span></span> <span data-ttu-id="6d72c-1387">控制器传统路由（例如 `{controller}/{action}/{id?}`）使用路由参数指定层次结构。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1387">Controller conventional routes such as `{controller}/{action}/{id?}` specify a hierarchy using route parameters.</span></span> <span data-ttu-id="6d72c-1388">对于控制器和 Razor Pages 的[专用传统路由](xref:mvc/controllers/routing#dcr)和[特性路由](xref:mvc/controllers/routing#ar)：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1388">For [dedicated conventional routes](xref:mvc/controllers/routing#dcr) and [attribute routes](xref:mvc/controllers/routing#ar) to controllers and Razor Pages:</span></span>

* <span data-ttu-id="6d72c-1389">有一个路由值层次结构。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1389">There is a hierarchy of route values.</span></span>
* <span data-ttu-id="6d72c-1390">它们不会出现在模板中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1390">They don't appear in the template.</span></span>

<span data-ttu-id="6d72c-1391">对于这些情况，URL 生成将定义“所需值”这一概念。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1391">For these cases, URL generation defines the **required values** concept.</span></span> <span data-ttu-id="6d72c-1392">而由控制器和 Razor Pages 创建的终结点将指定所需值，以允许路由值失效起作用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1392">Endpoints created by controllers and Razor Pages have required values specified that allow route value invalidation to work.</span></span>

<span data-ttu-id="6d72c-1393">路由值失效算法的详细信息：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1393">The route value invalidation algorithm in detail:</span></span>

* <span data-ttu-id="6d72c-1394">所需值名称与路由参数组合在一起，然后从左到右进行处理。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1394">The required value names are combined with the route parameters, then processed from left-to-right.</span></span>
* <span data-ttu-id="6d72c-1395">对于每个参数，将比较环境值和显式值：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1395">For each parameter, the ambient value and explicit value are compared:</span></span>
    * <span data-ttu-id="6d72c-1396">如果环境值和显式值相同，则该过程将继续。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1396">If the ambient value and explicit value are the same, the process continues.</span></span>
    * <span data-ttu-id="6d72c-1397">如果环境值存在而显式值不存在，则在生成 URL 时使用环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1397">If the ambient value is present and the explicit value isn't, the ambient value is used when generating the URL.</span></span>
    * <span data-ttu-id="6d72c-1398">如果环境值不存在而显式值存在，则拒绝环境值和所有后续环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1398">If the ambient value isn't present and the explicit value is, reject the ambient value and all subsequent ambient values.</span></span>
    * <span data-ttu-id="6d72c-1399">如果环境值和显式值均存在，并且这两个值不同，则拒绝环境值和所有后续环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1399">If the ambient value and the explicit value are present, and the two values are different, reject the ambient value and all subsequent ambient values.</span></span>

<span data-ttu-id="6d72c-1400">此时，URL 生成操作就可以计算路由约束了。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1400">At this point, the URL generation operation is ready to evaluate route constraints.</span></span> <span data-ttu-id="6d72c-1401">接受的值集与提供给约束的参数默认值相结合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1401">The set of accepted values is combined with the parameter default values, which is provided to constraints.</span></span> <span data-ttu-id="6d72c-1402">如果所有约束都通过，则操作将继续。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1402">If the constraints all pass, the operation continues.</span></span>

<span data-ttu-id="6d72c-1403">接下来，接受的值可用于扩展路由模板。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1403">Next, the **accepted values** can be used to expand the route template.</span></span> <span data-ttu-id="6d72c-1404">处理路由模板：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1404">The route template is processed:</span></span>

* <span data-ttu-id="6d72c-1405">从左到右。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1405">From left-to-right.</span></span>
* <span data-ttu-id="6d72c-1406">每个参数都替换了接受的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1406">Each parameter has its accepted value substituted.</span></span>
* <span data-ttu-id="6d72c-1407">具有以下特殊情况：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1407">With the following special cases:</span></span>
  * <span data-ttu-id="6d72c-1408">如果接受的值缺少一个值并且参数具有默认值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1408">If the accepted values is missing a value and the parameter has a default value, the default value is used.</span></span>
  * <span data-ttu-id="6d72c-1409">如果接受的值缺少一个值并且参数是可选的，则继续处理。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1409">If the accepted values is missing a value and the parameter is optional, processing continues.</span></span>
  * <span data-ttu-id="6d72c-1410">如果缺少的可选参数右侧的任何路由参数都具有值，则操作将失败。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1410">If any route parameter to the right of a missing optional parameter has a value, the operation fails.</span></span>
  * <!-- review default-valued parameters optional parameters --> <span data-ttu-id="6d72c-1411">如果可能，连续的默认值参数和可选参数会折叠。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1411">Contiguous default-valued parameters and optional parameters are collapsed where possible.</span></span>

<span data-ttu-id="6d72c-1412">显式提供且与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1412">Values explicitly provided that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="6d72c-1413">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1413">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="6d72c-1414">环境值</span><span class="sxs-lookup"><span data-stu-id="6d72c-1414">Ambient Values</span></span>                     | <span data-ttu-id="6d72c-1415">显式值</span><span class="sxs-lookup"><span data-stu-id="6d72c-1415">Explicit Values</span></span>                        | <span data-ttu-id="6d72c-1416">结果</span><span class="sxs-lookup"><span data-stu-id="6d72c-1416">Result</span></span>                  |
| ---
<span data-ttu-id="6d72c-1417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1418">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1419">'Identity'</span></span>
- <span data-ttu-id="6d72c-1420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1420">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1421">'Razor'</span></span>
- <span data-ttu-id="6d72c-1422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1424">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1425">'Identity'</span></span>
- <span data-ttu-id="6d72c-1426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1426">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1427">'Razor'</span></span>
- <span data-ttu-id="6d72c-1428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1430">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1431">'Identity'</span></span>
- <span data-ttu-id="6d72c-1432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1432">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1433">'Razor'</span></span>
- <span data-ttu-id="6d72c-1434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1436">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1437">'Identity'</span></span>
- <span data-ttu-id="6d72c-1438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1438">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1439">'Razor'</span></span>
- <span data-ttu-id="6d72c-1440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1442">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1443">'Identity'</span></span>
- <span data-ttu-id="6d72c-1444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1444">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1445">'Razor'</span></span>
- <span data-ttu-id="6d72c-1446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1448">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1449">'Identity'</span></span>
- <span data-ttu-id="6d72c-1450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1450">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1451">'Razor'</span></span>
- <span data-ttu-id="6d72c-1452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1454">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1455">'Identity'</span></span>
- <span data-ttu-id="6d72c-1456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1456">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1457">'Razor'</span></span>
- <span data-ttu-id="6d72c-1458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1460">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1461">'Identity'</span></span>
- <span data-ttu-id="6d72c-1462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1462">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1463">'Razor'</span></span>
- <span data-ttu-id="6d72c-1464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1466">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1467">'Identity'</span></span>
- <span data-ttu-id="6d72c-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1469">'Razor'</span></span>
- <span data-ttu-id="6d72c-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1472">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1473">'Identity'</span></span>
- <span data-ttu-id="6d72c-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1475">'Razor'</span></span>
- <span data-ttu-id="6d72c-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1478">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1479">'Identity'</span></span>
- <span data-ttu-id="6d72c-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1481">'Razor'</span></span>
- <span data-ttu-id="6d72c-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1484">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1485">'Identity'</span></span>
- <span data-ttu-id="6d72c-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1487">'Razor'</span></span>
- <span data-ttu-id="6d72c-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1490">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1491">'Identity'</span></span>
- <span data-ttu-id="6d72c-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1493">'Razor'</span></span>
- <span data-ttu-id="6d72c-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1496">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1497">'Identity'</span></span>
- <span data-ttu-id="6d72c-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1499">'Razor'</span></span>
- <span data-ttu-id="6d72c-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1502">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1503">'Identity'</span></span>
- <span data-ttu-id="6d72c-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1505">'Razor'</span></span>
- <span data-ttu-id="6d72c-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1506">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1507">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1507">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1508">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1509">'Identity'</span></span>
- <span data-ttu-id="6d72c-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1511">'Razor'</span></span>
- <span data-ttu-id="6d72c-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1514">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1515">'Identity'</span></span>
- <span data-ttu-id="6d72c-1516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1516">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1517">'Razor'</span></span>
- <span data-ttu-id="6d72c-1518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1520">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1521">'Identity'</span></span>
- <span data-ttu-id="6d72c-1522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1522">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1523">'Razor'</span></span>
- <span data-ttu-id="6d72c-1524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1526">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1527">'Identity'</span></span>
- <span data-ttu-id="6d72c-1528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1528">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1529">'Razor'</span></span>
- <span data-ttu-id="6d72c-1530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1532">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1533">'Identity'</span></span>
- <span data-ttu-id="6d72c-1534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1534">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1535">'Razor'</span></span>
- <span data-ttu-id="6d72c-1536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1538">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1539">'Identity'</span></span>
- <span data-ttu-id="6d72c-1540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1540">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1541">'Razor'</span></span>
- <span data-ttu-id="6d72c-1542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1544">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1545">'Identity'</span></span>
- <span data-ttu-id="6d72c-1546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1546">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1547">'Razor'</span></span>
- <span data-ttu-id="6d72c-1548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1550">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1551">'Identity'</span></span>
- <span data-ttu-id="6d72c-1552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1552">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1553">'Razor'</span></span>
- <span data-ttu-id="6d72c-1554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1556">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1557">'Identity'</span></span>
- <span data-ttu-id="6d72c-1558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1558">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1559">'Razor'</span></span>
- <span data-ttu-id="6d72c-1560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1562">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1563">'Identity'</span></span>
- <span data-ttu-id="6d72c-1564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1564">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1565">'Razor'</span></span>
- <span data-ttu-id="6d72c-1566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1568">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1569">'Identity'</span></span>
- <span data-ttu-id="6d72c-1570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1570">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1571">'Razor'</span></span>
- <span data-ttu-id="6d72c-1572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1574">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1575">'Identity'</span></span>
- <span data-ttu-id="6d72c-1576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1576">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1577">'Razor'</span></span>
- <span data-ttu-id="6d72c-1578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1580">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1581">'Identity'</span></span>
- <span data-ttu-id="6d72c-1582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1582">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1583">'Razor'</span></span>
- <span data-ttu-id="6d72c-1584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1586">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1587">'Identity'</span></span>
- <span data-ttu-id="6d72c-1588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1588">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1589">'Razor'</span></span>
- <span data-ttu-id="6d72c-1590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1592">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1593">'Identity'</span></span>
- <span data-ttu-id="6d72c-1594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1594">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1595">'Razor'</span></span>
- <span data-ttu-id="6d72c-1596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1598">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1599">'Identity'</span></span>
- <span data-ttu-id="6d72c-1600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1600">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1601">'Razor'</span></span>
- <span data-ttu-id="6d72c-1602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1604">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1605">'Identity'</span></span>
- <span data-ttu-id="6d72c-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1607">'Razor'</span></span>
- <span data-ttu-id="6d72c-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1608">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1609">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1609">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1610">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1611">'Identity'</span></span>
- <span data-ttu-id="6d72c-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1613">'Razor'</span></span>
- <span data-ttu-id="6d72c-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1616">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1617">'Identity'</span></span>
- <span data-ttu-id="6d72c-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1619">'Razor'</span></span>
- <span data-ttu-id="6d72c-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1622">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1623">'Identity'</span></span>
- <span data-ttu-id="6d72c-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1625">'Razor'</span></span>
- <span data-ttu-id="6d72c-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1628">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1629">'Identity'</span></span>
- <span data-ttu-id="6d72c-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1631">'Razor'</span></span>
- <span data-ttu-id="6d72c-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1634">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1635">'Identity'</span></span>
- <span data-ttu-id="6d72c-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1637">'Razor'</span></span>
- <span data-ttu-id="6d72c-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1640">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1641">'Identity'</span></span>
- <span data-ttu-id="6d72c-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1643">'Razor'</span></span>
- <span data-ttu-id="6d72c-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1646">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1647">'Identity'</span></span>
- <span data-ttu-id="6d72c-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1649">'Razor'</span></span>
- <span data-ttu-id="6d72c-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1652">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1653">'Identity'</span></span>
- <span data-ttu-id="6d72c-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1655">'Razor'</span></span>
- <span data-ttu-id="6d72c-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1658">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1659">'Identity'</span></span>
- <span data-ttu-id="6d72c-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1661">'Razor'</span></span>
- <span data-ttu-id="6d72c-1662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1663">------------ | | 控制器 =“Home”                | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 控制器 =“Order”，操作 =“About” | `/Order/About`          |
| 控制器 = “Home”，颜色 = “Red” | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 操作 =“About”，颜色 =“Red”        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="6d72c-1663">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

### <a name="problems-with-route-value-invalidation"></a><span data-ttu-id="6d72c-1664">路由值失效的问题</span><span class="sxs-lookup"><span data-stu-id="6d72c-1664">Problems with route value invalidation</span></span>

<span data-ttu-id="6d72c-1665">从 ASP.NET Core 3.0 开始，早期 ASP.NET Core 版本中使用的一些 URL 生成方案不适用于 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1665">As of ASP.NET Core 3.0, some URL generation schemes used in earlier ASP.NET Core versions don't work well with URL generation.</span></span> <span data-ttu-id="6d72c-1666">ASP.NET Core 团队计划在未来的版本中添加功能来满足这些需求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1666">The ASP.NET Core team plans to add features to address these needs in a future release.</span></span> <span data-ttu-id="6d72c-1667">现在，最佳解决方案是使用旧路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1667">For now the best solution is to use legacy routing.</span></span>

<span data-ttu-id="6d72c-1668">下面的代码显示了路由不支持的 URL 生成方案示例。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1668">The following code shows an example of a URL generation scheme that's not supported by routing.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

<span data-ttu-id="6d72c-1669">在前面的代码中，`culture` 路由参数用于本地化。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1669">In the preceding code, the `culture` route parameter is used for localization.</span></span> <span data-ttu-id="6d72c-1670">需要将 `culture` 参数始终作为环境值接受。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1670">The desire is to have the `culture` parameter always accepted as an ambient value.</span></span> <span data-ttu-id="6d72c-1671">但由于所需值的工作方式，不会将 `culture` 参数作为环境值接受：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1671">However, the `culture` parameter is not accepted as an ambient value because of the way required values work:</span></span>

* <span data-ttu-id="6d72c-1672">在 `"default"` 路由模板中，`culture` 路由参数位于 `controller` 的左侧，因此对 `controller` 的更改不会使 `culture` 失效。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1672">In the `"default"` route template, the `culture` route parameter is to the left of `controller`, so changes to `controller` won't invalidate `culture`.</span></span>
* <span data-ttu-id="6d72c-1673">在 `"blog"` 路由模板中，`culture` 路由参数应在 `controller` 的右侧，这会显示在所需值中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1673">In the `"blog"` route template, the `culture` route parameter is considered to be to the right of `controller`, which appears in the required values.</span></span>

## <a name="configuring-endpoint-metadata"></a><span data-ttu-id="6d72c-1674">配置终结点元数据</span><span class="sxs-lookup"><span data-stu-id="6d72c-1674">Configuring endpoint metadata</span></span>

<span data-ttu-id="6d72c-1675">以下链接提供有关配置终结点元数据的信息：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1675">The following links provide information on configuring endpoint metadata:</span></span>

* [<span data-ttu-id="6d72c-1676">通过终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="6d72c-1676">Enable Cors with endpoint routing</span></span>](xref:security/cors#enable-cors-with-endpoint-routing)
* <span data-ttu-id="6d72c-1677">使用自定义 `[MinimumAgeAuthorize]` 属性的 [IAuthorizationPolicyProvider 示例](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)</span><span class="sxs-lookup"><span data-stu-id="6d72c-1677">[IAuthorizationPolicyProvider sample](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) using a custom `[MinimumAgeAuthorize]` attribute</span></span>
* <span data-ttu-id="6d72c-1678">[使用 [Authorize] 属性测试身份验证](xref:security/authentication/identity#test-identity)</span><span class="sxs-lookup"><span data-stu-id="6d72c-1678">[Test authentication with the [Authorize] attribute](xref:security/authentication/identity#test-identity)</span></span>
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* <span data-ttu-id="6d72c-1679">[使用 [Authorize] 属性选择方案](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="6d72c-1679">[Selecting the scheme with the [Authorize] attribute](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span></span>
* <span data-ttu-id="6d72c-1680">[使用 [Authorize] 属性应用策略](xref:security/authorization/policies#applying-policies-to-mvc-controllers)</span><span class="sxs-lookup"><span data-stu-id="6d72c-1680">[Applying policies using the [Authorize] attribute](xref:security/authorization/policies#applying-policies-to-mvc-controllers)</span></span>
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a><span data-ttu-id="6d72c-1681">路由中与 RequireHost 匹配的主机</span><span class="sxs-lookup"><span data-stu-id="6d72c-1681">Host matching in routes with RequireHost</span></span>

<span data-ttu-id="6d72c-1682"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> 将约束应用于需要指定主机的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1682"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> applies a constraint to the route which requires the specified host.</span></span> <span data-ttu-id="6d72c-1683">`RequireHost` 或 [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) 参数可以是：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1683">The `RequireHost` or [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) parameter can be:</span></span>

* <span data-ttu-id="6d72c-1684">主机：`www.domain.com`匹配任何端口的 `www.domain.com`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1684">Host: `www.domain.com`, matches `www.domain.com` with any port.</span></span>
* <span data-ttu-id="6d72c-1685">带有通配符的主机：`*.domain.com`，匹配任何端口上的 `www.domain.com`、`subdomain.domain.com` 或 `www.subdomain.domain.com`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1685">Host with wildcard: `*.domain.com`, matches `www.domain.com`, `subdomain.domain.com`, or `www.subdomain.domain.com` on any port.</span></span>
* <span data-ttu-id="6d72c-1686">端口：`*:5000` 匹配任何主机的端口 5000。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1686">Port: `*:5000`, matches port 5000 with any host.</span></span>
* <span data-ttu-id="6d72c-1687">主机和端口：`www.domain.com:5000` 或 `*.domain.com:5000`（匹配主机和端口）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1687">Host and port: `www.domain.com:5000` or `*.domain.com:5000`, matches host and port.</span></span>

<span data-ttu-id="6d72c-1688">可以使用 `RequireHost` 或 `[Host]` 指定多个参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1688">Multiple parameters can be specified using `RequireHost` or `[Host]`.</span></span> <span data-ttu-id="6d72c-1689">约束匹配对任何参数均有效的主机。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1689">The constraint  matches hosts valid for any of the parameters.</span></span> <span data-ttu-id="6d72c-1690">例如，`[Host("domain.com", "*.domain.com")]` 匹配 `domain.com`、`www.domain.com` 和 `subdomain.domain.com`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1690">For example, `[Host("domain.com", "*.domain.com")]` matches `domain.com`, `www.domain.com`, and `subdomain.domain.com`.</span></span>

<span data-ttu-id="6d72c-1691">以下代码使用 `RequireHost` 来要求路由上的指定主机：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1691">The following code uses `RequireHost` to require the specified host on the route:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

<span data-ttu-id="6d72c-1692">以下代码使用控制器上的 `[Host]` 特性来要求任何指定的主机：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1692">The following code uses the `[Host]` attribute on the controller to require any of the specified hosts:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

<span data-ttu-id="6d72c-1693">当 `[Host]` 属性同时应用于控制器和操作方法时：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1693">When the `[Host]` attribute is applied to both the controller and action method:</span></span>

* <span data-ttu-id="6d72c-1694">使用操作上的属性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1694">The attribute on the action is used.</span></span>
* <span data-ttu-id="6d72c-1695">忽略控制器属性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1695">The controller attribute is ignored.</span></span>

## <a name="performance-guidance-for-routing"></a><span data-ttu-id="6d72c-1696">路由的性能指南</span><span class="sxs-lookup"><span data-stu-id="6d72c-1696">Performance guidance for routing</span></span>

<span data-ttu-id="6d72c-1697">大多数路由在 ASP.NET Core 3.0 中都进行了更新，以提高性能。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1697">Most of routing was updated in ASP.NET Core 3.0 to increase performance.</span></span>

<span data-ttu-id="6d72c-1698">当应用出现性能问题时，人们常常会怀疑路由是问题所在。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1698">When an app has performance problems, routing is often suspected as the problem.</span></span> <span data-ttu-id="6d72c-1699">怀疑路由的原因是，控制器和 Razor Pages 等框架在其日志记录消息中报告框架内所用的时间。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1699">The reason routing is suspected is that frameworks like controllers and Razor Pages report the amount of time spent inside the framework in their logging messages.</span></span> <span data-ttu-id="6d72c-1700">如果控制器报告的时间与请求的总时间之间存在明显的差异：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1700">When there's a significant difference between the time reported by controllers and the total time of the request:</span></span>

* <span data-ttu-id="6d72c-1701">开发者会将应用代码排除在问题根源之外。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1701">Developers eliminate their app code as the source of the problem.</span></span>
* <span data-ttu-id="6d72c-1702">他们通常认为路由是问题的原因。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1702">It's common to assume routing is the cause.</span></span>

<span data-ttu-id="6d72c-1703">路由的性能使用数千个终结点进行测试。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1703">Routing is performance tested using thousands of endpoints.</span></span> <span data-ttu-id="6d72c-1704">典型的应用不太可能仅仅因为太大而遇到性能问题。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1704">It's unlikely that a typical app will encounter a performance problem just by being too large.</span></span> <span data-ttu-id="6d72c-1705">路由性能缓慢的最常见根本原因通常在于性能不佳的自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1705">The most common root cause of slow routing performance is usually a badly-behaving custom middleware.</span></span>

<span data-ttu-id="6d72c-1706">下面的代码示例演示了一种用于缩小延迟源的基本方法：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1706">This following code sample demonstrates a basic technique for narrowing down the source of delay:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

<span data-ttu-id="6d72c-1707">时间路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1707">To time routing:</span></span>

* <span data-ttu-id="6d72c-1708">使用前面代码中所示的计时中间件的副本交错执行每个中间件。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1708">Interleave each middleware with a copy of the timing middleware shown in the preceding code.</span></span>
* <span data-ttu-id="6d72c-1709">添加唯一标识符，以便将计时数据与代码相关联。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1709">Add a unique identifier to correlate the timing data with the code.</span></span>

<span data-ttu-id="6d72c-1710">这是一种可在延迟显著的情况下减少延迟的基本方法，例如超过 `10ms`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1710">This is a basic way to narrow down the delay when it's significant, for example, more than `10ms`.</span></span>  <span data-ttu-id="6d72c-1711">从 `Time 1` 中减去 `Time 2` 会报告 `UseRouting` 中间件内所用的时间。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1711">Subtracting `Time 2` from `Time 1` reports the time spent inside the `UseRouting` middleware.</span></span>

<span data-ttu-id="6d72c-1712">下面的代码使用一种更紧凑的方法来处理前面的计时代码：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1712">The following code uses a more compact approach to the preceding timing code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a><span data-ttu-id="6d72c-1713">可能比较昂贵的路由功能</span><span class="sxs-lookup"><span data-stu-id="6d72c-1713">Potentially expensive routing features</span></span>

<span data-ttu-id="6d72c-1714">下面的列表提供了一些路由功能，这些功能相对于基本路由模板来说比较昂贵：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1714">The following list provides some insight into routing features that are relatively expensive compared with basic route templates:</span></span>

* <span data-ttu-id="6d72c-1715">正则表达式：可以编写复杂的正则表达式，或具有少量输入的长时间运行时间。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1715">Regular expressions: It's possible to write regular expressions that are complex, or have long running time with a small amount of input.</span></span>

* <span data-ttu-id="6d72c-1716">复杂段 (`{x}-{y}-{z}`)：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1716">Complex segments (`{x}-{y}-{z}`):</span></span> 
  * <span data-ttu-id="6d72c-1717">比分析常规 URL 路径段贵得多。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1717">Are significantly more expensive than parsing a regular URL path segment.</span></span>
  * <span data-ttu-id="6d72c-1718">导致更多的子字符串被分配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1718">Result in many more substrings being allocated.</span></span>
  * <span data-ttu-id="6d72c-1719">ASP.NET Core 3.0 路由性能更新中未更新的复杂段逻辑。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1719">The complex segment logic was not updated in ASP.NET Core 3.0 routing performance update.</span></span>

* <span data-ttu-id="6d72c-1720">同步数据访问：许多复杂应用都将数据库访问作为其路由的一部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1720">Synchronous data access: Many complex apps have database access as part of their routing.</span></span> <span data-ttu-id="6d72c-1721">ASP.NET Core 2.2 及更低版本的路由可能未提供适当的扩展点，因此无法支持数据库访问路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1721">ASP.NET Core 2.2 and earlier routing might not provide the right extensibility points to support database access routing.</span></span> <span data-ttu-id="6d72c-1722">例如，<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 和 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 是同步的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1722">For example, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, and <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are synchronous.</span></span> <span data-ttu-id="6d72c-1723">扩展点（如 <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> 和 <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext>）是异步的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1723">Extensibility points such as <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> and <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> are asynchronous.</span></span>

## <a name="guidance-for-library-authors"></a><span data-ttu-id="6d72c-1724">库创建者指南</span><span class="sxs-lookup"><span data-stu-id="6d72c-1724">Guidance for library authors</span></span>

<span data-ttu-id="6d72c-1725">本部分包含基于路由构建的库创建者指南。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1725">This section contains guidance for library authors building on top of routing.</span></span> <span data-ttu-id="6d72c-1726">这些详细信息旨在确保应用开发者可以在使用扩展路由的库和框架时具有良好的体验。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1726">These details are intended to ensure that app developers have a good experience using libraries and frameworks that extend routing.</span></span>

### <a name="define-endpoints"></a><span data-ttu-id="6d72c-1727">定义终结点</span><span class="sxs-lookup"><span data-stu-id="6d72c-1727">Define endpoints</span></span>

<span data-ttu-id="6d72c-1728">若要创建一个使用路由进行 URL 匹配的框架，请先定义在 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 之上进行构建的用户体验。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1728">To create a framework that uses routing for URL matching, start by defining a user experience that builds on top of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>

<span data-ttu-id="6d72c-1729">在 <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> 之上进行构建。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1729">**DO** build on top of <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span> <span data-ttu-id="6d72c-1730">由此，用户就可以用其他 ASP.NET Core 功能编写框架，而不会造成混淆。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1730">This allows users to compose your framework with other ASP.NET Core features without confusion.</span></span> <span data-ttu-id="6d72c-1731">每个 ASP.NET Core 模板都包含路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1731">Every ASP.NET Core template includes routing.</span></span> <span data-ttu-id="6d72c-1732">假定路由存在并且用户熟悉路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1732">Assume routing is present and familiar for users.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="6d72c-1733">从对实现 <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> 的 `MapMyFramework(...)` 的调用返回密式具体类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1733">**DO** return a sealed concrete type from a call to `MapMyFramework(...)` that implements <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>.</span></span> <span data-ttu-id="6d72c-1734">大多数框架 `Map...` 方法都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1734">Most framework `Map...` methods follow this pattern.</span></span> <span data-ttu-id="6d72c-1735">`IEndpointConventionBuilder` 接口：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1735">The `IEndpointConventionBuilder` interface:</span></span>

* <span data-ttu-id="6d72c-1736">允许元数据的可组合性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1736">Allows composability of metadata.</span></span>
* <span data-ttu-id="6d72c-1737">面向多种扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1737">Is targeted by a variety of extension methods.</span></span>

<span data-ttu-id="6d72c-1738">通过声明自己的类型，你可以将自己的框架特定功能添加到生成器中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1738">Declaring your own type allows you to add your own framework-specific functionality to the builder.</span></span> <span data-ttu-id="6d72c-1739">可以包装一个框架声明的生成器并向其转发调用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1739">It's ok to wrap a framework-declared builder and forward calls to it.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="6d72c-1740">请考虑编写自己的 <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1740">**CONSIDER** writing your own <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>.</span></span> <span data-ttu-id="6d72c-1741">`EndpointDataSource` 是用于声明和更新终结点集合的低级别基元。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1741">`EndpointDataSource` is the low-level primitive for declaring and updating a collection of endpoints.</span></span> <span data-ttu-id="6d72c-1742">`EndpointDataSource` 是控制器和 Razor Pages 使用的强大 API。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1742">`EndpointDataSource` is a powerful API used by controllers and Razor Pages.</span></span>

<span data-ttu-id="6d72c-1743">路由测试具有非更新数据源的[基本示例](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1743">The routing tests have a [basic example](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) of a non-updating data source.</span></span>

<span data-ttu-id="6d72c-1744">默认情况下，请不要尝试注册 `EndpointDataSource`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1744">**DO NOT** attempt to register an `EndpointDataSource` by default.</span></span> <span data-ttu-id="6d72c-1745">要求用户在 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 中注册你的框架。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1745">Require users to register your framework in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span> <span data-ttu-id="6d72c-1746">路由的理念是，默认情况下不包含任何内容，并且 `UseEndpoints` 是注册终结点的位置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1746">The philosophy of routing is that nothing is included by default, and that `UseEndpoints` is the place to register endpoints.</span></span>

### <a name="creating-routing-integrated-middleware"></a><span data-ttu-id="6d72c-1747">创建路由集成式中间件</span><span class="sxs-lookup"><span data-stu-id="6d72c-1747">Creating routing-integrated middleware</span></span>

<span data-ttu-id="6d72c-1748">请考虑将元数据类型定义为接口。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1748">**CONSIDER** defining metadata types as an interface.</span></span>

<span data-ttu-id="6d72c-1749">请实现在类和方法上使用元数据类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1749">**DO** make it possible to use metadata types as an attribute on classes and methods.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

<span data-ttu-id="6d72c-1750">控制器和 Razor Pages 等框架支持将元数据特性应用到类型和方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1750">Frameworks like controllers and Razor Pages support applying metadata attributes to types and methods.</span></span> <span data-ttu-id="6d72c-1751">如果声明元数据类型：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1751">If you declare metadata types:</span></span>

* <span data-ttu-id="6d72c-1752">将它们作为[特性](/dotnet/csharp/programming-guide/concepts/attributes/)进行访问。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1752">Make them accessible as [attributes](/dotnet/csharp/programming-guide/concepts/attributes/).</span></span>
* <span data-ttu-id="6d72c-1753">大多数用户都熟悉应用特性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1753">Most users are familiar with applying attributes.</span></span>

<span data-ttu-id="6d72c-1754">将元数据类型声明为接口可增加另一层灵活性：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1754">Declaring a metadata type as an interface adds another layer of flexibility:</span></span>

* <span data-ttu-id="6d72c-1755">接口是可组合的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1755">Interfaces are composable.</span></span>
* <span data-ttu-id="6d72c-1756">开发者可以声明自己的且组合多个策略的类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1756">Developers can declare their own types that combine multiple policies.</span></span>

<span data-ttu-id="6d72c-1757">请实现元数据替代，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1757">**DO** make it possible to override metadata, as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

<span data-ttu-id="6d72c-1758">遵循这些准则的最佳方式是避免定义标记元数据：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1758">The best way to follow these guidelines is to avoid defining **marker metadata**:</span></span>

* <span data-ttu-id="6d72c-1759">不要只是查找元数据类型的状态。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1759">Don't just look for the presence of a metadata type.</span></span>
* <span data-ttu-id="6d72c-1760">定义元数据的属性并检查该属性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1760">Define a property on the metadata and check the property.</span></span>

<span data-ttu-id="6d72c-1761">对元数据集合进行排序，并支持按优先级替代。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1761">The metadata collection is ordered and supports overriding by priority.</span></span> <span data-ttu-id="6d72c-1762">对于控制器，操作方法上的元数据是最具体的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1762">In the case of controllers, metadata on the action method is most specific.</span></span>

<span data-ttu-id="6d72c-1763">使中间件始终有用，不管有没有路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1763">**DO** make middleware useful with and without routing.</span></span>

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

<span data-ttu-id="6d72c-1764">作为此准则的一个示例，请考虑 `UseAuthorization` 中间件。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1764">As an example of this guideline, consider the `UseAuthorization` middleware.</span></span> <span data-ttu-id="6d72c-1765">授权中间件允许传入回退策略。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1765">The authorization middleware allows you to pass in a fallback policy.</span></span> <!-- shown where?  (shown here) --> <span data-ttu-id="6d72c-1766">如果指定了回退策略，则适用于以下两者：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1766">The fallback policy, if specified, applies to both:</span></span>

* <span data-ttu-id="6d72c-1767">无指定策略的终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1767">Endpoints without a specified policy.</span></span>
* <span data-ttu-id="6d72c-1768">与终结点不匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1768">Requests that don't match an endpoint.</span></span>

<span data-ttu-id="6d72c-1769">这使得授权中间件在路由上下文之外很有用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1769">This makes the authorization middleware useful outside of the context of routing.</span></span> <span data-ttu-id="6d72c-1770">授权中间件可用于传统中间件的编程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1770">The authorization middleware can be used for traditional middleware programming.</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6d72c-1771">路由负责将请求 URI 映射到终结点并向这些终结点调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1771">Routing is responsible for mapping request URIs to endpoints and dispatching incoming requests to those endpoints.</span></span> <span data-ttu-id="6d72c-1772">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1772">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="6d72c-1773">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1773">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="6d72c-1774">通过使用应用中的路由信息，路由还能生成映射到终结点的 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1774">Using route information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="6d72c-1775">要在 ASP.NET Core 2.2 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1775">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="6d72c-1776"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 选项确定路由是应在内部使用 ASP.NET Core 2.1 或更早版本的基于终结点的逻辑还是使用其基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的逻辑。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1776">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="6d72c-1777">兼容性版本设置为 2.2 或更高版本时，默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1777">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="6d72c-1778">将值设置为 `false` 以使用先前的路由逻辑：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1778">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="6d72c-1779">有关基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由的详细信息，请参阅本主题的 [ASP.NET Core 2.1 版本](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1779">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6d72c-1780">本文档介绍较低级别的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1780">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="6d72c-1781">有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1781">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="6d72c-1782">有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1782">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="6d72c-1783">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6d72c-1783">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="6d72c-1784">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="6d72c-1784">Routing basics</span></span>

<span data-ttu-id="6d72c-1785">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1785">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="6d72c-1786">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1786">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="6d72c-1787">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1787">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="6d72c-1788">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1788">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="6d72c-1789">开发者通常在专业情况下使用[特性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1789">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span> <span data-ttu-id="6d72c-1790">专用情况示例包括：博客和电子商务终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1790">Specialized situations examples include, blog and ecommerce endpoints.</span></span>

<span data-ttu-id="6d72c-1791">Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1791">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="6d72c-1792">也就是说，对同一逻辑资源执行的许多操作（例如，GET 和 POST）都使用相同 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1792">This means that many operations, for example, GET, and POST, on the same logical resource use the same URL.</span></span> <span data-ttu-id="6d72c-1793">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1793">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

Razor<span data-ttu-id="6d72c-1794"> Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1794"> Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="6d72c-1795">还可以使用其他约定来自定义 Razor Pages 路由行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1795">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="6d72c-1796">有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1796">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="6d72c-1797">借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1797">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="6d72c-1798">此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1798">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="6d72c-1799">路由使用“终结点”(`Endpoint`) 来表示应用中的逻辑终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1799">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="6d72c-1800">终结点定义用于处理请求的委托和任意元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1800">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="6d72c-1801">元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1801">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="6d72c-1802">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1802">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="6d72c-1803">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1803">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="6d72c-1804">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1804">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="6d72c-1805"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1805"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="6d72c-1806">应用模型（如 MVC/Razor Pages）注册其所有终结点，这些终结点具有可预测的路由方案实现。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1806">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="6d72c-1807">路由实现会在中间件管道中任何所需位置制定路由决策。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1807">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="6d72c-1808">路由中间件之后出现的中间件可以检查路由中间件针对给定请求 URI 的终结点决策结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1808">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="6d72c-1809">可以在中间件管道中的任何位置枚举应用中的所有终结点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1809">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="6d72c-1810">应用可根据终结点信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1810">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="6d72c-1811">URL 生成是基于支持任意可扩展性的地址：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1811">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="6d72c-1812">可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在任意位置解析链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) 以生成 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1812">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="6d72c-1813">如果无法通过 DI 获得链接生成器 API，则 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 会提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1813">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="6d72c-1814">在 ASP.NET Core 2.2 中发布终结点路由后，终结点链接的适用范围限制为 MVC/Razor Pages 操作和页面。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1814">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="6d72c-1815">将计划在未来发布的版本中扩展终结点链接的功能。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1815">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

<span data-ttu-id="6d72c-1816">路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1816">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="6d72c-1817">[ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1817">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="6d72c-1818">要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1818">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="6d72c-1819">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="6d72c-1819">URL matching</span></span>

<span data-ttu-id="6d72c-1820">URL 匹配是路由向终结点调度传入请求的过程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1820">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="6d72c-1821">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1821">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="6d72c-1822">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1822">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="6d72c-1823">终结点路由中的路由系统负责所有的调度决策。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1823">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="6d72c-1824">由于中间件是基于所选终结点来应用策略，因此任何可能影响调度或安全策略应用的决策都应在路由系统内部制定，这一点很重要。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1824">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="6d72c-1825">执行终结点委托时，将根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1825">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="6d72c-1826">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1826">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="6d72c-1827">这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1827">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="6d72c-1828">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1828">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="6d72c-1829">提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1829"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="6d72c-1830">这些值是开发者定义的，不会影响通过任何方式路由的行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1830">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="6d72c-1831">此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1831">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="6d72c-1832">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1832">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="6d72c-1833">路由可以相互嵌套。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1833">Routes can be nested inside of one another.</span></span> <span data-ttu-id="6d72c-1834"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1834">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="6d72c-1835">通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1835">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="6d72c-1836"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1836">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="6d72c-1837">使用 LinkGenerator 的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="6d72c-1837">URL generation with LinkGenerator</span></span>

<span data-ttu-id="6d72c-1838">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1838">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="6d72c-1839">这需要考虑终结点与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1839">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="6d72c-1840">终结点路由包含链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1840">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="6d72c-1841"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 是可从 [DI](xref:fundamentals/dependency-injection) 检索的单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1841"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6d72c-1842">该 API 可在执行请求的上下文之外使用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1842">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="6d72c-1843">MVC 的 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 和依赖 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的方案（如 [Tag Helpers](xref:mvc/views/tag-helpers/intro)、HTML Helpers 和 [Action Results](xref:mvc/controllers/actions)）使用链接生成器提供链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1843">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="6d72c-1844">链接生成器基于“地址”和“地址方案”概念 。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1844">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="6d72c-1845">地址方案是确定哪些终结点用于链接生成的方式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1845">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="6d72c-1846">例如，许多用户熟悉的来自 MVC/Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1846">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="6d72c-1847">链接生成器可以通过以下扩展方法链接到 MVC/Razor Pages 操作和页面：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1847">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="6d72c-1848">这些方法的重载接受包含 `HttpContext` 的参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1848">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="6d72c-1849">这些方法在功能上等同于 `Url.Action` 和 `Url.Page`，但提供了更大的灵活性和更多的选项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1849">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="6d72c-1850">`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1850">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="6d72c-1851">`GetUri*` 方法始终生成包含方案和主机的绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1851">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="6d72c-1852">接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1852">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="6d72c-1853">除非重写，否则将使用来自执行请求的环境路由值、URL 基本路径、方案和主机。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1853">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="6d72c-1854">使用地址调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1854"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="6d72c-1855">生成 URI 的过程分两步进行：</span><span class="sxs-lookup"><span data-stu-id="6d72c-1855">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="6d72c-1856">将地址绑定到与地址匹配的终结点列表。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1856">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="6d72c-1857">计算每个终结点的 `RoutePattern`，直到找到与提供的值匹配的路由模式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1857">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="6d72c-1858">输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1858">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="6d72c-1859">对任何类型的地址，<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法均支持标准链接生成功能。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1859">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="6d72c-1860">使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作。</span><span class="sxs-lookup"><span data-stu-id="6d72c-1860">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="6d72c-1861">扩展方法</span><span class="sxs-lookup"><span data-stu-id="6d72c-1861">Extension Method</span></span>   | <span data-ttu-id="6d72c-1862">描述</span><span class="sxs-lookup"><span data-stu-id="6d72c-1862">Description</span></span>                                                         |
| ---
<span data-ttu-id="6d72c-1863">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1863">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1864">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1864">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1865">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1865">'Identity'</span></span>
- <span data-ttu-id="6d72c-1866">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1866">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1867">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1867">'Razor'</span></span>
- <span data-ttu-id="6d72c-1868">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1868">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1869">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1869">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1870">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1870">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1871">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1871">'Identity'</span></span>
- <span data-ttu-id="6d72c-1872">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1872">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1873">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1873">'Razor'</span></span>
- <span data-ttu-id="6d72c-1874">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1874">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1875">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1875">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1876">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1876">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1877">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1877">'Identity'</span></span>
- <span data-ttu-id="6d72c-1878">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1878">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1879">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1879">'Razor'</span></span>
- <span data-ttu-id="6d72c-1880">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1880">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1881">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1881">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1882">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1882">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1883">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1883">'Identity'</span></span>
- <span data-ttu-id="6d72c-1884">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1884">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1885">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1885">'Razor'</span></span>
- <span data-ttu-id="6d72c-1886">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1886">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1887">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1887">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1888">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1888">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1889">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1889">'Identity'</span></span>
- <span data-ttu-id="6d72c-1890">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1890">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1891">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1891">'Razor'</span></span>
- <span data-ttu-id="6d72c-1892">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1892">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1893">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1893">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1894">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1894">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1895">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1895">'Identity'</span></span>
- <span data-ttu-id="6d72c-1896">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1896">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1897">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1897">'Razor'</span></span>
- <span data-ttu-id="6d72c-1898">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1898">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1899">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1899">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1900">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1900">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1901">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1901">'Identity'</span></span>
- <span data-ttu-id="6d72c-1902">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1902">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1903">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1903">'Razor'</span></span>
- <span data-ttu-id="6d72c-1904">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1904">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-1905">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1905">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1906">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1906">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1907">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1907">'Identity'</span></span>
- <span data-ttu-id="6d72c-1908">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1908">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1909">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1909">'Razor'</span></span>
- <span data-ttu-id="6d72c-1910">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1910">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1911">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1911">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1912">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1912">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1913">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1913">'Identity'</span></span>
- <span data-ttu-id="6d72c-1914">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1914">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1915">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1915">'Razor'</span></span>
- <span data-ttu-id="6d72c-1916">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1916">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1917">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1917">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1918">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1918">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1919">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1919">'Identity'</span></span>
- <span data-ttu-id="6d72c-1920">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1920">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1921">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1921">'Razor'</span></span>
- <span data-ttu-id="6d72c-1922">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1922">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1923">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1923">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1924">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1924">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1925">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1925">'Identity'</span></span>
- <span data-ttu-id="6d72c-1926">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1926">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1927">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1927">'Razor'</span></span>
- <span data-ttu-id="6d72c-1928">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1928">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1929">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1929">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1930">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1930">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1931">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1931">'Identity'</span></span>
- <span data-ttu-id="6d72c-1932">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1932">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1933">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1933">'Razor'</span></span>
- <span data-ttu-id="6d72c-1934">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1934">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1935">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1935">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1936">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1936">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1937">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1937">'Identity'</span></span>
- <span data-ttu-id="6d72c-1938">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1938">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1939">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1939">'Razor'</span></span>
- <span data-ttu-id="6d72c-1940">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1940">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1941">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1941">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1942">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1942">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1943">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1943">'Identity'</span></span>
- <span data-ttu-id="6d72c-1944">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1944">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1945">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1945">'Razor'</span></span>
- <span data-ttu-id="6d72c-1946">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1946">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1947">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1947">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1948">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1948">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1949">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1949">'Identity'</span></span>
- <span data-ttu-id="6d72c-1950">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1950">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1951">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1951">'Razor'</span></span>
- <span data-ttu-id="6d72c-1952">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1952">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1953">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1953">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1954">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1954">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1955">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1955">'Identity'</span></span>
- <span data-ttu-id="6d72c-1956">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1956">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1957">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1957">'Razor'</span></span>
- <span data-ttu-id="6d72c-1958">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1958">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1959">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1959">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1960">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1960">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1961">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1961">'Identity'</span></span>
- <span data-ttu-id="6d72c-1962">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1962">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1963">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1963">'Razor'</span></span>
- <span data-ttu-id="6d72c-1964">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1964">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1965">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1965">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1966">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1966">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1967">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1967">'Identity'</span></span>
- <span data-ttu-id="6d72c-1968">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1968">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1969">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1969">'Razor'</span></span>
- <span data-ttu-id="6d72c-1970">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1970">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1971">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1971">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1972">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1972">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1973">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1973">'Identity'</span></span>
- <span data-ttu-id="6d72c-1974">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1974">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1975">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1975">'Razor'</span></span>
- <span data-ttu-id="6d72c-1976">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1976">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1977">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1977">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1978">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1978">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1979">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1979">'Identity'</span></span>
- <span data-ttu-id="6d72c-1980">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1980">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1981">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1981">'Razor'</span></span>
- <span data-ttu-id="6d72c-1982">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1982">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1983">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1983">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1984">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1984">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1985">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1985">'Identity'</span></span>
- <span data-ttu-id="6d72c-1986">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1986">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1987">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1987">'Razor'</span></span>
- <span data-ttu-id="6d72c-1988">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1988">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1989">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1989">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1990">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1990">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1991">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1991">'Identity'</span></span>
- <span data-ttu-id="6d72c-1992">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1992">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1993">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1993">'Razor'</span></span>
- <span data-ttu-id="6d72c-1994">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1994">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-1995">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-1995">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-1996">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1996">'Blazor'</span></span>
- <span data-ttu-id="6d72c-1997">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1997">'Identity'</span></span>
- <span data-ttu-id="6d72c-1998">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1998">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-1999">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-1999">'Razor'</span></span>
- <span data-ttu-id="6d72c-2000">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2000">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2001">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2001">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2002">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2002">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2003">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2003">'Identity'</span></span>
- <span data-ttu-id="6d72c-2004">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2004">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2005">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2005">'Razor'</span></span>
- <span data-ttu-id="6d72c-2006">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2006">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2007">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2007">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2008">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2008">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2009">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2009">'Identity'</span></span>
- <span data-ttu-id="6d72c-2010">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2010">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2011">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2011">'Razor'</span></span>
- <span data-ttu-id="6d72c-2012">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2012">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2013">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2013">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2014">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2014">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2015">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2015">'Identity'</span></span>
- <span data-ttu-id="6d72c-2016">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2016">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2017">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2017">'Razor'</span></span>
- <span data-ttu-id="6d72c-2018">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2018">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2019">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2019">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2020">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2020">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2021">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2021">'Identity'</span></span>
- <span data-ttu-id="6d72c-2022">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2022">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2023">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2023">'Razor'</span></span>
- <span data-ttu-id="6d72c-2024">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2024">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2025">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2025">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2026">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2026">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2027">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2027">'Identity'</span></span>
- <span data-ttu-id="6d72c-2028">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2028">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2029">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2029">'Razor'</span></span>
- <span data-ttu-id="6d72c-2030">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2030">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2031">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2031">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2032">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2032">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2033">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2033">'Identity'</span></span>
- <span data-ttu-id="6d72c-2034">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2034">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2035">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2035">'Razor'</span></span>
- <span data-ttu-id="6d72c-2036">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2036">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2037">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2037">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2038">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2038">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2039">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2039">'Identity'</span></span>
- <span data-ttu-id="6d72c-2040">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2040">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2041">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2041">'Razor'</span></span>
- <span data-ttu-id="6d72c-2042">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2042">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2043">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2043">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2044">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2044">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2045">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2045">'Identity'</span></span>
- <span data-ttu-id="6d72c-2046">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2046">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2047">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2047">'Razor'</span></span>
- <span data-ttu-id="6d72c-2048">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2048">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2049">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2049">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2050">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2050">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2051">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2051">'Identity'</span></span>
- <span data-ttu-id="6d72c-2052">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2052">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2053">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2053">'Razor'</span></span>
- <span data-ttu-id="6d72c-2054">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2054">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2055">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2055">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2056">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2056">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2057">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2057">'Identity'</span></span>
- <span data-ttu-id="6d72c-2058">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2058">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2059">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2059">'Razor'</span></span>
- <span data-ttu-id="6d72c-2060">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2060">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2061">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2061">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2062">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2062">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2063">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2063">'Identity'</span></span>
- <span data-ttu-id="6d72c-2064">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2064">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2065">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2065">'Razor'</span></span>
- <span data-ttu-id="6d72c-2066">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2066">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2067">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2067">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2068">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2068">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2069">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2069">'Identity'</span></span>
- <span data-ttu-id="6d72c-2070">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2070">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2071">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2071">'Razor'</span></span>
- <span data-ttu-id="6d72c-2072">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2072">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2073">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2073">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2074">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2074">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2075">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2075">'Identity'</span></span>
- <span data-ttu-id="6d72c-2076">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2076">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2077">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2077">'Razor'</span></span>
- <span data-ttu-id="6d72c-2078">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2078">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2079">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2079">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2080">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2080">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2081">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2081">'Identity'</span></span>
- <span data-ttu-id="6d72c-2082">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2082">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2083">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2083">'Razor'</span></span>
- <span data-ttu-id="6d72c-2084">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2084">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2085">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2085">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2086">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2086">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2087">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2087">'Identity'</span></span>
- <span data-ttu-id="6d72c-2088">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2088">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2089">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2089">'Razor'</span></span>
- <span data-ttu-id="6d72c-2090">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2090">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-2091">---------------------------------- | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 根据提供的值生成具有绝对路径的 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2091">---------------------------------- | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generates a URI with an absolute path based on the provided values.</span></span> <span data-ttu-id="6d72c-2092">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 根据提供的值生成绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2092">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="6d72c-2093">请注意有关调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列含义：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2093">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="6d72c-2094">对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2094">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="6d72c-2095">如果未验证传入请求的 `Host`标头，则可能以视图/页面中 URI 的形式将不受信任的请求输入发送回客户端。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2095">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="6d72c-2096">建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2096">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="6d72c-2097">在中间件中将 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2097">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="6d72c-2098">`Map*` 会更改执行请求的基路径，这会影响链接生成的输出。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2098">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="6d72c-2099">所有 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允许指定基路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2099">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="6d72c-2100">始终指定一个空的基路径来撤消 `Map*` 对链接生成的影响。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2100">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="6d72c-2101">与早期版本路由的差异</span><span class="sxs-lookup"><span data-stu-id="6d72c-2101">Differences from earlier versions of routing</span></span>

<span data-ttu-id="6d72c-2102">ASP.NET Core 2.2 或更高版本中的终结点路由与 ASP.NET Core 中早期版本的路由之间存在一些差异：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2102">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="6d72c-2103">终结点路由系统不支持基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的可扩展性，包括从 <xref:Microsoft.AspNetCore.Routing.Route> 继承。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2103">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="6d72c-2104">终结点路由不支持 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2104">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="6d72c-2105">使用 2.1 [兼容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 以继续使用兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2105">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="6d72c-2106">在使用传统路由时，终结点路由对生成的 URI 的大小写具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2106">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="6d72c-2107">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2107">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="6d72c-2108">假设使用以下路由生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2108">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="6d72c-2109">如果使用基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由，此代码生成 URI `/blog/ReadPost/17`，该 URI 遵循所提供路由值的大小写。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2109">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="6d72c-2110">ASP.NET Core 2.2 或更高版本中的终结点路由生成 `/Blog/ReadPost/17`（“Blog”大写）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2110">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="6d72c-2111">终结点路由提供 `IOutboundParameterTransformer` 接口，可用于在全局范围自定义此行为或应用不同的约定来映射 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2111">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="6d72c-2112">有关详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2112">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="6d72c-2113">在试图链接到不存在的控制器/操作或页面时，MVC/Razor Pages 通过传统路由执行的链接生成，其操作具有不同的行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2113">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="6d72c-2114">请考虑以下默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2114">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="6d72c-2115">假设使用以下路由通过默认模板生成某个操作的链接：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2115">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="6d72c-2116">如果使用基于 `IRouter` 的路由，即使 `BlogController` 不存在或没有 `ReadPost` 操作方法，结果也始终为 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2116">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="6d72c-2117">正如所料，如果操作方法存在，ASP.NET Core 2.2 或更高版本中的终结点路由会生成 `/Blog/ReadPost/17`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2117">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="6d72c-2118">但是，如果操作不存在，终结点路由会生成空字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2118">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="6d72c-2119">从概念上讲，如果操作不存在，终结点路由不会假定终结点存在。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2119">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="6d72c-2120">与终结点路由一起使用时，链接生成环境值失效算法的行为会有所不同。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2120">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="6d72c-2121">*环境值失效*是一种算法，用于决定当前正在执行的请求（环境值）中的哪些路由值可用于链接生成操作。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2121">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="6d72c-2122">链接到不同操作时，传统路由会使额外的路由值失效。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2122">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="6d72c-2123">ASP.NET Core 2.2 之前的版本中，属性路由不具有此行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2123">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="6d72c-2124">在 ASP.NET Core 的早期版本中，如果有另一个操作使用同一路由参数名称，则该操作的链接会导致发生链接生成错误。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2124">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="6d72c-2125">在 ASP.NET Core 2.2 或更高版本中，链接到另一个操作时，这两种路由形式都会使值失效。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2125">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="6d72c-2126">请考虑 ASP.NET Core2.1 或更高版本中的以下示例。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2126">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="6d72c-2127">链接到另一个操作（或另一页面）时，路由值可能会按非预期的方式被重用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2127">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="6d72c-2128">在 /Pages/Store/Product.cshtml 中：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2128">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="6d72c-2129">在 /Pages/Login.cshtml 中：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2129">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="6d72c-2130">如果 ASP.NET Core 2.1 或更早版本中的 URI 为 `/Store/Product/18`，则由 `@Url.Page("/Login")` 在 Store/Info 页面上生成的链接为 `/Login/18`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2130">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="6d72c-2131">即使链接目标完全是应用的其他部分，仍会重用 `id` 值 18。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2131">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="6d72c-2132">`/Login` 页面上下文中的 `id` 路由值可能是用户 ID 值，而非存储产品 ID 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2132">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="6d72c-2133">在 ASP.NET Core 2.2 或更高版本的终结点路由中，结果为 `/Login`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2133">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="6d72c-2134">当链接的目标是另一个操作或页面时，不会重复使用环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2134">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="6d72c-2135">往返路由参数语法：使用双星号 (`**`) catch-all 参数语法时，不对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2135">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="6d72c-2136">在链接生成期间，路由系统对双星号 (`**`) catch-all 参数（例如，`{**myparametername}`）中捕获的除正斜杠外的值进行编码。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2136">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="6d72c-2137">在 ASP.NET Core 2.2 或更高版本中，基于 `IRouter` 的路由支持双星号 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2137">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="6d72c-2138">ASP.NET Core (`{*myparametername}`) 早期版本中的单星号 catch-all 参数语法仍然受支持，并对正斜杠进行编码。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2138">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="6d72c-2139">路由</span><span class="sxs-lookup"><span data-stu-id="6d72c-2139">Route</span></span>              | <span data-ttu-id="6d72c-2140">链接生成方式为</span><span class="sxs-lookup"><span data-stu-id="6d72c-2140">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ---
<span data-ttu-id="6d72c-2141">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2141">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2142">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2143">'Identity'</span></span>
- <span data-ttu-id="6d72c-2144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2144">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2145">'Razor'</span></span>
- <span data-ttu-id="6d72c-2146">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2148">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2149">'Identity'</span></span>
- <span data-ttu-id="6d72c-2150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2150">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2151">'Razor'</span></span>
- <span data-ttu-id="6d72c-2152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2154">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2155">'Identity'</span></span>
- <span data-ttu-id="6d72c-2156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2156">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2157">'Razor'</span></span>
- <span data-ttu-id="6d72c-2158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2160">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2161">'Identity'</span></span>
- <span data-ttu-id="6d72c-2162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2162">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2163">'Razor'</span></span>
- <span data-ttu-id="6d72c-2164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2166">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2167">'Identity'</span></span>
- <span data-ttu-id="6d72c-2168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2168">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2169">'Razor'</span></span>
- <span data-ttu-id="6d72c-2170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2172">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2173">'Identity'</span></span>
- <span data-ttu-id="6d72c-2174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2174">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2175">'Razor'</span></span>
- <span data-ttu-id="6d72c-2176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2178">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2179">'Identity'</span></span>
- <span data-ttu-id="6d72c-2180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2180">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2181">'Razor'</span></span>
- <span data-ttu-id="6d72c-2182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2182">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-2183">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2183">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2184">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2185">'Identity'</span></span>
- <span data-ttu-id="6d72c-2186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2186">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2187">'Razor'</span></span>
- <span data-ttu-id="6d72c-2188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2190">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2191">'Identity'</span></span>
- <span data-ttu-id="6d72c-2192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2192">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2193">'Razor'</span></span>
- <span data-ttu-id="6d72c-2194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2196">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2197">'Identity'</span></span>
- <span data-ttu-id="6d72c-2198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2198">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2199">'Razor'</span></span>
- <span data-ttu-id="6d72c-2200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2202">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2203">'Identity'</span></span>
- <span data-ttu-id="6d72c-2204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2204">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2205">'Razor'</span></span>
- <span data-ttu-id="6d72c-2206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2208">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2209">'Identity'</span></span>
- <span data-ttu-id="6d72c-2210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2210">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2211">'Razor'</span></span>
- <span data-ttu-id="6d72c-2212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2214">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2215">'Identity'</span></span>
- <span data-ttu-id="6d72c-2216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2216">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2217">'Razor'</span></span>
- <span data-ttu-id="6d72c-2218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2220">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2221">'Identity'</span></span>
- <span data-ttu-id="6d72c-2222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2222">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2223">'Razor'</span></span>
- <span data-ttu-id="6d72c-2224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2226">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2227">'Identity'</span></span>
- <span data-ttu-id="6d72c-2228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2228">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2229">'Razor'</span></span>
- <span data-ttu-id="6d72c-2230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2232">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2233">'Identity'</span></span>
- <span data-ttu-id="6d72c-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2235">'Razor'</span></span>
- <span data-ttu-id="6d72c-2236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2238">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2239">'Identity'</span></span>
- <span data-ttu-id="6d72c-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2241">'Razor'</span></span>
- <span data-ttu-id="6d72c-2242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2244">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2245">'Identity'</span></span>
- <span data-ttu-id="6d72c-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2247">'Razor'</span></span>
- <span data-ttu-id="6d72c-2248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2250">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2251">'Identity'</span></span>
- <span data-ttu-id="6d72c-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2253">'Razor'</span></span>
- <span data-ttu-id="6d72c-2254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2256">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2257">'Identity'</span></span>
- <span data-ttu-id="6d72c-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2259">'Razor'</span></span>
- <span data-ttu-id="6d72c-2260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2262">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2263">'Identity'</span></span>
- <span data-ttu-id="6d72c-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2265">'Razor'</span></span>
- <span data-ttu-id="6d72c-2266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2268">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2269">'Identity'</span></span>
- <span data-ttu-id="6d72c-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2271">'Razor'</span></span>
- <span data-ttu-id="6d72c-2272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2274">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2275">'Identity'</span></span>
- <span data-ttu-id="6d72c-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2277">'Razor'</span></span>
- <span data-ttu-id="6d72c-2278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2280">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2281">'Identity'</span></span>
- <span data-ttu-id="6d72c-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2283">'Razor'</span></span>
- <span data-ttu-id="6d72c-2284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2286">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2287">'Identity'</span></span>
- <span data-ttu-id="6d72c-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2289">'Razor'</span></span>
- <span data-ttu-id="6d72c-2290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2292">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2293">'Identity'</span></span>
- <span data-ttu-id="6d72c-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2295">'Razor'</span></span>
- <span data-ttu-id="6d72c-2296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2298">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2299">'Identity'</span></span>
- <span data-ttu-id="6d72c-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2301">'Razor'</span></span>
- <span data-ttu-id="6d72c-2302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2304">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2305">'Identity'</span></span>
- <span data-ttu-id="6d72c-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2307">'Razor'</span></span>
- <span data-ttu-id="6d72c-2308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2310">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2311">'Identity'</span></span>
- <span data-ttu-id="6d72c-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2313">'Razor'</span></span>
- <span data-ttu-id="6d72c-2314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2316">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2317">'Identity'</span></span>
- <span data-ttu-id="6d72c-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2319">'Razor'</span></span>
- <span data-ttu-id="6d72c-2320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2322">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2323">'Identity'</span></span>
- <span data-ttu-id="6d72c-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2325">'Razor'</span></span>
- <span data-ttu-id="6d72c-2326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2328">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2329">'Identity'</span></span>
- <span data-ttu-id="6d72c-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2331">'Razor'</span></span>
- <span data-ttu-id="6d72c-2332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2334">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2335">'Identity'</span></span>
- <span data-ttu-id="6d72c-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2337">'Razor'</span></span>
- <span data-ttu-id="6d72c-2338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2340">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2341">'Identity'</span></span>
- <span data-ttu-id="6d72c-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2343">'Razor'</span></span>
- <span data-ttu-id="6d72c-2344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2346">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2347">'Identity'</span></span>
- <span data-ttu-id="6d72c-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2349">'Razor'</span></span>
- <span data-ttu-id="6d72c-2350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2352">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2353">'Identity'</span></span>
- <span data-ttu-id="6d72c-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2355">'Razor'</span></span>
- <span data-ttu-id="6d72c-2356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2358">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2359">'Identity'</span></span>
- <span data-ttu-id="6d72c-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2361">'Razor'</span></span>
- <span data-ttu-id="6d72c-2362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2364">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2365">'Identity'</span></span>
- <span data-ttu-id="6d72c-2366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2366">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2367">'Razor'</span></span>
- <span data-ttu-id="6d72c-2368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2370">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2371">'Identity'</span></span>
- <span data-ttu-id="6d72c-2372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2372">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2373">'Razor'</span></span>
- <span data-ttu-id="6d72c-2374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2374">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-2375">----------------------------------- |   | `/search/{*page}`  | `/search/admin%2Fproducts` （对正斜杠进行编码）             |   | `/search/{**page}` | `/search/admin/products`                                              |</span><span class="sxs-lookup"><span data-stu-id="6d72c-2375">----------------------------------- |   | `/search/{*page}`  | `/search/admin%2Fproducts` (the forward slash is encoded)             |   | `/search/{**page}` | `/search/admin/products`                                              |</span></span>

### <a name="middleware-example"></a><span data-ttu-id="6d72c-2376">中间件示例</span><span class="sxs-lookup"><span data-stu-id="6d72c-2376">Middleware example</span></span>

<span data-ttu-id="6d72c-2377">在以下示例中，中间件使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 创建列出存储产品的操作方法的链接。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2377">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="6d72c-2378">应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2378">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

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

### <a name="create-routes"></a><span data-ttu-id="6d72c-2379">创建路由</span><span class="sxs-lookup"><span data-stu-id="6d72c-2379">Create routes</span></span>

<span data-ttu-id="6d72c-2380">大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2380">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="6d72c-2381">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2381">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="6d72c-2382"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2382"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="6d72c-2383"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2383"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="6d72c-2384">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2384">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="6d72c-2385">以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2385">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="6d72c-2386">此模板与 URL 路径相匹配，并且提取路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2386">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="6d72c-2387">例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2387">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="6d72c-2388">路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2388">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="6d72c-2389">路由参数已命名。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2389">Route parameters are named.</span></span> <span data-ttu-id="6d72c-2390">参数通过将参数名称括在大括号 `{ ... }` 中来定义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2390">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="6d72c-2391">上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2391">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="6d72c-2392">这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2392">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="6d72c-2393">路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2393">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="6d72c-2394">路由参数名称后面的问号 (`?`) 定义了可选参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2394">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="6d72c-2395">路由匹配时，具有默认值的路由参数始终会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2395">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="6d72c-2396">如果没有相应的 URL 路径段，则可选参数不会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2396">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="6d72c-2397">有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2397">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="6d72c-2398">在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2398">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="6d72c-2399">此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2399">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="6d72c-2400">路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2400">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="6d72c-2401">在此示例中，路由值 `id` 必须可转换为整数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2401">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="6d72c-2402">有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2402">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="6d72c-2403">其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2403">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="6d72c-2404">这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2404">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="6d72c-2405">以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2405">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

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
> <span data-ttu-id="6d72c-2406">定义约束和默认值的内联语法对于简单路由很方便。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2406">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="6d72c-2407">但是，存在内联语法不支持的方案，例如数据令牌。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2407">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="6d72c-2408">以下示例演示了一些其他方案：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2408">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="6d72c-2409">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2409">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="6d72c-2410">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2410">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="6d72c-2411">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2411">Default values can be specified in the route template.</span></span> <span data-ttu-id="6d72c-2412">根据路由参数名称前的双星号 (`**`) 外观，`article` 路由参数被定义为 catch-all。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2412">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="6d72c-2413">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2413">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="6d72c-2414">以下示例添加了路由约束和数据令牌：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2414">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="6d72c-2415">上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2415">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="6d72c-2417">路由类 URL 生成</span><span class="sxs-lookup"><span data-stu-id="6d72c-2417">Route class URL generation</span></span>

<span data-ttu-id="6d72c-2418"><xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2418">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="6d72c-2419">从逻辑上来说，这是匹配 URL 路径的反向过程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2419">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="6d72c-2420">为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2420">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="6d72c-2421">将生成什么值？</span><span class="sxs-lookup"><span data-stu-id="6d72c-2421">What values would be produced?</span></span> <span data-ttu-id="6d72c-2422">这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2422">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="6d72c-2423">以下示例使用常规 ASP.NET Core MVC 默认路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2423">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="6d72c-2424">使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2424">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="6d72c-2425">路由值将替换为相应的路由参数，以形成 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2425">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="6d72c-2426">由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2426">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="6d72c-2427">使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2427">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="6d72c-2428">提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2428">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="6d72c-2429">已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2429">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="6d72c-2430">使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2430">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="6d72c-2431">有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2431">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="6d72c-2432">使用路由中间件</span><span class="sxs-lookup"><span data-stu-id="6d72c-2432">Use Routing Middleware</span></span>

<span data-ttu-id="6d72c-2433">引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2433">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="6d72c-2434">将路由添加到 `Startup.ConfigureServices` 中的服务容器：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2434">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="6d72c-2435">必须在 `Startup.Configure` 方法中配置路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2435">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="6d72c-2436">该示例应用使用以下 API：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2436">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="6d72c-2437"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>：仅匹配 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2437"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="6d72c-2438">下表显示了具有给定 URI 的响应。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2438">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="6d72c-2439">URI</span><span class="sxs-lookup"><span data-stu-id="6d72c-2439">URI</span></span>                    | <span data-ttu-id="6d72c-2440">响应</span><span class="sxs-lookup"><span data-stu-id="6d72c-2440">Response</span></span>                                          |
| ---
<span data-ttu-id="6d72c-2441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2442">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2443">'Identity'</span></span>
- <span data-ttu-id="6d72c-2444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2444">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2445">'Razor'</span></span>
- <span data-ttu-id="6d72c-2446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2448">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2449">'Identity'</span></span>
- <span data-ttu-id="6d72c-2450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2450">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2451">'Razor'</span></span>
- <span data-ttu-id="6d72c-2452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2454">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2455">'Identity'</span></span>
- <span data-ttu-id="6d72c-2456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2456">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2457">'Razor'</span></span>
- <span data-ttu-id="6d72c-2458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2460">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2461">'Identity'</span></span>
- <span data-ttu-id="6d72c-2462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2462">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2463">'Razor'</span></span>
- <span data-ttu-id="6d72c-2464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2466">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2467">'Identity'</span></span>
- <span data-ttu-id="6d72c-2468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2468">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2469">'Razor'</span></span>
- <span data-ttu-id="6d72c-2470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2472">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2473">'Identity'</span></span>
- <span data-ttu-id="6d72c-2474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2474">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2475">'Razor'</span></span>
- <span data-ttu-id="6d72c-2476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2478">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2479">'Identity'</span></span>
- <span data-ttu-id="6d72c-2480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2480">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2481">'Razor'</span></span>
- <span data-ttu-id="6d72c-2482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2484">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2485">'Identity'</span></span>
- <span data-ttu-id="6d72c-2486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2486">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2487">'Razor'</span></span>
- <span data-ttu-id="6d72c-2488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2490">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2491">'Identity'</span></span>
- <span data-ttu-id="6d72c-2492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2492">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2493">'Razor'</span></span>
- <span data-ttu-id="6d72c-2494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2494">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-2495">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2495">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2496">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2497">'Identity'</span></span>
- <span data-ttu-id="6d72c-2498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2498">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2499">'Razor'</span></span>
- <span data-ttu-id="6d72c-2500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2502">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2503">'Identity'</span></span>
- <span data-ttu-id="6d72c-2504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2504">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2505">'Razor'</span></span>
- <span data-ttu-id="6d72c-2506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2508">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2509">'Identity'</span></span>
- <span data-ttu-id="6d72c-2510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2510">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2511">'Razor'</span></span>
- <span data-ttu-id="6d72c-2512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2514">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2515">'Identity'</span></span>
- <span data-ttu-id="6d72c-2516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2516">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2517">'Razor'</span></span>
- <span data-ttu-id="6d72c-2518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2520">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2521">'Identity'</span></span>
- <span data-ttu-id="6d72c-2522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2522">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2523">'Razor'</span></span>
- <span data-ttu-id="6d72c-2524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2526">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2527">'Identity'</span></span>
- <span data-ttu-id="6d72c-2528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2528">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2529">'Razor'</span></span>
- <span data-ttu-id="6d72c-2530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2532">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2533">'Identity'</span></span>
- <span data-ttu-id="6d72c-2534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2534">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2535">'Razor'</span></span>
- <span data-ttu-id="6d72c-2536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2538">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2539">'Identity'</span></span>
- <span data-ttu-id="6d72c-2540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2540">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2541">'Razor'</span></span>
- <span data-ttu-id="6d72c-2542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2544">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2545">'Identity'</span></span>
- <span data-ttu-id="6d72c-2546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2546">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2547">'Razor'</span></span>
- <span data-ttu-id="6d72c-2548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2550">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2551">'Identity'</span></span>
- <span data-ttu-id="6d72c-2552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2552">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2553">'Razor'</span></span>
- <span data-ttu-id="6d72c-2554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2556">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2557">'Identity'</span></span>
- <span data-ttu-id="6d72c-2558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2558">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2559">'Razor'</span></span>
- <span data-ttu-id="6d72c-2560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2562">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2563">'Identity'</span></span>
- <span data-ttu-id="6d72c-2564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2564">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2565">'Razor'</span></span>
- <span data-ttu-id="6d72c-2566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2568">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2569">'Identity'</span></span>
- <span data-ttu-id="6d72c-2570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2570">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2571">'Razor'</span></span>
- <span data-ttu-id="6d72c-2572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2574">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2575">'Identity'</span></span>
- <span data-ttu-id="6d72c-2576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2576">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2577">'Razor'</span></span>
- <span data-ttu-id="6d72c-2578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2580">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2581">'Identity'</span></span>
- <span data-ttu-id="6d72c-2582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2582">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2583">'Razor'</span></span>
- <span data-ttu-id="6d72c-2584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2586">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2587">'Identity'</span></span>
- <span data-ttu-id="6d72c-2588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2588">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2589">'Razor'</span></span>
- <span data-ttu-id="6d72c-2590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2592">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2593">'Identity'</span></span>
- <span data-ttu-id="6d72c-2594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2594">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2595">'Razor'</span></span>
- <span data-ttu-id="6d72c-2596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2598">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2599">'Identity'</span></span>
- <span data-ttu-id="6d72c-2600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2600">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2601">'Razor'</span></span>
- <span data-ttu-id="6d72c-2602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2604">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2605">'Identity'</span></span>
- <span data-ttu-id="6d72c-2606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2606">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2607">'Razor'</span></span>
- <span data-ttu-id="6d72c-2608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2610">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2611">'Identity'</span></span>
- <span data-ttu-id="6d72c-2612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2612">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2613">'Razor'</span></span>
- <span data-ttu-id="6d72c-2614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2616">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2617">'Identity'</span></span>
- <span data-ttu-id="6d72c-2618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2618">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2619">'Razor'</span></span>
- <span data-ttu-id="6d72c-2620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2622">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2623">'Identity'</span></span>
- <span data-ttu-id="6d72c-2624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2624">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2625">'Razor'</span></span>
- <span data-ttu-id="6d72c-2626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2626">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-2627">------------------------- | | `/package/create/3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="6d72c-2627">------------------------- | | `/package/create/3`    | Hello!</span></span> <span data-ttu-id="6d72c-2628">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="6d72c-2628">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span></span> <span data-ttu-id="6d72c-2629">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span><span class="sxs-lookup"><span data-stu-id="6d72c-2629">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span></span> <span data-ttu-id="6d72c-2630">Route values: [operation, track], [id, -3] | | `/package/track/`      | 请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2630">Route values: [operation, track], [id, -3] | | `/package/track/`      | The request falls through, no match.</span></span>              <span data-ttu-id="6d72c-2631">| | `GET /hello/Joe`       | Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="6d72c-2631">| | `GET /hello/Joe`       | Hi, Joe!</span></span>                                          <span data-ttu-id="6d72c-2632">| | `POST /hello/Joe`      | 请求失败，仅匹配 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2632">| | `POST /hello/Joe`      | The request falls through, matches HTTP GET only.</span></span> <span data-ttu-id="6d72c-2633">| | `GET /hello/Joe/Smith` | 请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2633">| | `GET /hello/Joe/Smith` | The request falls through, no match.</span></span>              |

<span data-ttu-id="6d72c-2634">框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2634">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

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

<span data-ttu-id="6d72c-2635">`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2635">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="6d72c-2636">有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2636">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="6d72c-2637">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-2637">Route template reference</span></span>

<span data-ttu-id="6d72c-2638">如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2638">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="6d72c-2639">可在路由段中定义多个路由参数，但必须由文本值隔开。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2639">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="6d72c-2640">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2640">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="6d72c-2641">这些路由参数必须具有名称，且可能指定了其他属性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2641">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="6d72c-2642">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2642">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="6d72c-2643">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2643">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="6d72c-2644">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2644">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="6d72c-2645">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2645">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="6d72c-2646">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2646">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="6d72c-2647">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2647">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="6d72c-2648">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2648">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="6d72c-2649">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="6d72c-2649">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="6d72c-2650">可以使用星号 (`*`) 或双星号 (`**`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2650">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="6d72c-2651">这些称为 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2651">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="6d72c-2652">例如，`blog/{**slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2652">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="6d72c-2653">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2653">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="6d72c-2654">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2654">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="6d72c-2655">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2655">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="6d72c-2656">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2656">Note the escaped forward slash.</span></span> <span data-ttu-id="6d72c-2657">要往返路径分隔符，请使用 `**` 路由参数前缀。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2657">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="6d72c-2658">`{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2658">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="6d72c-2659">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2659">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="6d72c-2660">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2660">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="6d72c-2661">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2661">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="6d72c-2662">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2662">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="6d72c-2663">可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2663">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="6d72c-2664">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2664">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="6d72c-2665">在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2665">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="6d72c-2666">如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2666">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="6d72c-2667">通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2667">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="6d72c-2668">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2668">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="6d72c-2669">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2669">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="6d72c-2670">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2670">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="6d72c-2671">路由参数还可以具有参数转换器，用于在生成链接以及将操作和页面匹配到 URI 时转换参数的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2671">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="6d72c-2672">与约束类似，可以通过在路由参数名称后面添加冒号 (`:`) 和转换器名称，将参数变换器内联添加到路径参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2672">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="6d72c-2673">例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2673">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="6d72c-2674">有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2674">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="6d72c-2675">下表演示了示例路由模板及其行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-2675">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="6d72c-2676">路由模板</span><span class="sxs-lookup"><span data-stu-id="6d72c-2676">Route Template</span></span>                           | <span data-ttu-id="6d72c-2677">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="6d72c-2677">Example Matching URI</span></span>    | <span data-ttu-id="6d72c-2678">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="6d72c-2678">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="6d72c-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2680">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2681">'Identity'</span></span>
- <span data-ttu-id="6d72c-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2683">'Razor'</span></span>
- <span data-ttu-id="6d72c-2684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2684">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2686">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2687">'Identity'</span></span>
- <span data-ttu-id="6d72c-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2689">'Razor'</span></span>
- <span data-ttu-id="6d72c-2690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2692">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2693">'Identity'</span></span>
- <span data-ttu-id="6d72c-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2695">'Razor'</span></span>
- <span data-ttu-id="6d72c-2696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2698">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2699">'Identity'</span></span>
- <span data-ttu-id="6d72c-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2701">'Razor'</span></span>
- <span data-ttu-id="6d72c-2702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2704">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2705">'Identity'</span></span>
- <span data-ttu-id="6d72c-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2707">'Razor'</span></span>
- <span data-ttu-id="6d72c-2708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2710">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2711">'Identity'</span></span>
- <span data-ttu-id="6d72c-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2713">'Razor'</span></span>
- <span data-ttu-id="6d72c-2714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2716">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2717">'Identity'</span></span>
- <span data-ttu-id="6d72c-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2719">'Razor'</span></span>
- <span data-ttu-id="6d72c-2720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2722">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2723">'Identity'</span></span>
- <span data-ttu-id="6d72c-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2725">'Razor'</span></span>
- <span data-ttu-id="6d72c-2726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2728">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2729">'Identity'</span></span>
- <span data-ttu-id="6d72c-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2731">'Razor'</span></span>
- <span data-ttu-id="6d72c-2732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2734">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2735">'Identity'</span></span>
- <span data-ttu-id="6d72c-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2737">'Razor'</span></span>
- <span data-ttu-id="6d72c-2738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2740">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2741">'Identity'</span></span>
- <span data-ttu-id="6d72c-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2743">'Razor'</span></span>
- <span data-ttu-id="6d72c-2744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2746">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2747">'Identity'</span></span>
- <span data-ttu-id="6d72c-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2749">'Razor'</span></span>
- <span data-ttu-id="6d72c-2750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2752">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2753">'Identity'</span></span>
- <span data-ttu-id="6d72c-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2755">'Razor'</span></span>
- <span data-ttu-id="6d72c-2756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2758">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2759">'Identity'</span></span>
- <span data-ttu-id="6d72c-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2761">'Razor'</span></span>
- <span data-ttu-id="6d72c-2762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2764">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2765">'Identity'</span></span>
- <span data-ttu-id="6d72c-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2767">'Razor'</span></span>
- <span data-ttu-id="6d72c-2768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2770">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2771">'Identity'</span></span>
- <span data-ttu-id="6d72c-2772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2772">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2773">'Razor'</span></span>
- <span data-ttu-id="6d72c-2774">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2776">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2777">'Identity'</span></span>
- <span data-ttu-id="6d72c-2778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2778">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2779">'Razor'</span></span>
- <span data-ttu-id="6d72c-2780">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2780">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2781">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2781">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2782">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2782">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2783">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2783">'Identity'</span></span>
- <span data-ttu-id="6d72c-2784">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2784">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2785">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2785">'Razor'</span></span>
- <span data-ttu-id="6d72c-2786">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2786">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-2787">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2787">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2788">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2788">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2789">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2789">'Identity'</span></span>
- <span data-ttu-id="6d72c-2790">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2790">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2791">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2791">'Razor'</span></span>
- <span data-ttu-id="6d72c-2792">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2792">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2793">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2793">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2794">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2795">'Identity'</span></span>
- <span data-ttu-id="6d72c-2796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2796">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2797">'Razor'</span></span>
- <span data-ttu-id="6d72c-2798">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2799">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2799">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2800">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2801">'Identity'</span></span>
- <span data-ttu-id="6d72c-2802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2802">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2803">'Razor'</span></span>
- <span data-ttu-id="6d72c-2804">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2805">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2805">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2806">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2807">'Identity'</span></span>
- <span data-ttu-id="6d72c-2808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2808">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2809">'Razor'</span></span>
- <span data-ttu-id="6d72c-2810">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2811">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2811">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2812">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2813">'Identity'</span></span>
- <span data-ttu-id="6d72c-2814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2814">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2815">'Razor'</span></span>
- <span data-ttu-id="6d72c-2816">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2816">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2817">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2817">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2818">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2819">'Identity'</span></span>
- <span data-ttu-id="6d72c-2820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2820">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2821">'Razor'</span></span>
- <span data-ttu-id="6d72c-2822">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2823">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2823">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2824">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2825">'Identity'</span></span>
- <span data-ttu-id="6d72c-2826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2826">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2827">'Razor'</span></span>
- <span data-ttu-id="6d72c-2828">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2830">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2831">'Identity'</span></span>
- <span data-ttu-id="6d72c-2832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2832">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2833">'Razor'</span></span>
- <span data-ttu-id="6d72c-2834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2836">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2837">'Identity'</span></span>
- <span data-ttu-id="6d72c-2838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2838">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2839">'Razor'</span></span>
- <span data-ttu-id="6d72c-2840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2840">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-2841">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2841">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2842">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2843">'Identity'</span></span>
- <span data-ttu-id="6d72c-2844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2844">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2845">'Razor'</span></span>
- <span data-ttu-id="6d72c-2846">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2848">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2849">'Identity'</span></span>
- <span data-ttu-id="6d72c-2850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2850">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2851">'Razor'</span></span>
- <span data-ttu-id="6d72c-2852">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2854">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2855">'Identity'</span></span>
- <span data-ttu-id="6d72c-2856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2856">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2857">'Razor'</span></span>
- <span data-ttu-id="6d72c-2858">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2860">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2861">'Identity'</span></span>
- <span data-ttu-id="6d72c-2862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2862">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2863">'Razor'</span></span>
- <span data-ttu-id="6d72c-2864">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2866">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2867">'Identity'</span></span>
- <span data-ttu-id="6d72c-2868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2868">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2869">'Razor'</span></span>
- <span data-ttu-id="6d72c-2870">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2872">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2873">'Identity'</span></span>
- <span data-ttu-id="6d72c-2874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2874">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2875">'Razor'</span></span>
- <span data-ttu-id="6d72c-2876">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2878">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2879">'Identity'</span></span>
- <span data-ttu-id="6d72c-2880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2880">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2881">'Razor'</span></span>
- <span data-ttu-id="6d72c-2882">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2884">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2885">'Identity'</span></span>
- <span data-ttu-id="6d72c-2886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2886">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2887">'Razor'</span></span>
- <span data-ttu-id="6d72c-2888">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2890">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2891">'Identity'</span></span>
- <span data-ttu-id="6d72c-2892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2892">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2893">'Razor'</span></span>
- <span data-ttu-id="6d72c-2894">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2896">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2897">'Identity'</span></span>
- <span data-ttu-id="6d72c-2898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2898">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2899">'Razor'</span></span>
- <span data-ttu-id="6d72c-2900">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2902">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2903">'Identity'</span></span>
- <span data-ttu-id="6d72c-2904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2904">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2905">'Razor'</span></span>
- <span data-ttu-id="6d72c-2906">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2906">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2907">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2907">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2908">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2909">'Identity'</span></span>
- <span data-ttu-id="6d72c-2910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2910">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2911">'Razor'</span></span>
- <span data-ttu-id="6d72c-2912">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2914">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2915">'Identity'</span></span>
- <span data-ttu-id="6d72c-2916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2916">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2917">'Razor'</span></span>
- <span data-ttu-id="6d72c-2918">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2920">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2921">'Identity'</span></span>
- <span data-ttu-id="6d72c-2922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2922">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2923">'Razor'</span></span>
- <span data-ttu-id="6d72c-2924">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2926">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2927">'Identity'</span></span>
- <span data-ttu-id="6d72c-2928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2928">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2929">'Razor'</span></span>
- <span data-ttu-id="6d72c-2930">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2932">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2933">'Identity'</span></span>
- <span data-ttu-id="6d72c-2934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2934">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2935">'Razor'</span></span>
- <span data-ttu-id="6d72c-2936">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2938">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2939">'Identity'</span></span>
- <span data-ttu-id="6d72c-2940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2940">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2941">'Razor'</span></span>
- <span data-ttu-id="6d72c-2942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2944">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2945">'Identity'</span></span>
- <span data-ttu-id="6d72c-2946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2946">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2947">'Razor'</span></span>
- <span data-ttu-id="6d72c-2948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2950">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2951">'Identity'</span></span>
- <span data-ttu-id="6d72c-2952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2952">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2953">'Razor'</span></span>
- <span data-ttu-id="6d72c-2954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2956">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2957">'Identity'</span></span>
- <span data-ttu-id="6d72c-2958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2958">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2959">'Razor'</span></span>
- <span data-ttu-id="6d72c-2960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2962">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2963">'Identity'</span></span>
- <span data-ttu-id="6d72c-2964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2964">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2965">'Razor'</span></span>
- <span data-ttu-id="6d72c-2966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2968">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2969">'Identity'</span></span>
- <span data-ttu-id="6d72c-2970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2970">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2971">'Razor'</span></span>
- <span data-ttu-id="6d72c-2972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2974">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2975">'Identity'</span></span>
- <span data-ttu-id="6d72c-2976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2976">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2977">'Razor'</span></span>
- <span data-ttu-id="6d72c-2978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2980">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2981">'Identity'</span></span>
- <span data-ttu-id="6d72c-2982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2982">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2983">'Razor'</span></span>
- <span data-ttu-id="6d72c-2984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2986">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2987">'Identity'</span></span>
- <span data-ttu-id="6d72c-2988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2988">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2989">'Razor'</span></span>
- <span data-ttu-id="6d72c-2990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2992">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2993">'Identity'</span></span>
- <span data-ttu-id="6d72c-2994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2994">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-2995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2995">'Razor'</span></span>
- <span data-ttu-id="6d72c-2996">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-2997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-2997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-2998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2998">'Blazor'</span></span>
- <span data-ttu-id="6d72c-2999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-2999">'Identity'</span></span>
- <span data-ttu-id="6d72c-3000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3000">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3001">'Razor'</span></span>
- <span data-ttu-id="6d72c-3002">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3004">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3005">'Identity'</span></span>
- <span data-ttu-id="6d72c-3006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3006">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3007">'Razor'</span></span>
- <span data-ttu-id="6d72c-3008">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3008">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3009">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3009">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3010">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3010">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3011">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3011">'Identity'</span></span>
- <span data-ttu-id="6d72c-3012">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3012">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3013">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3013">'Razor'</span></span>
- <span data-ttu-id="6d72c-3014">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3014">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3015">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3015">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3016">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3016">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3017">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3017">'Identity'</span></span>
- <span data-ttu-id="6d72c-3018">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3018">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3019">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3019">'Razor'</span></span>
- <span data-ttu-id="6d72c-3020">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3020">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3021">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3021">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3022">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3022">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3023">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3023">'Identity'</span></span>
- <span data-ttu-id="6d72c-3024">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3024">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3025">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3025">'Razor'</span></span>
- <span data-ttu-id="6d72c-3026">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3026">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3027">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3027">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3028">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3028">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3029">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3029">'Identity'</span></span>
- <span data-ttu-id="6d72c-3030">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3030">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3031">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3031">'Razor'</span></span>
- <span data-ttu-id="6d72c-3032">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3032">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3033">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3033">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3034">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3034">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3035">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3035">'Identity'</span></span>
- <span data-ttu-id="6d72c-3036">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3036">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3037">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3037">'Razor'</span></span>
- <span data-ttu-id="6d72c-3038">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3038">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3039">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3039">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3040">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3040">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3041">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3041">'Identity'</span></span>
- <span data-ttu-id="6d72c-3042">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3042">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3043">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3043">'Razor'</span></span>
- <span data-ttu-id="6d72c-3044">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3044">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3045">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3045">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3046">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3046">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3047">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3047">'Identity'</span></span>
- <span data-ttu-id="6d72c-3048">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3048">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3049">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3049">'Razor'</span></span>
- <span data-ttu-id="6d72c-3050">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3050">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3051">------------------------------------- | | `hello`                                  | `/hello`                | 仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3051">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="6d72c-3052">| | `{Page=Home}`                            | `/`                     | 匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3052">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="6d72c-3053">| | `{Page=Home}`                            | `/Contact`              | 匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3053">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="6d72c-3054">| | `{controller}/{action}/{id?}`            | `/Products/List`        | 映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3054">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="6d72c-3055">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | 映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3055">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> <span data-ttu-id="6d72c-3056">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | 映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3056">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="6d72c-3057">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3057">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="6d72c-3058">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3058">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="6d72c-3059">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3059">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="6d72c-3060">保留的路由名称</span><span class="sxs-lookup"><span data-stu-id="6d72c-3060">Reserved routing names</span></span>

<span data-ttu-id="6d72c-3061">以下关键字是保留的名称，它们不能用作路由名称或参数：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3061">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="6d72c-3062">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-3062">Route constraint reference</span></span>

<span data-ttu-id="6d72c-3063">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3063">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="6d72c-3064">路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3064">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="6d72c-3065">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3065">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="6d72c-3066">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3066">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="6d72c-3067">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3067">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="6d72c-3068">请勿将约束用于“输入验证”。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3068">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="6d72c-3069">如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求” 。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3069">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="6d72c-3070">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3070">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="6d72c-3071">下表演示示例路由约束及其预期行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3071">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="6d72c-3072">约束</span><span class="sxs-lookup"><span data-stu-id="6d72c-3072">constraint</span></span> | <span data-ttu-id="6d72c-3073">示例</span><span class="sxs-lookup"><span data-stu-id="6d72c-3073">Example</span></span> | <span data-ttu-id="6d72c-3074">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="6d72c-3074">Example Matches</span></span> | <span data-ttu-id="6d72c-3075">说明</span><span class="sxs-lookup"><span data-stu-id="6d72c-3075">Notes</span></span> |
| ---
<span data-ttu-id="6d72c-3076">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3076">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3077">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3077">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3078">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3078">'Identity'</span></span>
- <span data-ttu-id="6d72c-3079">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3079">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3080">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3080">'Razor'</span></span>
- <span data-ttu-id="6d72c-3081">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3081">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3083">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3084">'Identity'</span></span>
- <span data-ttu-id="6d72c-3085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3085">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3086">'Razor'</span></span>
- <span data-ttu-id="6d72c-3087">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3089">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3090">'Identity'</span></span>
- <span data-ttu-id="6d72c-3091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3091">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3092">'Razor'</span></span>
- <span data-ttu-id="6d72c-3093">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3093">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3094">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3094">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3095">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3096">'Identity'</span></span>
- <span data-ttu-id="6d72c-3097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3097">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3098">'Razor'</span></span>
- <span data-ttu-id="6d72c-3099">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3099">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3100">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3100">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3101">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3102">'Identity'</span></span>
- <span data-ttu-id="6d72c-3103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3103">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3104">'Razor'</span></span>
- <span data-ttu-id="6d72c-3105">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3107">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3108">'Identity'</span></span>
- <span data-ttu-id="6d72c-3109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3109">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3110">'Razor'</span></span>
- <span data-ttu-id="6d72c-3111">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3113">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3114">'Identity'</span></span>
- <span data-ttu-id="6d72c-3115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3115">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3116">'Razor'</span></span>
- <span data-ttu-id="6d72c-3117">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3119">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3120">'Identity'</span></span>
- <span data-ttu-id="6d72c-3121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3121">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3122">'Razor'</span></span>
- <span data-ttu-id="6d72c-3123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3125">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3126">'Identity'</span></span>
- <span data-ttu-id="6d72c-3127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3127">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3128">'Razor'</span></span>
- <span data-ttu-id="6d72c-3129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3129">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3130">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | 匹配任何整数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3130">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer.</span></span> <span data-ttu-id="6d72c-3131">| | `bool` | `{active:bool}` | `true`, `FALSE` | 匹配 `true` 或 `false. Case-insensitive. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture. See  preceding warning.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture. See  preceding warning.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture. See  preceding warning.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture. See  preceding warning.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String has maximum of 8 characters. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and has maximum of 16 characters. |
| `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18. |
| `max(value)` | `{age:max(120)}` | `91` | Integer value maximum of 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 and maximum of 120. |
| `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters `a`-`z`.  Case-insensitive. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression. See tips about defining a regular expression. |
| `required` | `{name:required}` | `Rick\` | 用于强制在 URL 生成过程中存在非参数值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3131">| | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false. Case-insensitive. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture. See  preceding warning.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture. See  preceding warning.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture. See  preceding warning.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture. See  preceding warning.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String has maximum of 8 characters. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and has maximum of 16 characters. |
| `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18. |
| `max(value)` | `{age:max(120)}` | `91` | Integer value maximum of 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 and maximum of 120. |
| `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters `a`-`z`.  Case-insensitive. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression. See tips about defining a regular expression. |
| `required` | `{name:required}` | `Rick\` | Used to enforce that a non-parameter value is present during URL generation.</span></span> |

<span data-ttu-id="6d72c-3132">可向单个参数应用多个由冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3132">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="6d72c-3133">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3133">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="6d72c-3134">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3134">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="6d72c-3135">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3135">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="6d72c-3136">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3136">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="6d72c-3137">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3137">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="6d72c-3138">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3138">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="6d72c-3139">正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-3139">Regular expressions</span></span>

<span data-ttu-id="6d72c-3140">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3140">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="6d72c-3141">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3141">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="6d72c-3142">正则表达式与路由和 C# 语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3142">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="6d72c-3143">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3143">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="6d72c-3144">在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3144">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing:</span></span>

* <span data-ttu-id="6d72c-3145">表达式必须将字符串中提供的单反斜杠 `\` 字符作为源代码中的双反斜杠 `\\` 字符。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3145">The expression must have the single backslash `\` characters provided in the string as double backslash `\\` characters in the source code.</span></span>
* <span data-ttu-id="6d72c-3146">正则表达式必须使用 `\\` 对 `\` 字符串转义字符进行转义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3146">The regular expression must us `\\` in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="6d72c-3147">使用[逐字字符串文本](/dotnet/csharp/language-reference/keywords/string)时，正则表达式不需要 `\\`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3147">The regular expression doesn't require `\\` when using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="6d72c-3148">要对路由参数分隔符 `{`、`}`、`[`、`]` 进行转义，请将表达式 `{{`、`}`、`[[`、`]]` 中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3148">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression `{{`, `}`, `[[`, `]]`.</span></span> <span data-ttu-id="6d72c-3149">下表展示了正则表达式和转义版本：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3149">The following table shows a regular expression and the escaped version:</span></span>

| <span data-ttu-id="6d72c-3150">正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-3150">Regular Expression</span></span>    | <span data-ttu-id="6d72c-3151">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-3151">Escaped Regular Expression</span></span>     |
| ---
<span data-ttu-id="6d72c-3152">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3152">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3153">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3154">'Identity'</span></span>
- <span data-ttu-id="6d72c-3155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3155">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3156">'Razor'</span></span>
- <span data-ttu-id="6d72c-3157">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3158">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3158">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3159">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3160">'Identity'</span></span>
- <span data-ttu-id="6d72c-3161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3161">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3162">'Razor'</span></span>
- <span data-ttu-id="6d72c-3163">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3165">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3166">'Identity'</span></span>
- <span data-ttu-id="6d72c-3167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3167">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3168">'Razor'</span></span>
- <span data-ttu-id="6d72c-3169">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3171">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3172">'Identity'</span></span>
- <span data-ttu-id="6d72c-3173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3173">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3174">'Razor'</span></span>
- <span data-ttu-id="6d72c-3175">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3177">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3178">'Identity'</span></span>
- <span data-ttu-id="6d72c-3179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3179">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3180">'Razor'</span></span>
- <span data-ttu-id="6d72c-3181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3183">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3184">'Identity'</span></span>
- <span data-ttu-id="6d72c-3185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3185">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3186">'Razor'</span></span>
- <span data-ttu-id="6d72c-3187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3188">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3188">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3189">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3190">'Identity'</span></span>
- <span data-ttu-id="6d72c-3191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3191">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3192">'Razor'</span></span>
- <span data-ttu-id="6d72c-3193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3195">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3196">'Identity'</span></span>
- <span data-ttu-id="6d72c-3197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3197">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3198">'Razor'</span></span>
- <span data-ttu-id="6d72c-3199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3199">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3200">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3200">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3201">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3201">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3202">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3202">'Identity'</span></span>
- <span data-ttu-id="6d72c-3203">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3203">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3204">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3204">'Razor'</span></span>
- <span data-ttu-id="6d72c-3205">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3205">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3206">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3206">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3207">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3208">'Identity'</span></span>
- <span data-ttu-id="6d72c-3209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3209">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3210">'Razor'</span></span>
- <span data-ttu-id="6d72c-3211">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3211">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3212">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3212">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3213">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3214">'Identity'</span></span>
- <span data-ttu-id="6d72c-3215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3215">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3216">'Razor'</span></span>
- <span data-ttu-id="6d72c-3217">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3218">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3218">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3219">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3220">'Identity'</span></span>
- <span data-ttu-id="6d72c-3221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3221">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3222">'Razor'</span></span>
- <span data-ttu-id="6d72c-3223">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3224">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3224">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3225">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3226">'Identity'</span></span>
- <span data-ttu-id="6d72c-3227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3227">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3228">'Razor'</span></span>
- <span data-ttu-id="6d72c-3229">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3230">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3230">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3231">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3232">'Identity'</span></span>
- <span data-ttu-id="6d72c-3233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3233">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3234">'Razor'</span></span>
- <span data-ttu-id="6d72c-3235">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3236">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3236">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3237">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3238">'Identity'</span></span>
- <span data-ttu-id="6d72c-3239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3239">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3240">'Razor'</span></span>
- <span data-ttu-id="6d72c-3241">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3243">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3244">'Identity'</span></span>
- <span data-ttu-id="6d72c-3245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3245">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3246">'Razor'</span></span>
- <span data-ttu-id="6d72c-3247">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3249">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3250">'Identity'</span></span>
- <span data-ttu-id="6d72c-3251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3251">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3252">'Razor'</span></span>
- <span data-ttu-id="6d72c-3253">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3255">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3256">'Identity'</span></span>
- <span data-ttu-id="6d72c-3257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3257">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3258">'Razor'</span></span>
- <span data-ttu-id="6d72c-3259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3261">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3262">'Identity'</span></span>
- <span data-ttu-id="6d72c-3263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3263">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3264">'Razor'</span></span>
- <span data-ttu-id="6d72c-3265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3267">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3268">'Identity'</span></span>
- <span data-ttu-id="6d72c-3269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3269">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3270">'Razor'</span></span>
- <span data-ttu-id="6d72c-3271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3273">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3274">'Identity'</span></span>
- <span data-ttu-id="6d72c-3275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3275">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3276">'Razor'</span></span>
- <span data-ttu-id="6d72c-3277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3277">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3278">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="6d72c-3278">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="6d72c-3279">路由中使用的正则表达式通常以脱字号 `^` 开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3279">Regular expressions used in routing often start with the caret `^` character and match starting position of the string.</span></span> <span data-ttu-id="6d72c-3280">表达式通常以美元符号 `$` 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3280">The expressions often end with the dollar sign `$` character and match end of the string.</span></span> <span data-ttu-id="6d72c-3281">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3281">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="6d72c-3282">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3282">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="6d72c-3283">下表提供了示例并说明了它们匹配或匹配失败的原因。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3283">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="6d72c-3284">表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-3284">Expression</span></span>   | <span data-ttu-id="6d72c-3285">String</span><span class="sxs-lookup"><span data-stu-id="6d72c-3285">String</span></span>    | <span data-ttu-id="6d72c-3286">匹配</span><span class="sxs-lookup"><span data-stu-id="6d72c-3286">Match</span></span> | <span data-ttu-id="6d72c-3287">注释</span><span class="sxs-lookup"><span data-stu-id="6d72c-3287">Comment</span></span>               |
| ---
<span data-ttu-id="6d72c-3288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3289">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3290">'Identity'</span></span>
- <span data-ttu-id="6d72c-3291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3291">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3292">'Razor'</span></span>
- <span data-ttu-id="6d72c-3293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3295">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3296">'Identity'</span></span>
- <span data-ttu-id="6d72c-3297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3297">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3298">'Razor'</span></span>
- <span data-ttu-id="6d72c-3299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3301">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3302">'Identity'</span></span>
- <span data-ttu-id="6d72c-3303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3303">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3304">'Razor'</span></span>
- <span data-ttu-id="6d72c-3305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3307">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3308">'Identity'</span></span>
- <span data-ttu-id="6d72c-3309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3309">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3310">'Razor'</span></span>
- <span data-ttu-id="6d72c-3311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3311">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3312">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3312">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3313">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3314">'Identity'</span></span>
- <span data-ttu-id="6d72c-3315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3315">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3316">'Razor'</span></span>
- <span data-ttu-id="6d72c-3317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3319">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3320">'Identity'</span></span>
- <span data-ttu-id="6d72c-3321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3321">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3322">'Razor'</span></span>
- <span data-ttu-id="6d72c-3323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3323">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3324">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3324">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3325">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3326">'Identity'</span></span>
- <span data-ttu-id="6d72c-3327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3327">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3328">'Razor'</span></span>
- <span data-ttu-id="6d72c-3329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3331">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3332">'Identity'</span></span>
- <span data-ttu-id="6d72c-3333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3333">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3334">'Razor'</span></span>
- <span data-ttu-id="6d72c-3335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3337">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3338">'Identity'</span></span>
- <span data-ttu-id="6d72c-3339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3339">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3340">'Razor'</span></span>
- <span data-ttu-id="6d72c-3341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3343">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3344">'Identity'</span></span>
- <span data-ttu-id="6d72c-3345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3345">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3346">'Razor'</span></span>
- <span data-ttu-id="6d72c-3347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3349">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3350">'Identity'</span></span>
- <span data-ttu-id="6d72c-3351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3351">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3352">'Razor'</span></span>
- <span data-ttu-id="6d72c-3353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3355">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3356">'Identity'</span></span>
- <span data-ttu-id="6d72c-3357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3357">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3358">'Razor'</span></span>
- <span data-ttu-id="6d72c-3359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3361">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3362">'Identity'</span></span>
- <span data-ttu-id="6d72c-3363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3363">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3364">'Razor'</span></span>
- <span data-ttu-id="6d72c-3365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3367">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3368">'Identity'</span></span>
- <span data-ttu-id="6d72c-3369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3369">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3370">'Razor'</span></span>
- <span data-ttu-id="6d72c-3371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3371">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3372">---------- | | `[a-z]{2}`   | hello     | 是   | 子字符串匹配      | | `[a-z]{2}`   | 123abc456 | 是   | 子字符串匹配     | | `[a-z]{2}`   | mz        | 是   | 匹配表达式    | | `[a-z]{2}`   | MZ        | 是   | 不区分大小写    | | `^[a-z]{2}$` | hello     | 否    | 请参阅上述的 `^` 和 `$` | | `^[a-z]{2}$` | 123abc456 | 否    | 请参阅上述的 `^` 和 `$` |</span><span class="sxs-lookup"><span data-stu-id="6d72c-3372">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="6d72c-3373">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3373">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="6d72c-3374">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3374">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="6d72c-3375">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3375">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="6d72c-3376">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3376">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="6d72c-3377">已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3377">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="6d72c-3378">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="6d72c-3378">Custom route constraints</span></span>

<span data-ttu-id="6d72c-3379">除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3379">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="6d72c-3380"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3380">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="6d72c-3381">若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3381">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="6d72c-3382"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3382">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="6d72c-3383">应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3383">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="6d72c-3384">例如：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3384">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="6d72c-3385">然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3385">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="6d72c-3386">例如：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3386">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a><span data-ttu-id="6d72c-3387">参数转换器参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-3387">Parameter transformer reference</span></span>

<span data-ttu-id="6d72c-3388">参数转换器：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3388">Parameter transformers:</span></span>

* <span data-ttu-id="6d72c-3389">在为 <xref:Microsoft.AspNetCore.Routing.Route> 生成链接时执行。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3389">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="6d72c-3390">实现 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3390">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="6d72c-3391">使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3391">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="6d72c-3392">获取参数的路由值并将其转换为新的字符串值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3392">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="6d72c-3393">在生成的链接中使用转换后的值的结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3393">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="6d72c-3394">例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3394">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="6d72c-3395">若要在路由模式中使用参数转换器，请先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3395">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="6d72c-3396">框架使用参数转化器来转换进行终结点解析的 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3396">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="6d72c-3397">例如，ASP.NET Core MVC 使用参数转换器来转换用于匹配 `area``controller``action` 和 `page` 的路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3397">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="6d72c-3398">使用上述路由，操作 `SubscriptionManagementController.GetAll` 与 URI `/subscription-management/get-all` 匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3398">With the preceding route, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="6d72c-3399">参数转换器不会更改用于生成链接的路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3399">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="6d72c-3400">例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3400">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="6d72c-3401">对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3401">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="6d72c-3402">ASP.NET Core MVC 还具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3402">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="6d72c-3403">该约定将指定的参数转换器应用于应用中的所有属性路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3403">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="6d72c-3404">在替换属性路径令牌时，参数转换器将转换这些令牌。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3404">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="6d72c-3405">有关详细信息，请参阅[使用参数转换器自定义标记替换](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3405">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* Razor<span data-ttu-id="6d72c-3406"> Pages 具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 约定。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3406"> Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="6d72c-3407">此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3407">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="6d72c-3408">参数转换器转换 Razor Pages 路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3408">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="6d72c-3409">有关详细信息，请参阅[使用参数转换器自定义页面路由](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3409">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

## <a name="url-generation-reference"></a><span data-ttu-id="6d72c-3410">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-3410">URL generation reference</span></span>

<span data-ttu-id="6d72c-3411">以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3411">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="6d72c-3412">上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3412">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="6d72c-3413">字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3413">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="6d72c-3414">有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3414">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="6d72c-3415"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3415">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="6d72c-3416">由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3416">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="6d72c-3417">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3417">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="6d72c-3418">在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3418">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="6d72c-3419">忽略与参数不匹配的环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3419">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="6d72c-3420">在显式提供的值会覆盖环境值的情况下，也会忽略环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3420">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="6d72c-3421">在 URL 中将从左到右进行匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3421">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="6d72c-3422">显式提供但与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3422">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="6d72c-3423">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3423">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="6d72c-3424">环境值</span><span class="sxs-lookup"><span data-stu-id="6d72c-3424">Ambient Values</span></span>                     | <span data-ttu-id="6d72c-3425">显式值</span><span class="sxs-lookup"><span data-stu-id="6d72c-3425">Explicit Values</span></span>                        | <span data-ttu-id="6d72c-3426">结果</span><span class="sxs-lookup"><span data-stu-id="6d72c-3426">Result</span></span>                  |
| ---
<span data-ttu-id="6d72c-3427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3428">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3429">'Identity'</span></span>
- <span data-ttu-id="6d72c-3430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3430">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3431">'Razor'</span></span>
- <span data-ttu-id="6d72c-3432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3434">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3435">'Identity'</span></span>
- <span data-ttu-id="6d72c-3436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3436">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3437">'Razor'</span></span>
- <span data-ttu-id="6d72c-3438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3440">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3441">'Identity'</span></span>
- <span data-ttu-id="6d72c-3442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3442">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3443">'Razor'</span></span>
- <span data-ttu-id="6d72c-3444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3446">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3447">'Identity'</span></span>
- <span data-ttu-id="6d72c-3448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3448">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3449">'Razor'</span></span>
- <span data-ttu-id="6d72c-3450">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3452">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3453">'Identity'</span></span>
- <span data-ttu-id="6d72c-3454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3454">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3455">'Razor'</span></span>
- <span data-ttu-id="6d72c-3456">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3458">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3459">'Identity'</span></span>
- <span data-ttu-id="6d72c-3460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3460">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3461">'Razor'</span></span>
- <span data-ttu-id="6d72c-3462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3464">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3465">'Identity'</span></span>
- <span data-ttu-id="6d72c-3466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3466">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3467">'Razor'</span></span>
- <span data-ttu-id="6d72c-3468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3470">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3471">'Identity'</span></span>
- <span data-ttu-id="6d72c-3472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3472">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3473">'Razor'</span></span>
- <span data-ttu-id="6d72c-3474">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3476">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3477">'Identity'</span></span>
- <span data-ttu-id="6d72c-3478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3478">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3479">'Razor'</span></span>
- <span data-ttu-id="6d72c-3480">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3482">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3483">'Identity'</span></span>
- <span data-ttu-id="6d72c-3484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3484">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3485">'Razor'</span></span>
- <span data-ttu-id="6d72c-3486">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3488">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3489">'Identity'</span></span>
- <span data-ttu-id="6d72c-3490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3490">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3491">'Razor'</span></span>
- <span data-ttu-id="6d72c-3492">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3492">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3493">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3493">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3494">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3495">'Identity'</span></span>
- <span data-ttu-id="6d72c-3496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3496">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3497">'Razor'</span></span>
- <span data-ttu-id="6d72c-3498">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3498">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3499">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3499">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3500">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3500">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3501">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3501">'Identity'</span></span>
- <span data-ttu-id="6d72c-3502">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3502">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3503">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3503">'Razor'</span></span>
- <span data-ttu-id="6d72c-3504">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3504">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3505">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3505">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3506">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3506">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3507">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3507">'Identity'</span></span>
- <span data-ttu-id="6d72c-3508">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3508">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3509">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3509">'Razor'</span></span>
- <span data-ttu-id="6d72c-3510">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3510">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3511">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3511">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3512">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3512">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3513">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3513">'Identity'</span></span>
- <span data-ttu-id="6d72c-3514">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3514">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3515">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3515">'Razor'</span></span>
- <span data-ttu-id="6d72c-3516">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3516">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3517">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3517">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3518">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3518">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3519">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3519">'Identity'</span></span>
- <span data-ttu-id="6d72c-3520">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3520">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3521">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3521">'Razor'</span></span>
- <span data-ttu-id="6d72c-3522">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3522">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3523">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3523">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3524">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3525">'Identity'</span></span>
- <span data-ttu-id="6d72c-3526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3526">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3527">'Razor'</span></span>
- <span data-ttu-id="6d72c-3528">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3529">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3529">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3530">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3530">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3531">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3531">'Identity'</span></span>
- <span data-ttu-id="6d72c-3532">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3532">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3533">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3533">'Razor'</span></span>
- <span data-ttu-id="6d72c-3534">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3534">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3535">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3535">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3536">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3536">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3537">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3537">'Identity'</span></span>
- <span data-ttu-id="6d72c-3538">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3538">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3539">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3539">'Razor'</span></span>
- <span data-ttu-id="6d72c-3540">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3540">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3542">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3543">'Identity'</span></span>
- <span data-ttu-id="6d72c-3544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3544">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3545">'Razor'</span></span>
- <span data-ttu-id="6d72c-3546">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3548">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3549">'Identity'</span></span>
- <span data-ttu-id="6d72c-3550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3550">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3551">'Razor'</span></span>
- <span data-ttu-id="6d72c-3552">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3554">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3555">'Identity'</span></span>
- <span data-ttu-id="6d72c-3556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3556">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3557">'Razor'</span></span>
- <span data-ttu-id="6d72c-3558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3560">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3561">'Identity'</span></span>
- <span data-ttu-id="6d72c-3562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3562">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3563">'Razor'</span></span>
- <span data-ttu-id="6d72c-3564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3566">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3567">'Identity'</span></span>
- <span data-ttu-id="6d72c-3568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3568">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3569">'Razor'</span></span>
- <span data-ttu-id="6d72c-3570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3572">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3573">'Identity'</span></span>
- <span data-ttu-id="6d72c-3574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3574">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3575">'Razor'</span></span>
- <span data-ttu-id="6d72c-3576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3578">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3579">'Identity'</span></span>
- <span data-ttu-id="6d72c-3580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3580">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3581">'Razor'</span></span>
- <span data-ttu-id="6d72c-3582">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3584">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3585">'Identity'</span></span>
- <span data-ttu-id="6d72c-3586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3586">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3587">'Razor'</span></span>
- <span data-ttu-id="6d72c-3588">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3590">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3591">'Identity'</span></span>
- <span data-ttu-id="6d72c-3592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3592">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3593">'Razor'</span></span>
- <span data-ttu-id="6d72c-3594">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3594">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3595">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3595">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3596">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3596">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3597">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3597">'Identity'</span></span>
- <span data-ttu-id="6d72c-3598">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3598">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3599">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3599">'Razor'</span></span>
- <span data-ttu-id="6d72c-3600">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3600">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3602">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3603">'Identity'</span></span>
- <span data-ttu-id="6d72c-3604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3604">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3605">'Razor'</span></span>
- <span data-ttu-id="6d72c-3606">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3608">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3609">'Identity'</span></span>
- <span data-ttu-id="6d72c-3610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3610">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3611">'Razor'</span></span>
- <span data-ttu-id="6d72c-3612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3614">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3615">'Identity'</span></span>
- <span data-ttu-id="6d72c-3616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3616">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3617">'Razor'</span></span>
- <span data-ttu-id="6d72c-3618">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3618">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3619">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3619">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3620">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3621">'Identity'</span></span>
- <span data-ttu-id="6d72c-3622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3622">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3623">'Razor'</span></span>
- <span data-ttu-id="6d72c-3624">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3626">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3627">'Identity'</span></span>
- <span data-ttu-id="6d72c-3628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3628">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3629">'Razor'</span></span>
- <span data-ttu-id="6d72c-3630">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3632">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3633">'Identity'</span></span>
- <span data-ttu-id="6d72c-3634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3634">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3635">'Razor'</span></span>
- <span data-ttu-id="6d72c-3636">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3638">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3639">'Identity'</span></span>
- <span data-ttu-id="6d72c-3640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3640">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3641">'Razor'</span></span>
- <span data-ttu-id="6d72c-3642">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3644">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3645">'Identity'</span></span>
- <span data-ttu-id="6d72c-3646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3646">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3647">'Razor'</span></span>
- <span data-ttu-id="6d72c-3648">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3650">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3651">'Identity'</span></span>
- <span data-ttu-id="6d72c-3652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3652">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3653">'Razor'</span></span>
- <span data-ttu-id="6d72c-3654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3656">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3657">'Identity'</span></span>
- <span data-ttu-id="6d72c-3658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3658">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3659">'Razor'</span></span>
- <span data-ttu-id="6d72c-3660">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3662">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3663">'Identity'</span></span>
- <span data-ttu-id="6d72c-3664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3664">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3665">'Razor'</span></span>
- <span data-ttu-id="6d72c-3666">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3668">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3669">'Identity'</span></span>
- <span data-ttu-id="6d72c-3670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3670">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3671">'Razor'</span></span>
- <span data-ttu-id="6d72c-3672">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3672">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3673">------------ | | 控制器 =“Home”                | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 控制器 =“Order”，操作 =“About” | `/Order/About`          |
| 控制器 = “Home”，颜色 = “Red” | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 操作 =“About”，颜色 =“Red”        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="6d72c-3673">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

<span data-ttu-id="6d72c-3674">如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3674">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="6d72c-3675">当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3675">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="6d72c-3676">复杂段</span><span class="sxs-lookup"><span data-stu-id="6d72c-3676">Complex segments</span></span>

<span data-ttu-id="6d72c-3677">复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3677">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="6d72c-3678">请参阅[此代码](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3678">See [this code](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="6d72c-3679">ASP.NET Core 无法使用[代码示例](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3679">The [code sample](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6d72c-3680">路由负责将请求 URI 映射到路由处理程序和调度传入的请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3680">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="6d72c-3681">路由在应用中定义，并在应用启动时进行配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3681">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="6d72c-3682">路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3682">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="6d72c-3683">如果使用应用中配置的路由，路由能生成映射到路由处理程序的 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3683">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

<span data-ttu-id="6d72c-3684">要在 ASP.NET Core 2.1 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3684">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> <span data-ttu-id="6d72c-3685">本文档介绍较低级别的 ASP.NET Core 路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3685">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="6d72c-3686">有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3686">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="6d72c-3687">有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3687">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="6d72c-3688">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6d72c-3688">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="6d72c-3689">路由基础知识</span><span class="sxs-lookup"><span data-stu-id="6d72c-3689">Routing basics</span></span>

<span data-ttu-id="6d72c-3690">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3690">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="6d72c-3691">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3691">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="6d72c-3692">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3692">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="6d72c-3693">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3693">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="6d72c-3694">开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3694">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="6d72c-3695">Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3695">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="6d72c-3696">也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3696">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="6d72c-3697">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3697">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

Razor<span data-ttu-id="6d72c-3698"> Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3698"> Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="6d72c-3699">还可以使用其他约定来自定义 Razor Pages 路由行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3699">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="6d72c-3700">有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3700">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="6d72c-3701">借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3701">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="6d72c-3702">此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3702">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="6d72c-3703">路由使用 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由实现来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3703">Routing uses routes implementations of <xref:Microsoft.AspNetCore.Routing.IRouter> to:</span></span>

* <span data-ttu-id="6d72c-3704">将传入请求映射到路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3704">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="6d72c-3705">生成响应中使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3705">Generate the URLs used in responses.</span></span>

<span data-ttu-id="6d72c-3706">默认情况下，应用只有一个路由集合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3706">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="6d72c-3707">当请求到达时，将按照路由在集合中的存在顺序来处理路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3707">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="6d72c-3708">框架试图通过在集合中的每个路由上调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法，将传入请求的 URL 与集合中的路由进行匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3708">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="6d72c-3709">响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3709">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="6d72c-3710">路由系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3710">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="6d72c-3711">路由模板语法用于通过标记化路由参数来定义路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3711">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="6d72c-3712">可以使用常规样式和属性样式终结点配置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3712">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="6d72c-3713"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3713"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="6d72c-3714">应用模型（如 MVC/Razor Pages）注册了其所有具有可预测的路由方案实现的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3714">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="6d72c-3715">响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3715">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="6d72c-3716">URL 生成基于支持任意可扩展性的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3716">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="6d72c-3717"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 提供生成 URL 的方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3717"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
<span data-ttu-id="6d72c-3718">路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3718">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="6d72c-3719">[ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3719">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="6d72c-3720">要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3720">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="6d72c-3721">URL 匹配</span><span class="sxs-lookup"><span data-stu-id="6d72c-3721">URL matching</span></span>

<span data-ttu-id="6d72c-3722">URL 匹配是一个过程，通过该过程，路由可向处理程序调度传入请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3722">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="6d72c-3723">此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3723">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="6d72c-3724">向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3724">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="6d72c-3725">传入请求将进入 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>，后者将对序列中的每个路由调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3725">Incoming requests enter the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="6d72c-3726"><xref:Microsoft.AspNetCore.Routing.IRouter> 实例将选择是否通过将 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) 设置为非 NULL <xref:Microsoft.AspNetCore.Http.RequestDelegate> 来处理请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3726">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="6d72c-3727">如果路由为请求设置处理程序，将停止路由处理，并调用处理程序来处理该请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3727">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="6d72c-3728">如果未找到用于处理请求的路由处理程序，中间件会将请求传递给请求管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3728">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="6d72c-3729"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的主要输入是与当前请求关联的 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3729">The primary input to <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="6d72c-3730">[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 和 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) 是路由匹配后设置的输出。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3730">The [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="6d72c-3731">调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的匹配还可根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3731">A match that calls <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> also sets the properties of the [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="6d72c-3732">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3732">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="6d72c-3733">这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3733">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="6d72c-3734">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3734">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="6d72c-3735">提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3735"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="6d72c-3736">这些值是开发者定义的，不会影响通过任何方式路由的行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3736">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="6d72c-3737">此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3737">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="6d72c-3738">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3738">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="6d72c-3739">路由可以相互嵌套。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3739">Routes can be nested inside of one another.</span></span> <span data-ttu-id="6d72c-3740"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3740">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="6d72c-3741">通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3741">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="6d72c-3742"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3742">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation"></a><span data-ttu-id="6d72c-3743">URL 生成</span><span class="sxs-lookup"><span data-stu-id="6d72c-3743">URL generation</span></span>

<span data-ttu-id="6d72c-3744">URL 生成是通过其可根据一组路由值创建 URL 路径的过程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3744">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="6d72c-3745">这需要考虑处理程序与访问它们的 URL 之间的逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3745">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="6d72c-3746">URL 遵循类似的迭代过程，但开头是将用户或框架代码调用到路由集合的 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3746">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="6d72c-3747">每个路由按顺序调用其 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法，直到返回非 NULL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3747">Each *route* has its <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="6d72c-3748"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的主输入有：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3748">The primary inputs to <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> are:</span></span>

* [<span data-ttu-id="6d72c-3749">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="6d72c-3749">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [<span data-ttu-id="6d72c-3750">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="6d72c-3750">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [<span data-ttu-id="6d72c-3751">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="6d72c-3751">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

<span data-ttu-id="6d72c-3752">路由主要使用 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 和 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 提供的路由值来确定是否可能生成 URL 以及要包括哪些值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3752">Routes primarily use the route values provided by <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> and <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="6d72c-3753"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 是通过匹配当前请求而生成的路由值集。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3753">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="6d72c-3754">与此相反，<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 是指定如何为当前操作生成所需 URL 的路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3754">In contrast, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="6d72c-3755">如果路由应获取与当前上下文关联的服务或其他数据时，则提供 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3755">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="6d72c-3756">将 [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) 视为 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) 的一组替代。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3756">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="6d72c-3757">URL 生成尝试重复使用当前请求中的路由值，以便使用相同路由或路由值生成链接的 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3757">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="6d72c-3758"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的输出是 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3758">The output of <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is a <xref:Microsoft.AspNetCore.Routing.VirtualPathData>.</span></span> <span data-ttu-id="6d72c-3759"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> 是 <xref:Microsoft.AspNetCore.Routing.RouteData> 的并行值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3759"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> is a parallel of <xref:Microsoft.AspNetCore.Routing.RouteData>.</span></span> <span data-ttu-id="6d72c-3760"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> 包含输出 URL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 以及路由应该设置的某些其他属性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3760"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> contains the <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="6d72c-3761">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 属性包含路由生成的虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3761">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="6d72c-3762">你可能需要进一步处理路径，具体取决于你的需求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3762">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="6d72c-3763">如果要在 HTML 中呈现生成的 URL，请预置应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3763">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="6d72c-3764">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) 是对已成功生成 URL 的路由的引用。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3764">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="6d72c-3765">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 属性是生成 URL 的路由的其他相关数据的字典。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3765">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="6d72c-3766">这是 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 的并性值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3766">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

### <a name="create-routes"></a><span data-ttu-id="6d72c-3767">创建路由</span><span class="sxs-lookup"><span data-stu-id="6d72c-3767">Create routes</span></span>

<span data-ttu-id="6d72c-3768">路由提供 <xref:Microsoft.AspNetCore.Routing.Route> 类，作为 <xref:Microsoft.AspNetCore.Routing.IRouter> 的标准实现。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3768">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="6d72c-3769"><xref:Microsoft.AspNetCore.Routing.Route> 使用 route template 语法来定义模式，以便在调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 时匹配 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3769"><xref:Microsoft.AspNetCore.Routing.Route> uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="6d72c-3770">调用 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 时，<xref:Microsoft.AspNetCore.Routing.Route> 使用同一路由模板生成 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3770"><xref:Microsoft.AspNetCore.Routing.Route> uses the same route template to generate a URL when <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is called.</span></span>

<span data-ttu-id="6d72c-3771">大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3771">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="6d72c-3772">任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3772">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="6d72c-3773"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3773"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="6d72c-3774"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3774"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="6d72c-3775">默认处理程序是 `IRouter`，该处理程序可能无法处理该请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3775">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="6d72c-3776">例如，ASP.NET Core MVC 通常被配置为默认处理程序，仅处理与可用控制器和操作匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3776">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="6d72c-3777">要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3777">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="6d72c-3778">以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3778">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="6d72c-3779">此模板与 URL 路径相匹配，并且提取路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3779">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="6d72c-3780">例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3780">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="6d72c-3781">路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3781">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="6d72c-3782">路由参数已命名。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3782">Route parameters are named.</span></span> <span data-ttu-id="6d72c-3783">参数通过将参数名称括在大括号 `{ ... }` 中来定义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3783">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="6d72c-3784">上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3784">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="6d72c-3785">这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3785">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="6d72c-3786">路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3786">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="6d72c-3787">路由参数名称后面的问号 (`?`) 定义了可选参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3787">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="6d72c-3788">路由匹配时，具有默认值的路由参数始终会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3788">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="6d72c-3789">如果没有相应的 URL 路径段，则可选参数不会生成路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3789">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="6d72c-3790">有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3790">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="6d72c-3791">在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3791">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="6d72c-3792">此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3792">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="6d72c-3793">路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3793">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="6d72c-3794">在此示例中，路由值 `id` 必须可转换为整数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3794">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="6d72c-3795">有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3795">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="6d72c-3796">其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3796">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="6d72c-3797">这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3797">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="6d72c-3798">以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3798">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

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
> <span data-ttu-id="6d72c-3799">定义约束和默认值的内联语法对于简单路由很方便。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3799">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="6d72c-3800">但是，存在内联语法不支持的方案，例如数据令牌。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3800">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="6d72c-3801">以下示例演示了一些其他方案：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3801">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="6d72c-3802">上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3802">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="6d72c-3803">`controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3803">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="6d72c-3804">可在路由模板中指定默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3804">Default values can be specified in the route template.</span></span> <span data-ttu-id="6d72c-3805">根据路由参数名称前的星号 (`*`) 外观，`article` 路由参数被定义为 catch-all。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3805">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="6d72c-3806">全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3806">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="6d72c-3807">以下示例添加了路由约束和数据令牌：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3807">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="6d72c-3808">上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3808">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="6d72c-3810">路由类 URL 生成</span><span class="sxs-lookup"><span data-stu-id="6d72c-3810">Route class URL generation</span></span>

<span data-ttu-id="6d72c-3811"><xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3811">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="6d72c-3812">从逻辑上来说，这是匹配 URL 路径的反向过程。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3812">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="6d72c-3813">为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3813">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="6d72c-3814">将生成什么值？</span><span class="sxs-lookup"><span data-stu-id="6d72c-3814">What values would be produced?</span></span> <span data-ttu-id="6d72c-3815">这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3815">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="6d72c-3816">以下示例使用常规 ASP.NET Core MVC 默认路由：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3816">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="6d72c-3817">使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3817">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="6d72c-3818">路由值将替换为相应的路由参数，以形成 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3818">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="6d72c-3819">由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3819">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="6d72c-3820">使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3820">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="6d72c-3821">提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3821">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="6d72c-3822">已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3822">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="6d72c-3823">使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3823">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="6d72c-3824">有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3824">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="6d72c-3825">使用路由中间件</span><span class="sxs-lookup"><span data-stu-id="6d72c-3825">Use routing middleware</span></span>

<span data-ttu-id="6d72c-3826">引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3826">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="6d72c-3827">将路由添加到 `Startup.ConfigureServices` 中的服务容器：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3827">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="6d72c-3828">必须在 `Startup.Configure` 方法中配置路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3828">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="6d72c-3829">该示例应用使用以下 API：</span><span class="sxs-lookup"><span data-stu-id="6d72c-3829">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="6d72c-3830"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>：仅匹配 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3830"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="6d72c-3831">下表显示了具有给定 URI 的响应。</span><span class="sxs-lookup"><span data-stu-id="6d72c-3831">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="6d72c-3832">URI</span><span class="sxs-lookup"><span data-stu-id="6d72c-3832">URI</span></span>                    | <span data-ttu-id="6d72c-3833">响应</span><span class="sxs-lookup"><span data-stu-id="6d72c-3833">Response</span></span>                                          |
| ---
<span data-ttu-id="6d72c-3834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3835">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3836">'Identity'</span></span>
- <span data-ttu-id="6d72c-3837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3837">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3838">'Razor'</span></span>
- <span data-ttu-id="6d72c-3839">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3841">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3842">'Identity'</span></span>
- <span data-ttu-id="6d72c-3843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3843">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3844">'Razor'</span></span>
- <span data-ttu-id="6d72c-3845">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3847">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3848">'Identity'</span></span>
- <span data-ttu-id="6d72c-3849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3849">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3850">'Razor'</span></span>
- <span data-ttu-id="6d72c-3851">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3853">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3854">'Identity'</span></span>
- <span data-ttu-id="6d72c-3855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3855">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3856">'Razor'</span></span>
- <span data-ttu-id="6d72c-3857">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3859">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3860">'Identity'</span></span>
- <span data-ttu-id="6d72c-3861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3861">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3862">'Razor'</span></span>
- <span data-ttu-id="6d72c-3863">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3865">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3866">'Identity'</span></span>
- <span data-ttu-id="6d72c-3867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3867">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3868">'Razor'</span></span>
- <span data-ttu-id="6d72c-3869">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3871">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3872">'Identity'</span></span>
- <span data-ttu-id="6d72c-3873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3873">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3874">'Razor'</span></span>
- <span data-ttu-id="6d72c-3875">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3877">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3878">'Identity'</span></span>
- <span data-ttu-id="6d72c-3879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3879">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3880">'Razor'</span></span>
- <span data-ttu-id="6d72c-3881">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3883">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3884">'Identity'</span></span>
- <span data-ttu-id="6d72c-3885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3885">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3886">'Razor'</span></span>
- <span data-ttu-id="6d72c-3887">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3887">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-3888">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3888">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3889">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3890">'Identity'</span></span>
- <span data-ttu-id="6d72c-3891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3891">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3892">'Razor'</span></span>
- <span data-ttu-id="6d72c-3893">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3895">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3896">'Identity'</span></span>
- <span data-ttu-id="6d72c-3897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3897">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3898">'Razor'</span></span>
- <span data-ttu-id="6d72c-3899">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3901">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3902">'Identity'</span></span>
- <span data-ttu-id="6d72c-3903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3903">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3904">'Razor'</span></span>
- <span data-ttu-id="6d72c-3905">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3907">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3908">'Identity'</span></span>
- <span data-ttu-id="6d72c-3909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3909">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3910">'Razor'</span></span>
- <span data-ttu-id="6d72c-3911">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3913">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3914">'Identity'</span></span>
- <span data-ttu-id="6d72c-3915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3915">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3916">'Razor'</span></span>
- <span data-ttu-id="6d72c-3917">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3917">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3918">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3918">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3919">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3919">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3920">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3920">'Identity'</span></span>
- <span data-ttu-id="6d72c-3921">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3921">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3922">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3922">'Razor'</span></span>
- <span data-ttu-id="6d72c-3923">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3923">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3924">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3924">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3925">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3925">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3926">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3926">'Identity'</span></span>
- <span data-ttu-id="6d72c-3927">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3927">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3928">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3928">'Razor'</span></span>
- <span data-ttu-id="6d72c-3929">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3929">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3930">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3930">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3931">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3931">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3932">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3932">'Identity'</span></span>
- <span data-ttu-id="6d72c-3933">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3933">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3934">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3934">'Razor'</span></span>
- <span data-ttu-id="6d72c-3935">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3935">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3936">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3936">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3937">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3937">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3938">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3938">'Identity'</span></span>
- <span data-ttu-id="6d72c-3939">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3939">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3940">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3940">'Razor'</span></span>
- <span data-ttu-id="6d72c-3941">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3941">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3942">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3942">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3943">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3943">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3944">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3944">'Identity'</span></span>
- <span data-ttu-id="6d72c-3945">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3945">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3946">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3946">'Razor'</span></span>
- <span data-ttu-id="6d72c-3947">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3947">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3948">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3948">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3949">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3949">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3950">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3950">'Identity'</span></span>
- <span data-ttu-id="6d72c-3951">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3951">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3952">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3952">'Razor'</span></span>
- <span data-ttu-id="6d72c-3953">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3953">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3954">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3954">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3955">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3955">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3956">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3956">'Identity'</span></span>
- <span data-ttu-id="6d72c-3957">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3957">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3958">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3958">'Razor'</span></span>
- <span data-ttu-id="6d72c-3959">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3959">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3960">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3960">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3961">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3961">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3962">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3962">'Identity'</span></span>
- <span data-ttu-id="6d72c-3963">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3963">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3964">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3964">'Razor'</span></span>
- <span data-ttu-id="6d72c-3965">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3965">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3966">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3966">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3967">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3967">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3968">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3968">'Identity'</span></span>
- <span data-ttu-id="6d72c-3969">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3969">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3970">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3970">'Razor'</span></span>
- <span data-ttu-id="6d72c-3971">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3971">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3972">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3972">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3973">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3973">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3974">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3974">'Identity'</span></span>
- <span data-ttu-id="6d72c-3975">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3975">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3976">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3976">'Razor'</span></span>
- <span data-ttu-id="6d72c-3977">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3977">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3978">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3978">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3979">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3979">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3980">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3980">'Identity'</span></span>
- <span data-ttu-id="6d72c-3981">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3981">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3982">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3982">'Razor'</span></span>
- <span data-ttu-id="6d72c-3983">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3983">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3984">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3984">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3985">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3985">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3986">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3986">'Identity'</span></span>
- <span data-ttu-id="6d72c-3987">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3987">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3988">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3988">'Razor'</span></span>
- <span data-ttu-id="6d72c-3989">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3989">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3990">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3990">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3991">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3991">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3992">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3992">'Identity'</span></span>
- <span data-ttu-id="6d72c-3993">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3993">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-3994">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3994">'Razor'</span></span>
- <span data-ttu-id="6d72c-3995">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3995">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-3996">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-3996">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-3997">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3997">'Blazor'</span></span>
- <span data-ttu-id="6d72c-3998">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3998">'Identity'</span></span>
- <span data-ttu-id="6d72c-3999">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-3999">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4000">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4000">'Razor'</span></span>
- <span data-ttu-id="6d72c-4001">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4001">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4002">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4002">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4003">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4003">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4004">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4004">'Identity'</span></span>
- <span data-ttu-id="6d72c-4005">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4005">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4006">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4006">'Razor'</span></span>
- <span data-ttu-id="6d72c-4007">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4007">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4008">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4008">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4009">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4009">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4010">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4010">'Identity'</span></span>
- <span data-ttu-id="6d72c-4011">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4011">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4012">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4012">'Razor'</span></span>
- <span data-ttu-id="6d72c-4013">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4013">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4014">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4014">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4015">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4015">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4016">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4016">'Identity'</span></span>
- <span data-ttu-id="6d72c-4017">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4017">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4018">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4018">'Razor'</span></span>
- <span data-ttu-id="6d72c-4019">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4019">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4020">------------------------- | | `/package/create/3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="6d72c-4020">------------------------- | | `/package/create/3`    | Hello!</span></span> <span data-ttu-id="6d72c-4021">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="6d72c-4021">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span></span> <span data-ttu-id="6d72c-4022">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span><span class="sxs-lookup"><span data-stu-id="6d72c-4022">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span></span> <span data-ttu-id="6d72c-4023">Route values: [operation, track], [id, -3] | | `/package/track/`      | 请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4023">Route values: [operation, track], [id, -3] | | `/package/track/`      | The request falls through, no match.</span></span>              <span data-ttu-id="6d72c-4024">| | `GET /hello/Joe`       | Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="6d72c-4024">| | `GET /hello/Joe`       | Hi, Joe!</span></span>                                          <span data-ttu-id="6d72c-4025">| | `POST /hello/Joe`      | 请求失败，仅匹配 HTTP GET。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4025">| | `POST /hello/Joe`      | The request falls through, matches HTTP GET only.</span></span> <span data-ttu-id="6d72c-4026">| | `GET /hello/Joe/Smith` | 请求失败，不匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4026">| | `GET /hello/Joe/Smith` | The request falls through, no match.</span></span>              |

<span data-ttu-id="6d72c-4027">如果要配置单个路由，请在 `IRouter` 实例中调用 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> 传入。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4027">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="6d72c-4028">无需使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4028">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

<span data-ttu-id="6d72c-4029">框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="6d72c-4029">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

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

<span data-ttu-id="6d72c-4030">一些列出的方法（如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>）需要 <xref:Microsoft.AspNetCore.Http.RequestDelegate>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4030">Some of listed methods, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, require a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="6d72c-4031">路由匹配时，<xref:Microsoft.AspNetCore.Http.RequestDelegate> 会用作路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4031">The <xref:Microsoft.AspNetCore.Http.RequestDelegate> is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="6d72c-4032">此系列中的其他方法允许配置中间件管道，将其用作路由处理程序。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4032">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="6d72c-4033">如果 `Map*` 方法不接受处理程序（例如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>），则它将使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4033">If the `Map*` method doesn't accept a handler, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

<span data-ttu-id="6d72c-4034">`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4034">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="6d72c-4035">有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4035">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="6d72c-4036">路由模板参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-4036">Route template reference</span></span>

<span data-ttu-id="6d72c-4037">如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4037">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="6d72c-4038">可在路由段中定义多个路由参数，但必须由文本值隔开。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4038">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="6d72c-4039">例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4039">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="6d72c-4040">这些路由参数必须具有名称，且可能指定了其他属性。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4040">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="6d72c-4041">路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4041">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="6d72c-4042">文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4042">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="6d72c-4043">要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4043">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="6d72c-4044">尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4044">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="6d72c-4045">例如，考虑模板 `files/{filename}.{ext?}`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4045">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="6d72c-4046">当 `filename` 和 `ext` 的值都存在时，将填充这两个值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4046">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="6d72c-4047">如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4047">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="6d72c-4048">以下 URL 与此路由相匹配：</span><span class="sxs-lookup"><span data-stu-id="6d72c-4048">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="6d72c-4049">可以使用星号 (`*`) 作为路由参数的前缀，以绑定到 URI 的其余部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4049">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="6d72c-4050">这称为 catch-all 参数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4050">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="6d72c-4051">例如，`blog/{*slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4051">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="6d72c-4052">全方位参数还可以匹配空字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4052">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="6d72c-4053">使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4053">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="6d72c-4054">例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4054">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="6d72c-4055">请注意转义的正斜杠。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4055">Note the escaped forward slash.</span></span>

<span data-ttu-id="6d72c-4056">路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4056">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="6d72c-4057">例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4057">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="6d72c-4058">如果参数的 URL 中不存在任何值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4058">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="6d72c-4059">通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4059">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="6d72c-4060">可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4060">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="6d72c-4061">路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4061">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="6d72c-4062">在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4062">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="6d72c-4063">如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4063">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="6d72c-4064">通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4064">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="6d72c-4065">约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4065">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="6d72c-4066">例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4066">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="6d72c-4067">有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4067">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="6d72c-4068">下表演示了示例路由模板及其行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4068">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="6d72c-4069">路由模板</span><span class="sxs-lookup"><span data-stu-id="6d72c-4069">Route Template</span></span>                           | <span data-ttu-id="6d72c-4070">示例匹配 URI</span><span class="sxs-lookup"><span data-stu-id="6d72c-4070">Example Matching URI</span></span>    | <span data-ttu-id="6d72c-4071">请求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="6d72c-4071">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="6d72c-4072">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4072">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4073">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4073">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4074">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4074">'Identity'</span></span>
- <span data-ttu-id="6d72c-4075">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4075">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4076">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4076">'Razor'</span></span>
- <span data-ttu-id="6d72c-4077">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4077">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4078">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4078">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4079">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4079">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4080">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4080">'Identity'</span></span>
- <span data-ttu-id="6d72c-4081">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4081">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4082">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4082">'Razor'</span></span>
- <span data-ttu-id="6d72c-4083">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4083">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4084">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4084">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4085">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4085">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4086">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4086">'Identity'</span></span>
- <span data-ttu-id="6d72c-4087">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4087">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4088">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4088">'Razor'</span></span>
- <span data-ttu-id="6d72c-4089">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4089">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4090">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4090">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4091">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4091">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4092">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4092">'Identity'</span></span>
- <span data-ttu-id="6d72c-4093">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4093">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4094">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4094">'Razor'</span></span>
- <span data-ttu-id="6d72c-4095">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4095">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4096">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4096">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4097">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4097">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4098">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4098">'Identity'</span></span>
- <span data-ttu-id="6d72c-4099">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4099">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4100">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4100">'Razor'</span></span>
- <span data-ttu-id="6d72c-4101">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4101">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4102">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4102">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4103">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4104">'Identity'</span></span>
- <span data-ttu-id="6d72c-4105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4106">'Razor'</span></span>
- <span data-ttu-id="6d72c-4107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4107">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4108">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4108">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4109">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4109">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4110">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4110">'Identity'</span></span>
- <span data-ttu-id="6d72c-4111">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4111">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4112">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4112">'Razor'</span></span>
- <span data-ttu-id="6d72c-4113">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4113">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4115">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4115">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4116">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4116">'Identity'</span></span>
- <span data-ttu-id="6d72c-4117">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4117">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4118">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4118">'Razor'</span></span>
- <span data-ttu-id="6d72c-4119">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4119">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4120">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4120">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4121">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4121">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4122">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4122">'Identity'</span></span>
- <span data-ttu-id="6d72c-4123">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4123">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4124">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4124">'Razor'</span></span>
- <span data-ttu-id="6d72c-4125">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4125">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4127">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4128">'Identity'</span></span>
- <span data-ttu-id="6d72c-4129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4129">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4130">'Razor'</span></span>
- <span data-ttu-id="6d72c-4131">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4133">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4134">'Identity'</span></span>
- <span data-ttu-id="6d72c-4135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4135">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4136">'Razor'</span></span>
- <span data-ttu-id="6d72c-4137">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4139">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4140">'Identity'</span></span>
- <span data-ttu-id="6d72c-4141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4141">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4142">'Razor'</span></span>
- <span data-ttu-id="6d72c-4143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4145">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4146">'Identity'</span></span>
- <span data-ttu-id="6d72c-4147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4147">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4148">'Razor'</span></span>
- <span data-ttu-id="6d72c-4149">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4151">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4152">'Identity'</span></span>
- <span data-ttu-id="6d72c-4153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4153">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4154">'Razor'</span></span>
- <span data-ttu-id="6d72c-4155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4157">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4158">'Identity'</span></span>
- <span data-ttu-id="6d72c-4159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4159">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4160">'Razor'</span></span>
- <span data-ttu-id="6d72c-4161">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4163">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4164">'Identity'</span></span>
- <span data-ttu-id="6d72c-4165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4165">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4166">'Razor'</span></span>
- <span data-ttu-id="6d72c-4167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4169">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4170">'Identity'</span></span>
- <span data-ttu-id="6d72c-4171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4171">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4172">'Razor'</span></span>
- <span data-ttu-id="6d72c-4173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4175">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4176">'Identity'</span></span>
- <span data-ttu-id="6d72c-4177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4177">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4178">'Razor'</span></span>
- <span data-ttu-id="6d72c-4179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4179">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4180">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4180">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4181">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4182">'Identity'</span></span>
- <span data-ttu-id="6d72c-4183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4183">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4184">'Razor'</span></span>
- <span data-ttu-id="6d72c-4185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4186">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4186">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4187">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4188">'Identity'</span></span>
- <span data-ttu-id="6d72c-4189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4189">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4190">'Razor'</span></span>
- <span data-ttu-id="6d72c-4191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4193">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4194">'Identity'</span></span>
- <span data-ttu-id="6d72c-4195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4195">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4196">'Razor'</span></span>
- <span data-ttu-id="6d72c-4197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4198">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4198">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4199">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4200">'Identity'</span></span>
- <span data-ttu-id="6d72c-4201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4201">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4202">'Razor'</span></span>
- <span data-ttu-id="6d72c-4203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4205">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4206">'Identity'</span></span>
- <span data-ttu-id="6d72c-4207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4207">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4208">'Razor'</span></span>
- <span data-ttu-id="6d72c-4209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4211">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4212">'Identity'</span></span>
- <span data-ttu-id="6d72c-4213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4213">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4214">'Razor'</span></span>
- <span data-ttu-id="6d72c-4215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4217">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4218">'Identity'</span></span>
- <span data-ttu-id="6d72c-4219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4219">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4220">'Razor'</span></span>
- <span data-ttu-id="6d72c-4221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4223">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4224">'Identity'</span></span>
- <span data-ttu-id="6d72c-4225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4225">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4226">'Razor'</span></span>
- <span data-ttu-id="6d72c-4227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4229">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4230">'Identity'</span></span>
- <span data-ttu-id="6d72c-4231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4231">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4232">'Razor'</span></span>
- <span data-ttu-id="6d72c-4233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4233">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4234">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4234">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4235">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4236">'Identity'</span></span>
- <span data-ttu-id="6d72c-4237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4237">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4238">'Razor'</span></span>
- <span data-ttu-id="6d72c-4239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4241">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4242">'Identity'</span></span>
- <span data-ttu-id="6d72c-4243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4243">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4244">'Razor'</span></span>
- <span data-ttu-id="6d72c-4245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4247">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4248">'Identity'</span></span>
- <span data-ttu-id="6d72c-4249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4249">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4250">'Razor'</span></span>
- <span data-ttu-id="6d72c-4251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4253">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4254">'Identity'</span></span>
- <span data-ttu-id="6d72c-4255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4255">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4256">'Razor'</span></span>
- <span data-ttu-id="6d72c-4257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4259">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4260">'Identity'</span></span>
- <span data-ttu-id="6d72c-4261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4261">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4262">'Razor'</span></span>
- <span data-ttu-id="6d72c-4263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4265">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4266">'Identity'</span></span>
- <span data-ttu-id="6d72c-4267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4267">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4268">'Razor'</span></span>
- <span data-ttu-id="6d72c-4269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4271">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4272">'Identity'</span></span>
- <span data-ttu-id="6d72c-4273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4273">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4274">'Razor'</span></span>
- <span data-ttu-id="6d72c-4275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4277">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4278">'Identity'</span></span>
- <span data-ttu-id="6d72c-4279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4279">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4280">'Razor'</span></span>
- <span data-ttu-id="6d72c-4281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4283">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4284">'Identity'</span></span>
- <span data-ttu-id="6d72c-4285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4285">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4286">'Razor'</span></span>
- <span data-ttu-id="6d72c-4287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4289">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4290">'Identity'</span></span>
- <span data-ttu-id="6d72c-4291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4291">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4292">'Razor'</span></span>
- <span data-ttu-id="6d72c-4293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4295">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4296">'Identity'</span></span>
- <span data-ttu-id="6d72c-4297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4297">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4298">'Razor'</span></span>
- <span data-ttu-id="6d72c-4299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4301">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4302">'Identity'</span></span>
- <span data-ttu-id="6d72c-4303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4303">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4304">'Razor'</span></span>
- <span data-ttu-id="6d72c-4305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4307">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4308">'Identity'</span></span>
- <span data-ttu-id="6d72c-4309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4309">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4310">'Razor'</span></span>
- <span data-ttu-id="6d72c-4311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4313">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4314">'Identity'</span></span>
- <span data-ttu-id="6d72c-4315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4315">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4316">'Razor'</span></span>
- <span data-ttu-id="6d72c-4317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4319">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4320">'Identity'</span></span>
- <span data-ttu-id="6d72c-4321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4321">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4322">'Razor'</span></span>
- <span data-ttu-id="6d72c-4323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4325">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4326">'Identity'</span></span>
- <span data-ttu-id="6d72c-4327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4327">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4328">'Razor'</span></span>
- <span data-ttu-id="6d72c-4329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4331">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4332">'Identity'</span></span>
- <span data-ttu-id="6d72c-4333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4333">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4334">'Razor'</span></span>
- <span data-ttu-id="6d72c-4335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4337">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4338">'Identity'</span></span>
- <span data-ttu-id="6d72c-4339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4339">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4340">'Razor'</span></span>
- <span data-ttu-id="6d72c-4341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4343">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4344">'Identity'</span></span>
- <span data-ttu-id="6d72c-4345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4345">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4346">'Razor'</span></span>
- <span data-ttu-id="6d72c-4347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4349">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4350">'Identity'</span></span>
- <span data-ttu-id="6d72c-4351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4351">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4352">'Razor'</span></span>
- <span data-ttu-id="6d72c-4353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4355">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4356">'Identity'</span></span>
- <span data-ttu-id="6d72c-4357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4357">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4358">'Razor'</span></span>
- <span data-ttu-id="6d72c-4359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4361">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4362">'Identity'</span></span>
- <span data-ttu-id="6d72c-4363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4363">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4364">'Razor'</span></span>
- <span data-ttu-id="6d72c-4365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4367">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4368">'Identity'</span></span>
- <span data-ttu-id="6d72c-4369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4369">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4370">'Razor'</span></span>
- <span data-ttu-id="6d72c-4371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4373">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4374">'Identity'</span></span>
- <span data-ttu-id="6d72c-4375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4375">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4376">'Razor'</span></span>
- <span data-ttu-id="6d72c-4377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4379">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4380">'Identity'</span></span>
- <span data-ttu-id="6d72c-4381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4381">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4382">'Razor'</span></span>
- <span data-ttu-id="6d72c-4383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4385">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4386">'Identity'</span></span>
- <span data-ttu-id="6d72c-4387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4387">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4388">'Razor'</span></span>
- <span data-ttu-id="6d72c-4389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4391">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4392">'Identity'</span></span>
- <span data-ttu-id="6d72c-4393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4393">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4394">'Razor'</span></span>
- <span data-ttu-id="6d72c-4395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4397">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4398">'Identity'</span></span>
- <span data-ttu-id="6d72c-4399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4399">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4400">'Razor'</span></span>
- <span data-ttu-id="6d72c-4401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4403">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4404">'Identity'</span></span>
- <span data-ttu-id="6d72c-4405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4405">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4406">'Razor'</span></span>
- <span data-ttu-id="6d72c-4407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4409">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4410">'Identity'</span></span>
- <span data-ttu-id="6d72c-4411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4411">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4412">'Razor'</span></span>
- <span data-ttu-id="6d72c-4413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4415">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4416">'Identity'</span></span>
- <span data-ttu-id="6d72c-4417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4417">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4418">'Razor'</span></span>
- <span data-ttu-id="6d72c-4419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4421">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4422">'Identity'</span></span>
- <span data-ttu-id="6d72c-4423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4423">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4424">'Razor'</span></span>
- <span data-ttu-id="6d72c-4425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4427">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4428">'Identity'</span></span>
- <span data-ttu-id="6d72c-4429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4429">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4430">'Razor'</span></span>
- <span data-ttu-id="6d72c-4431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4433">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4434">'Identity'</span></span>
- <span data-ttu-id="6d72c-4435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4435">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4436">'Razor'</span></span>
- <span data-ttu-id="6d72c-4437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4439">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4440">'Identity'</span></span>
- <span data-ttu-id="6d72c-4441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4441">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4442">'Razor'</span></span>
- <span data-ttu-id="6d72c-4443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4443">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4444">------------------------------------- | | `hello`                                  | `/hello`                | 仅匹配单个路径 `/hello`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4444">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="6d72c-4445">| | `{Page=Home}`                            | `/`                     | 匹配并将 `Page` 设置为 `Home`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4445">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="6d72c-4446">| | `{Page=Home}`                            | `/Contact`              | 匹配并将 `Page` 设置为 `Contact`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4446">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="6d72c-4447">| | `{controller}/{action}/{id?}`            | `/Products/List`        | 映射到 `Products` 控制器和 `List` 操作。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4447">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="6d72c-4448">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | 映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4448">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> <span data-ttu-id="6d72c-4449">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | 映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4449">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="6d72c-4450">使用模板通常是进行路由最简单的方法。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4450">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="6d72c-4451">还可在路由模板外指定约束和默认值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4451">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="6d72c-4452">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4452">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="6d72c-4453">路由约束参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-4453">Route constraint reference</span></span>

<span data-ttu-id="6d72c-4454">路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4454">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="6d72c-4455">路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4455">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="6d72c-4456">某些路由约束使用路由值以外的数据来考虑是否可以路由请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4456">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="6d72c-4457">例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4457">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="6d72c-4458">约束用于路由请求和链接生成。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4458">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="6d72c-4459">请勿将约束用于“输入验证”。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4459">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="6d72c-4460">如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求” 。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4460">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="6d72c-4461">路由约束用于消除类似路由的歧义，而不是验证特定路由的输入。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4461">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="6d72c-4462">下表演示示例路由约束及其预期行为。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4462">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="6d72c-4463">约束</span><span class="sxs-lookup"><span data-stu-id="6d72c-4463">constraint</span></span> | <span data-ttu-id="6d72c-4464">示例</span><span class="sxs-lookup"><span data-stu-id="6d72c-4464">Example</span></span> | <span data-ttu-id="6d72c-4465">匹配项示例</span><span class="sxs-lookup"><span data-stu-id="6d72c-4465">Example Matches</span></span> | <span data-ttu-id="6d72c-4466">说明</span><span class="sxs-lookup"><span data-stu-id="6d72c-4466">Notes</span></span> |
| ---
<span data-ttu-id="6d72c-4467">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4467">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4468">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4469">'Identity'</span></span>
- <span data-ttu-id="6d72c-4470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4470">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4471">'Razor'</span></span>
- <span data-ttu-id="6d72c-4472">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4473">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4473">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4474">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4475">'Identity'</span></span>
- <span data-ttu-id="6d72c-4476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4476">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4477">'Razor'</span></span>
- <span data-ttu-id="6d72c-4478">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4478">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4479">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4479">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4480">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4481">'Identity'</span></span>
- <span data-ttu-id="6d72c-4482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4482">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4483">'Razor'</span></span>
- <span data-ttu-id="6d72c-4484">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4484">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4485">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4485">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4486">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4487">'Identity'</span></span>
- <span data-ttu-id="6d72c-4488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4488">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4489">'Razor'</span></span>
- <span data-ttu-id="6d72c-4490">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4490">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4491">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4491">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4492">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4493">'Identity'</span></span>
- <span data-ttu-id="6d72c-4494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4494">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4495">'Razor'</span></span>
- <span data-ttu-id="6d72c-4496">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4496">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4497">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4497">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4498">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4498">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4499">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4499">'Identity'</span></span>
- <span data-ttu-id="6d72c-4500">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4500">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4501">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4501">'Razor'</span></span>
- <span data-ttu-id="6d72c-4502">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4502">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4503">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4503">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4504">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4504">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4505">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4505">'Identity'</span></span>
- <span data-ttu-id="6d72c-4506">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4506">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4507">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4507">'Razor'</span></span>
- <span data-ttu-id="6d72c-4508">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4508">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4510">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4511">'Identity'</span></span>
- <span data-ttu-id="6d72c-4512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4512">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4513">'Razor'</span></span>
- <span data-ttu-id="6d72c-4514">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4516">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4517">'Identity'</span></span>
- <span data-ttu-id="6d72c-4518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4518">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4519">'Razor'</span></span>
- <span data-ttu-id="6d72c-4520">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4520">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4521">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | 匹配任何整数 | | `bool` | `{active:bool}` | `true`, `FALSE` | 匹配 `true` 或 `false`（不区分大小写） | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | 在固定区域性中匹配有效的 `DateTime` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4521">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer | | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false` (case-insensitive) | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="6d72c-4522">请参阅前面的警告。| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | 在固定区域性中匹配有效的 `decimal` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4522">See  preceding warning.| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="6d72c-4523">请参阅前面的警告。| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | 在固定区域性中匹配有效的 `double` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4523">See  preceding warning.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="6d72c-4524">请参阅前面的警告。| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | 在固定区域性中匹配有效的 `float` 值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4524">See  preceding warning.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="6d72c-4525">请参阅前面的警告。| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 匹配有效的 `Guid` 值 | | `long` | `{ticks:long}` | `123456789`, `-123456789` | 匹配有效的 `long` 值 | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字符串必须至少为 4 个字符 | | `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 字符串不得超过 8 个字符 | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字符串必须正好为 12 个字符 | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字符串必须至少为 8 个字符，且不得超过 16 个字符 | | `min(value)` | `{age:min(18)}` | `19` | 整数值必须至少为 18 | | `max(value)` | `{age:max(120)}` | `91` | 整数值不得超过 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | 整数值必须至少为 18，且不得超过 120 | | `alpha` | `{name:alpha}` | `Rick` | 字符串必须由一个或多个字母字符（`a`-`z`，不区分大小写）组成 | | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字符串必须匹配正则表达式（参见有关定义正则表达式的提示） | | `required` | `{name:required}` | `Rick` | 用于强制在 URL 生成过程中存在非参数值 |</span><span class="sxs-lookup"><span data-stu-id="6d72c-4525">See  preceding warning.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters | | `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | String must be no more than 8 characters | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and no more than 16 characters long | | `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18 | | `max(value)` | `{age:max(120)}` | `91` | Integer value must be no more than 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 but no more than 120 | | `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive) | | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression (see tips about defining a regular expression) | | `required` | `{name:required}` | `Rick` | Used to enforce that a non-parameter value is present during URL generation |</span></span>

<span data-ttu-id="6d72c-4526">可向单个参数应用多个由冒号分隔的约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4526">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="6d72c-4527">例如，以下约束将参数限制为大于或等于 1 的整数值：</span><span class="sxs-lookup"><span data-stu-id="6d72c-4527">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="6d72c-4528">验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4528">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="6d72c-4529">这些约束假定 URL 不可本地化。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4529">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="6d72c-4530">框架提供的路由约束不会修改存储于路由值中的值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4530">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="6d72c-4531">从 URL 中分析的所有路由值都将存储为字符串。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4531">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="6d72c-4532">例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4532">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="6d72c-4533">正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-4533">Regular expressions</span></span>

<span data-ttu-id="6d72c-4534">ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4534">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="6d72c-4535">有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4535">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="6d72c-4536">正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4536">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="6d72c-4537">必须对正则表达式令牌进行转义。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4537">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="6d72c-4538">要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4538">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="6d72c-4539">要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4539">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="6d72c-4540">下表展示了正则表达式和转义版本。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4540">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="6d72c-4541">正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-4541">Regular Expression</span></span>    | <span data-ttu-id="6d72c-4542">转义后的正则表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-4542">Escaped Regular Expression</span></span>     |
| ---
<span data-ttu-id="6d72c-4543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4544">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4545">'Identity'</span></span>
- <span data-ttu-id="6d72c-4546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4546">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4547">'Razor'</span></span>
- <span data-ttu-id="6d72c-4548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4550">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4551">'Identity'</span></span>
- <span data-ttu-id="6d72c-4552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4552">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4553">'Razor'</span></span>
- <span data-ttu-id="6d72c-4554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4556">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4557">'Identity'</span></span>
- <span data-ttu-id="6d72c-4558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4558">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4559">'Razor'</span></span>
- <span data-ttu-id="6d72c-4560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4562">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4563">'Identity'</span></span>
- <span data-ttu-id="6d72c-4564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4564">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4565">'Razor'</span></span>
- <span data-ttu-id="6d72c-4566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4568">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4569">'Identity'</span></span>
- <span data-ttu-id="6d72c-4570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4570">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4571">'Razor'</span></span>
- <span data-ttu-id="6d72c-4572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4574">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4575">'Identity'</span></span>
- <span data-ttu-id="6d72c-4576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4576">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4577">'Razor'</span></span>
- <span data-ttu-id="6d72c-4578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4580">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4581">'Identity'</span></span>
- <span data-ttu-id="6d72c-4582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4582">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4583">'Razor'</span></span>
- <span data-ttu-id="6d72c-4584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4586">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4587">'Identity'</span></span>
- <span data-ttu-id="6d72c-4588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4588">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4589">'Razor'</span></span>
- <span data-ttu-id="6d72c-4590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4590">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4591">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4591">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4592">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4593">'Identity'</span></span>
- <span data-ttu-id="6d72c-4594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4594">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4595">'Razor'</span></span>
- <span data-ttu-id="6d72c-4596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4598">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4599">'Identity'</span></span>
- <span data-ttu-id="6d72c-4600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4600">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4601">'Razor'</span></span>
- <span data-ttu-id="6d72c-4602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4604">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4605">'Identity'</span></span>
- <span data-ttu-id="6d72c-4606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4606">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4607">'Razor'</span></span>
- <span data-ttu-id="6d72c-4608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4610">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4611">'Identity'</span></span>
- <span data-ttu-id="6d72c-4612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4612">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4613">'Razor'</span></span>
- <span data-ttu-id="6d72c-4614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4616">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4617">'Identity'</span></span>
- <span data-ttu-id="6d72c-4618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4618">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4619">'Razor'</span></span>
- <span data-ttu-id="6d72c-4620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4622">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4623">'Identity'</span></span>
- <span data-ttu-id="6d72c-4624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4624">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4625">'Razor'</span></span>
- <span data-ttu-id="6d72c-4626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4628">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4629">'Identity'</span></span>
- <span data-ttu-id="6d72c-4630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4630">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4631">'Razor'</span></span>
- <span data-ttu-id="6d72c-4632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4634">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4635">'Identity'</span></span>
- <span data-ttu-id="6d72c-4636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4636">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4637">'Razor'</span></span>
- <span data-ttu-id="6d72c-4638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4640">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4641">'Identity'</span></span>
- <span data-ttu-id="6d72c-4642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4642">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4643">'Razor'</span></span>
- <span data-ttu-id="6d72c-4644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4646">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4647">'Identity'</span></span>
- <span data-ttu-id="6d72c-4648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4648">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4649">'Razor'</span></span>
- <span data-ttu-id="6d72c-4650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4652">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4653">'Identity'</span></span>
- <span data-ttu-id="6d72c-4654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4654">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4655">'Razor'</span></span>
- <span data-ttu-id="6d72c-4656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4658">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4659">'Identity'</span></span>
- <span data-ttu-id="6d72c-4660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4660">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4661">'Razor'</span></span>
- <span data-ttu-id="6d72c-4662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4664">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4665">'Identity'</span></span>
- <span data-ttu-id="6d72c-4666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4666">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4667">'Razor'</span></span>
- <span data-ttu-id="6d72c-4668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4668">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4669">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="6d72c-4669">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="6d72c-4670">路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4670">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="6d72c-4671">表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4671">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="6d72c-4672">`^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4672">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="6d72c-4673">如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4673">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="6d72c-4674">下表提供了示例并说明了它们匹配或匹配失败的原因。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4674">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="6d72c-4675">表达式</span><span class="sxs-lookup"><span data-stu-id="6d72c-4675">Expression</span></span>   | <span data-ttu-id="6d72c-4676">String</span><span class="sxs-lookup"><span data-stu-id="6d72c-4676">String</span></span>    | <span data-ttu-id="6d72c-4677">匹配</span><span class="sxs-lookup"><span data-stu-id="6d72c-4677">Match</span></span> | <span data-ttu-id="6d72c-4678">注释</span><span class="sxs-lookup"><span data-stu-id="6d72c-4678">Comment</span></span>               |
| ---
<span data-ttu-id="6d72c-4679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4680">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4681">'Identity'</span></span>
- <span data-ttu-id="6d72c-4682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4682">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4683">'Razor'</span></span>
- <span data-ttu-id="6d72c-4684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4684">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4686">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4687">'Identity'</span></span>
- <span data-ttu-id="6d72c-4688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4688">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4689">'Razor'</span></span>
- <span data-ttu-id="6d72c-4690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4692">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4693">'Identity'</span></span>
- <span data-ttu-id="6d72c-4694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4694">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4695">'Razor'</span></span>
- <span data-ttu-id="6d72c-4696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4698">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4699">'Identity'</span></span>
- <span data-ttu-id="6d72c-4700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4700">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4701">'Razor'</span></span>
- <span data-ttu-id="6d72c-4702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4702">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4703">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4703">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4704">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4705">'Identity'</span></span>
- <span data-ttu-id="6d72c-4706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4706">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4707">'Razor'</span></span>
- <span data-ttu-id="6d72c-4708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4710">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4711">'Identity'</span></span>
- <span data-ttu-id="6d72c-4712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4712">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4713">'Razor'</span></span>
- <span data-ttu-id="6d72c-4714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4714">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4715">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4715">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4716">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4717">'Identity'</span></span>
- <span data-ttu-id="6d72c-4718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4718">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4719">'Razor'</span></span>
- <span data-ttu-id="6d72c-4720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4722">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4723">'Identity'</span></span>
- <span data-ttu-id="6d72c-4724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4724">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4725">'Razor'</span></span>
- <span data-ttu-id="6d72c-4726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4728">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4729">'Identity'</span></span>
- <span data-ttu-id="6d72c-4730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4730">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4731">'Razor'</span></span>
- <span data-ttu-id="6d72c-4732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4734">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4735">'Identity'</span></span>
- <span data-ttu-id="6d72c-4736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4736">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4737">'Razor'</span></span>
- <span data-ttu-id="6d72c-4738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4740">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4741">'Identity'</span></span>
- <span data-ttu-id="6d72c-4742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4742">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4743">'Razor'</span></span>
- <span data-ttu-id="6d72c-4744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4746">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4747">'Identity'</span></span>
- <span data-ttu-id="6d72c-4748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4748">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4749">'Razor'</span></span>
- <span data-ttu-id="6d72c-4750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4752">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4753">'Identity'</span></span>
- <span data-ttu-id="6d72c-4754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4754">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4755">'Razor'</span></span>
- <span data-ttu-id="6d72c-4756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4758">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4759">'Identity'</span></span>
- <span data-ttu-id="6d72c-4760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4760">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4761">'Razor'</span></span>
- <span data-ttu-id="6d72c-4762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4762">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4763">---------- | | `[a-z]{2}`   | hello     | 是   | 子字符串匹配      | | `[a-z]{2}`   | 123abc456 | 是   | 子字符串匹配     | | `[a-z]{2}`   | mz        | 是   | 匹配表达式    | | `[a-z]{2}`   | MZ        | 是   | 不区分大小写    | | `^[a-z]{2}$` | hello     | 否    | 请参阅上述的 `^` 和 `$` | | `^[a-z]{2}$` | 123abc456 | 否    | 请参阅上述的 `^` 和 `$` |</span><span class="sxs-lookup"><span data-stu-id="6d72c-4763">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="6d72c-4764">有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4764">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="6d72c-4765">若要将参数限制为一组已知的可能值，可使用正则表达式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4765">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="6d72c-4766">例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4766">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="6d72c-4767">如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4767">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="6d72c-4768">已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4768">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="6d72c-4769">自定义路由约束</span><span class="sxs-lookup"><span data-stu-id="6d72c-4769">Custom Route Constraints</span></span>

<span data-ttu-id="6d72c-4770">除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4770">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="6d72c-4771"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4771">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="6d72c-4772">若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4772">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="6d72c-4773"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4773">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="6d72c-4774">应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4774">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="6d72c-4775">例如：</span><span class="sxs-lookup"><span data-stu-id="6d72c-4775">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="6d72c-4776">然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4776">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="6d72c-4777">例如：</span><span class="sxs-lookup"><span data-stu-id="6d72c-4777">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a><span data-ttu-id="6d72c-4778">URL 生成参考</span><span class="sxs-lookup"><span data-stu-id="6d72c-4778">URL generation reference</span></span>

<span data-ttu-id="6d72c-4779">以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4779">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="6d72c-4780">上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4780">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="6d72c-4781">字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4781">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="6d72c-4782">有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4782">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="6d72c-4783"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4783">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="6d72c-4784">由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4784">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="6d72c-4785">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4785">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="6d72c-4786">在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4786">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="6d72c-4787">忽略与参数不匹配的环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4787">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="6d72c-4788">在显式提供的值会覆盖环境值的情况下，也会忽略环境值。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4788">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="6d72c-4789">在 URL 中将从左到右进行匹配。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4789">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="6d72c-4790">显式提供但与路由片段不匹配的值将添加到查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4790">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="6d72c-4791">下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。</span><span class="sxs-lookup"><span data-stu-id="6d72c-4791">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="6d72c-4792">环境值</span><span class="sxs-lookup"><span data-stu-id="6d72c-4792">Ambient Values</span></span>                     | <span data-ttu-id="6d72c-4793">显式值</span><span class="sxs-lookup"><span data-stu-id="6d72c-4793">Explicit Values</span></span>                        | <span data-ttu-id="6d72c-4794">结果</span><span class="sxs-lookup"><span data-stu-id="6d72c-4794">Result</span></span>                  |
| ---
<span data-ttu-id="6d72c-4795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4796">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4797">'Identity'</span></span>
- <span data-ttu-id="6d72c-4798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4798">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4799">'Razor'</span></span>
- <span data-ttu-id="6d72c-4800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4802">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4803">'Identity'</span></span>
- <span data-ttu-id="6d72c-4804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4804">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4805">'Razor'</span></span>
- <span data-ttu-id="6d72c-4806">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4806">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4807">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4807">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4808">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4808">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4809">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4809">'Identity'</span></span>
- <span data-ttu-id="6d72c-4810">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4810">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4811">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4811">'Razor'</span></span>
- <span data-ttu-id="6d72c-4812">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4812">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4813">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4813">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4814">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4814">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4815">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4815">'Identity'</span></span>
- <span data-ttu-id="6d72c-4816">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4816">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4817">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4817">'Razor'</span></span>
- <span data-ttu-id="6d72c-4818">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4818">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4820">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4821">'Identity'</span></span>
- <span data-ttu-id="6d72c-4822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4822">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4823">'Razor'</span></span>
- <span data-ttu-id="6d72c-4824">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4826">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4827">'Identity'</span></span>
- <span data-ttu-id="6d72c-4828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4828">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4829">'Razor'</span></span>
- <span data-ttu-id="6d72c-4830">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4832">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4833">'Identity'</span></span>
- <span data-ttu-id="6d72c-4834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4834">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4835">'Razor'</span></span>
- <span data-ttu-id="6d72c-4836">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4838">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4839">'Identity'</span></span>
- <span data-ttu-id="6d72c-4840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4840">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4841">'Razor'</span></span>
- <span data-ttu-id="6d72c-4842">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4844">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4845">'Identity'</span></span>
- <span data-ttu-id="6d72c-4846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4846">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4847">'Razor'</span></span>
- <span data-ttu-id="6d72c-4848">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4850">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4851">'Identity'</span></span>
- <span data-ttu-id="6d72c-4852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4852">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4853">'Razor'</span></span>
- <span data-ttu-id="6d72c-4854">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4856">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4857">'Identity'</span></span>
- <span data-ttu-id="6d72c-4858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4858">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4859">'Razor'</span></span>
- <span data-ttu-id="6d72c-4860">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4862">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4863">'Identity'</span></span>
- <span data-ttu-id="6d72c-4864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4864">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4865">'Razor'</span></span>
- <span data-ttu-id="6d72c-4866">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4868">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4869">'Identity'</span></span>
- <span data-ttu-id="6d72c-4870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4870">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4871">'Razor'</span></span>
- <span data-ttu-id="6d72c-4872">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4874">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4875">'Identity'</span></span>
- <span data-ttu-id="6d72c-4876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4876">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4877">'Razor'</span></span>
- <span data-ttu-id="6d72c-4878">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4880">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4881">'Identity'</span></span>
- <span data-ttu-id="6d72c-4882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4882">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4883">'Razor'</span></span>
- <span data-ttu-id="6d72c-4884">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4884">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4885">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4885">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4886">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4887">'Identity'</span></span>
- <span data-ttu-id="6d72c-4888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4888">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4889">'Razor'</span></span>
- <span data-ttu-id="6d72c-4890">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4892">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4893">'Identity'</span></span>
- <span data-ttu-id="6d72c-4894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4894">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4895">'Razor'</span></span>
- <span data-ttu-id="6d72c-4896">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4898">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4899">'Identity'</span></span>
- <span data-ttu-id="6d72c-4900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4900">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4901">'Razor'</span></span>
- <span data-ttu-id="6d72c-4902">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4904">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4905">'Identity'</span></span>
- <span data-ttu-id="6d72c-4906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4906">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4907">'Razor'</span></span>
- <span data-ttu-id="6d72c-4908">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4908">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4909">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4909">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4910">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4911">'Identity'</span></span>
- <span data-ttu-id="6d72c-4912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4912">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4913">'Razor'</span></span>
- <span data-ttu-id="6d72c-4914">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4916">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4917">'Identity'</span></span>
- <span data-ttu-id="6d72c-4918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4918">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4919">'Razor'</span></span>
- <span data-ttu-id="6d72c-4920">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4922">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4923">'Identity'</span></span>
- <span data-ttu-id="6d72c-4924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4924">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4925">'Razor'</span></span>
- <span data-ttu-id="6d72c-4926">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4928">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4929">'Identity'</span></span>
- <span data-ttu-id="6d72c-4930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4930">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4931">'Razor'</span></span>
- <span data-ttu-id="6d72c-4932">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4934">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4935">'Identity'</span></span>
- <span data-ttu-id="6d72c-4936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4936">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4937">'Razor'</span></span>
- <span data-ttu-id="6d72c-4938">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4940">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4941">'Identity'</span></span>
- <span data-ttu-id="6d72c-4942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4942">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4943">'Razor'</span></span>
- <span data-ttu-id="6d72c-4944">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4946">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4947">'Identity'</span></span>
- <span data-ttu-id="6d72c-4948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4948">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4949">'Razor'</span></span>
- <span data-ttu-id="6d72c-4950">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4952">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4953">'Identity'</span></span>
- <span data-ttu-id="6d72c-4954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4954">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4955">'Razor'</span></span>
- <span data-ttu-id="6d72c-4956">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4958">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4959">'Identity'</span></span>
- <span data-ttu-id="6d72c-4960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4960">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4961">'Razor'</span></span>
- <span data-ttu-id="6d72c-4962">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4964">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4965">'Identity'</span></span>
- <span data-ttu-id="6d72c-4966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4966">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4967">'Razor'</span></span>
- <span data-ttu-id="6d72c-4968">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4970">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4971">'Identity'</span></span>
- <span data-ttu-id="6d72c-4972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4972">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4973">'Razor'</span></span>
- <span data-ttu-id="6d72c-4974">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4976">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4977">'Identity'</span></span>
- <span data-ttu-id="6d72c-4978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4978">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4979">'Razor'</span></span>
- <span data-ttu-id="6d72c-4980">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4980">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4981">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4981">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4982">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4982">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4983">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4983">'Identity'</span></span>
- <span data-ttu-id="6d72c-4984">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4984">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4985">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4985">'Razor'</span></span>
- <span data-ttu-id="6d72c-4986">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4986">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-4987">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4987">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4988">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4988">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4989">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4989">'Identity'</span></span>
- <span data-ttu-id="6d72c-4990">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4990">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4991">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4991">'Razor'</span></span>
- <span data-ttu-id="6d72c-4992">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4992">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4993">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4993">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-4994">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4994">'Blazor'</span></span>
- <span data-ttu-id="6d72c-4995">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4995">'Identity'</span></span>
- <span data-ttu-id="6d72c-4996">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4996">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-4997">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-4997">'Razor'</span></span>
- <span data-ttu-id="6d72c-4998">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4998">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-4999">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-4999">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-5000">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5000">'Blazor'</span></span>
- <span data-ttu-id="6d72c-5001">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5001">'Identity'</span></span>
- <span data-ttu-id="6d72c-5002">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5002">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-5003">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5003">'Razor'</span></span>
- <span data-ttu-id="6d72c-5004">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5004">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-5005">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5005">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-5006">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5006">'Blazor'</span></span>
- <span data-ttu-id="6d72c-5007">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5007">'Identity'</span></span>
- <span data-ttu-id="6d72c-5008">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5008">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-5009">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5009">'Razor'</span></span>
- <span data-ttu-id="6d72c-5010">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5010">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-5011">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5011">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-5012">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5012">'Blazor'</span></span>
- <span data-ttu-id="6d72c-5013">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5013">'Identity'</span></span>
- <span data-ttu-id="6d72c-5014">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5014">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-5015">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5015">'Razor'</span></span>
- <span data-ttu-id="6d72c-5016">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5016">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-5017">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5017">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-5018">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5018">'Blazor'</span></span>
- <span data-ttu-id="6d72c-5019">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5019">'Identity'</span></span>
- <span data-ttu-id="6d72c-5020">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5020">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-5021">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5021">'Razor'</span></span>
- <span data-ttu-id="6d72c-5022">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5022">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-5023">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5023">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-5024">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5024">'Blazor'</span></span>
- <span data-ttu-id="6d72c-5025">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5025">'Identity'</span></span>
- <span data-ttu-id="6d72c-5026">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5026">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-5027">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5027">'Razor'</span></span>
- <span data-ttu-id="6d72c-5028">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5028">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-5029">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5029">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-5030">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5030">'Blazor'</span></span>
- <span data-ttu-id="6d72c-5031">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5031">'Identity'</span></span>
- <span data-ttu-id="6d72c-5032">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5032">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-5033">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5033">'Razor'</span></span>
- <span data-ttu-id="6d72c-5034">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5034">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d72c-5035">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5035">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d72c-5036">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5036">'Blazor'</span></span>
- <span data-ttu-id="6d72c-5037">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5037">'Identity'</span></span>
- <span data-ttu-id="6d72c-5038">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5038">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d72c-5039">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d72c-5039">'Razor'</span></span>
- <span data-ttu-id="6d72c-5040">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="6d72c-5040">'SignalR' uid:</span></span> 

<span data-ttu-id="6d72c-5041">------------ | | 控制器 =“Home”                | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 控制器 =“Order”，操作 =“About” | `/Order/About`          |
| 控制器 = “Home”，颜色 = “Red” | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 操作 =“About”，颜色 =“Red”        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="6d72c-5041">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

<span data-ttu-id="6d72c-5042">如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：</span><span class="sxs-lookup"><span data-stu-id="6d72c-5042">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="6d72c-5043">当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。</span><span class="sxs-lookup"><span data-stu-id="6d72c-5043">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="6d72c-5044">复杂段</span><span class="sxs-lookup"><span data-stu-id="6d72c-5044">Complex segments</span></span>

<span data-ttu-id="6d72c-5045">复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="6d72c-5045">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="6d72c-5046">请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。</span><span class="sxs-lookup"><span data-stu-id="6d72c-5046">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="6d72c-5047">ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。</span><span class="sxs-lookup"><span data-stu-id="6d72c-5047">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>

::: moniker-end
