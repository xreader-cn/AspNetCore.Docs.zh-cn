---
<span data-ttu-id="731d3-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="731d3-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="731d3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="731d3-102">'Blazor'</span></span>
- <span data-ttu-id="731d3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="731d3-103">'Identity'</span></span>
- <span data-ttu-id="731d3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="731d3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="731d3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="731d3-105">'Razor'</span></span>
- <span data-ttu-id="731d3-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="731d3-106">'SignalR' uid:</span></span> 

---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="731d3-107">在浏览器应用中使用 gRPC</span><span class="sxs-lookup"><span data-stu-id="731d3-107">Use gRPC in browser apps</span></span>

<span data-ttu-id="731d3-108">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="731d3-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="731d3-109">**.NET 中的 gRPC-Web 支持是试验性的**</span><span class="sxs-lookup"><span data-stu-id="731d3-109">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="731d3-110">适用于 .NET 的 gRPC-Web 是一个试验性项目，而不是已完善的产品。</span><span class="sxs-lookup"><span data-stu-id="731d3-110">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="731d3-111">我们希望：</span><span class="sxs-lookup"><span data-stu-id="731d3-111">We want to:</span></span>
>
> * <span data-ttu-id="731d3-112">测试我们实现 gRPC-Web 的方法是否有效。</span><span class="sxs-lookup"><span data-stu-id="731d3-112">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="731d3-113">获得此方法是否对 .NET 开发人员有用（与通过代理设置 gRPC-Web 的传统方法相比）的反馈。</span><span class="sxs-lookup"><span data-stu-id="731d3-113">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="731d3-114">请通过 [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) 提供反馈，以确保我们生成的内容受到开发人员欢迎并能帮助他们提高工作效率。</span><span class="sxs-lookup"><span data-stu-id="731d3-114">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="731d3-115">无法从基于浏览器的应用中调用 HTTP/2 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="731d3-115">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="731d3-116">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) 是一种允许浏览器 JavaScript 和 Blazor 应用调用 gRPC 服务的协议。</span><span class="sxs-lookup"><span data-stu-id="731d3-116">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="731d3-117">本文介绍如何使用 .NET Core 中的 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="731d3-117">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="731d3-118">.NET Core 中的 gRPC-Web 与Envoy</span><span class="sxs-lookup"><span data-stu-id="731d3-118">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="731d3-119">有两种方式可将 gRPC-Web 添加到 ASP.NET Core 应用中：</span><span class="sxs-lookup"><span data-stu-id="731d3-119">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="731d3-120">在 ASP.NET Core 中同时支持 gRPC-Web 和 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="731d3-120">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="731d3-121">此选项会使用 `Grpc.AspNetCore.Web` 包提供的中间件。</span><span class="sxs-lookup"><span data-stu-id="731d3-121">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="731d3-122">使用 [Envoy 代理](https://www.envoyproxy.io/)的 gRPC-Web 支持将 gRPC-Web 转换为 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="731d3-122">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="731d3-123">转换后的调用随后会转发给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="731d3-123">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="731d3-124">每种方法既有优点，也有缺点。</span><span class="sxs-lookup"><span data-stu-id="731d3-124">There are pros and cons to each approach.</span></span> <span data-ttu-id="731d3-125">如果已在应用的环境中使用 Envoy 作为代理，则合理的做法可能是还用它来提供 gRPC-Web 支持。</span><span class="sxs-lookup"><span data-stu-id="731d3-125">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="731d3-126">如果想向 gRPC-Web 提供一种仅需要 ASP.NET Core 的简单解决方案，则 `Grpc.AspNetCore.Web` 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="731d3-126">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="731d3-127">配置 ASP.NET Core 中的 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="731d3-127">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="731d3-128">可将托管于 ASP.NET Core 中的 gRPC 服务配置为随 HTTP/2 gRPC 一起支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="731d3-128">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="731d3-129">gRPC-Web 不需要对服务进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="731d3-129">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="731d3-130">唯一的修改是启动配置。</span><span class="sxs-lookup"><span data-stu-id="731d3-130">The only modification is startup configuration.</span></span>

<span data-ttu-id="731d3-131">若要使用 ASP.NET Core gRPC 服务启用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="731d3-131">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="731d3-132">添加对 [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 包的引用。</span><span class="sxs-lookup"><span data-stu-id="731d3-132">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="731d3-133">配置应用以使用 gRPC-Web，方法是将 `UseGrpcWeb` 和 `EnableGrpcWeb` 添加到 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="731d3-133">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="731d3-134">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="731d3-134">The preceding code:</span></span>

* <span data-ttu-id="731d3-135">在路由之后、终结点之前添加 gRPC-Web 中间件 `UseGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="731d3-135">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="731d3-136">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `EnableGrpcWeb` 的 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="731d3-136">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="731d3-137">或者，可以配置 gRPC-Web 中间件，使所有服务在默认情况下都支持 gRPC-Web，而不需要 `EnableGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="731d3-137">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="731d3-138">在添加中间件时指定 `new GrpcWebOptions { DefaultEnabled = true }`。</span><span class="sxs-lookup"><span data-stu-id="731d3-138">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="731d3-139">存在一个已知问题，它导致在 .NET Core 3.x 中[由 Http.sys 托管](xref:fundamentals/servers/httpsys)时 gRPC-Web 会失败。</span><span class="sxs-lookup"><span data-stu-id="731d3-139">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="731d3-140">[此处](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)提供了一种变通方法，可用来获取在 Http.sys 上工作 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="731d3-140">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="731d3-141">gRPC-Web 和 CORS</span><span class="sxs-lookup"><span data-stu-id="731d3-141">gRPC-Web and CORS</span></span>

<span data-ttu-id="731d3-142">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="731d3-142">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="731d3-143">此限制适用于使用浏览器应用发出 gRPC-Web 调用。</span><span class="sxs-lookup"><span data-stu-id="731d3-143">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="731d3-144">例如，由 `https://www.contoso.com` 提供服务的浏览器应用对托管于 `https://services.contoso.com` 上的 gRPC-Web 服务的调用会被阻止。</span><span class="sxs-lookup"><span data-stu-id="731d3-144">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="731d3-145">跨域资源共享 (CORS) 可用于放宽此限制。</span><span class="sxs-lookup"><span data-stu-id="731d3-145">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="731d3-146">若要允许浏览器应用进行跨域 gRPC-Web 调用，请[在 ASP.NET Core 中设置 CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="731d3-146">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="731d3-147">使用内置 CORS 支持，并使用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> 公开特定于 gRPC 的标头。</span><span class="sxs-lookup"><span data-stu-id="731d3-147">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="731d3-148">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="731d3-148">The preceding code:</span></span>

* <span data-ttu-id="731d3-149">调用 `AddCors` 以添加 CORS 服务，并配置公开特定于 gRPC 的标头的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="731d3-149">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="731d3-150">调用 `UseCors` 以在路由之后、终结点之前添加 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="731d3-150">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="731d3-151">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `RequiresCors`的 CORS。</span><span class="sxs-lookup"><span data-stu-id="731d3-151">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="731d3-152">从浏览器调用 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="731d3-152">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="731d3-153">浏览器应用可以使用 gRPC-Web 来调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="731d3-153">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="731d3-154">使用 gRPC-Web 从浏览器调用 gRPC 服务时，存在一些要求和限制：</span><span class="sxs-lookup"><span data-stu-id="731d3-154">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="731d3-155">服务器必须已配置为支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="731d3-155">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="731d3-156">不支持客户端流式处理和双向流式处理调用。</span><span class="sxs-lookup"><span data-stu-id="731d3-156">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="731d3-157">支持服务器流式处理。</span><span class="sxs-lookup"><span data-stu-id="731d3-157">Server streaming is supported.</span></span>
* <span data-ttu-id="731d3-158">在其他域上调用 gRPC 服务需要在服务器上配置 [CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="731d3-158">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="731d3-159">JavaScript gRPC-Web 客户端</span><span class="sxs-lookup"><span data-stu-id="731d3-159">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="731d3-160">存在 JavaScript gRPC-Web 客户端。</span><span class="sxs-lookup"><span data-stu-id="731d3-160">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="731d3-161">有关如何从 JavaScript 使用 gRPC-Web 的说明，请参阅[使用 gRPC-Web 编写 JavaScript 客户端代码](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。</span><span class="sxs-lookup"><span data-stu-id="731d3-161">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="731d3-162">使用 .NET gRPC 客户端配置 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="731d3-162">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="731d3-163">可以将 .NET gRPC 客户端配置为发出 gRPC-Web 调用。</span><span class="sxs-lookup"><span data-stu-id="731d3-163">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="731d3-164">这对于托管在浏览器中且具有相同 JavaScript 代码 HTTP 限制的 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 应用来说非常有用。</span><span class="sxs-lookup"><span data-stu-id="731d3-164">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="731d3-165">使用 .NET 客户端调用 gRPC-Web [与 HTTP/2 gRPC 相同](xref:grpc/client)。</span><span class="sxs-lookup"><span data-stu-id="731d3-165">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="731d3-166">唯一的修改是创建通道的方式。</span><span class="sxs-lookup"><span data-stu-id="731d3-166">The only modification is how the channel is created.</span></span>

<span data-ttu-id="731d3-167">若要使用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="731d3-167">To use gRPC-Web:</span></span>

* <span data-ttu-id="731d3-168">添加对 [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 包的引用。</span><span class="sxs-lookup"><span data-stu-id="731d3-168">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="731d3-169">确保对 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 包的引用为 2.29.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="731d3-169">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="731d3-170">配置通道以使用 `GrpcWebHandler`：</span><span class="sxs-lookup"><span data-stu-id="731d3-170">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="731d3-171">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="731d3-171">The preceding code:</span></span>

* <span data-ttu-id="731d3-172">配置通道以使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="731d3-172">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="731d3-173">创建一个客户端并使用通道发出调用。</span><span class="sxs-lookup"><span data-stu-id="731d3-173">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="731d3-174">`GrpcWebHandler` 具有以下配置选项：</span><span class="sxs-lookup"><span data-stu-id="731d3-174">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="731d3-175">**InnerHandler**：发出 gRPC HTTP 请求的基础 <xref:System.Net.Http.HttpMessageHandler>，例如 `HttpClientHandler`。</span><span class="sxs-lookup"><span data-stu-id="731d3-175">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="731d3-176">GrpcWebMode：枚举类型，指定 gRPC HTTP 请求 `Content-Type` 是 `application/grpc-web` 还是 `application/grpc-web-text`。</span><span class="sxs-lookup"><span data-stu-id="731d3-176">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="731d3-177">`GrpcWebMode.GrpcWeb` 配置不进行编码即发送的内容。</span><span class="sxs-lookup"><span data-stu-id="731d3-177">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="731d3-178">默认值。</span><span class="sxs-lookup"><span data-stu-id="731d3-178">Default value.</span></span>
    * <span data-ttu-id="731d3-179">`GrpcWebMode.GrpcWebText` 配置需进行 base64 编码的内容。</span><span class="sxs-lookup"><span data-stu-id="731d3-179">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="731d3-180">对于浏览器中的服务器流式处理调用是必需的。</span><span class="sxs-lookup"><span data-stu-id="731d3-180">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="731d3-181">**HttpVersion**：HTTP 协议 `Version` 用于在基础 gRPC HTTP 请求上设置 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)。</span><span class="sxs-lookup"><span data-stu-id="731d3-181">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="731d3-182">gRPC-Web 不需要特定版本，且除非指定，否则不会替代默认版本。</span><span class="sxs-lookup"><span data-stu-id="731d3-182">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="731d3-183">生成的 gRPC 客户端具有用于调用一元方法的同步和异步方法。</span><span class="sxs-lookup"><span data-stu-id="731d3-183">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="731d3-184">例如，`SayHello` 是同步的，而 `SayHelloAsync` 是异步的。</span><span class="sxs-lookup"><span data-stu-id="731d3-184">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="731d3-185">在 Blazor WebAssembly 应用中调用同步方法将导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="731d3-185">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="731d3-186">必须始终在 Blazor WebAssembly 中使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="731d3-186">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="731d3-187">其他资源</span><span class="sxs-lookup"><span data-stu-id="731d3-187">Additional resources</span></span>

* [<span data-ttu-id="731d3-188">适用于 Web 客户端 GitHub 项目的 gRPC</span><span class="sxs-lookup"><span data-stu-id="731d3-188">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
