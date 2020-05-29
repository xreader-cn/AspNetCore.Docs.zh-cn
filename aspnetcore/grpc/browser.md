---
<span data-ttu-id="2e4ff-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2e4ff-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="2e4ff-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2e4ff-102">'Blazor'</span></span>
- <span data-ttu-id="2e4ff-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2e4ff-103">'Identity'</span></span>
- <span data-ttu-id="2e4ff-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2e4ff-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2e4ff-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2e4ff-105">'Razor'</span></span>
- <span data-ttu-id="2e4ff-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2e4ff-106">'SignalR' uid:</span></span> 

---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="2e4ff-107">在浏览器应用中使用 gRPC</span><span class="sxs-lookup"><span data-stu-id="2e4ff-107">Use gRPC in browser apps</span></span>

<span data-ttu-id="2e4ff-108">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2e4ff-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="2e4ff-109">无法从基于浏览器的应用中调用 HTTP/2 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-109">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="2e4ff-110">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) 是一种允许浏览器 JavaScript 和 Blazor 应用调用 gRPC 服务的协议。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-110">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="2e4ff-111">本文介绍如何使用 .NET Core 中的 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-111">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="2e4ff-112">.NET Core 中的 gRPC-Web 与Envoy</span><span class="sxs-lookup"><span data-stu-id="2e4ff-112">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="2e4ff-113">有两种方式可将 gRPC-Web 添加到 ASP.NET Core 应用中：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-113">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="2e4ff-114">在 ASP.NET Core 中同时支持 gRPC-Web 和 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-114">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="2e4ff-115">此选项会使用 `Grpc.AspNetCore.Web` 包提供的中间件。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-115">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="2e4ff-116">使用 [Envoy 代理](https://www.envoyproxy.io/)的 gRPC-Web 支持将 gRPC-Web 转换为 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-116">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="2e4ff-117">转换后的调用随后会转发给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-117">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="2e4ff-118">每种方法既有优点，也有缺点。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-118">There are pros and cons to each approach.</span></span> <span data-ttu-id="2e4ff-119">如果已在应用的环境中使用 Envoy 作为代理，则合理的做法可能是还用它来提供 gRPC-Web 支持。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-119">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="2e4ff-120">如果想向 gRPC-Web 提供一种仅需要 ASP.NET Core 的简单解决方案，则 `Grpc.AspNetCore.Web` 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-120">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="2e4ff-121">配置 ASP.NET Core 中的 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="2e4ff-121">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="2e4ff-122">可将托管于 ASP.NET Core 中的 gRPC 服务配置为随 HTTP/2 gRPC 一起支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-122">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="2e4ff-123">gRPC-Web 不需要对服务进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-123">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="2e4ff-124">唯一的修改是启动配置。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-124">The only modification is startup configuration.</span></span>

<span data-ttu-id="2e4ff-125">若要使用 ASP.NET Core gRPC 服务启用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-125">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="2e4ff-126">添加对 [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 包的引用。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-126">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="2e4ff-127">配置应用以使用 gRPC-Web，方法是将 `UseGrpcWeb` 和 `EnableGrpcWeb` 添加到 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-127">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="2e4ff-128">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-128">The preceding code:</span></span>

* <span data-ttu-id="2e4ff-129">在路由之后、终结点之前添加 gRPC-Web 中间件 `UseGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-129">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="2e4ff-130">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `EnableGrpcWeb` 的 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-130">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="2e4ff-131">或者，可以配置 gRPC-Web 中间件，使所有服务在默认情况下都支持 gRPC-Web，而不需要 `EnableGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-131">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="2e4ff-132">在添加中间件时指定 `new GrpcWebOptions { DefaultEnabled = true }`。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-132">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="2e4ff-133">存在一个已知问题，它导致在 .NET Core 3.x 中[由 Http.sys 托管](xref:fundamentals/servers/httpsys)时 gRPC-Web 会失败。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-133">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="2e4ff-134">[此处](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)提供了一种变通方法，可用来获取在 Http.sys 上工作 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-134">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="2e4ff-135">gRPC-Web 和 CORS</span><span class="sxs-lookup"><span data-stu-id="2e4ff-135">gRPC-Web and CORS</span></span>

<span data-ttu-id="2e4ff-136">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-136">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="2e4ff-137">此限制适用于使用浏览器应用发出 gRPC-Web 调用。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-137">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="2e4ff-138">例如，由 `https://www.contoso.com` 提供服务的浏览器应用对托管于 `https://services.contoso.com` 上的 gRPC-Web 服务的调用会被阻止。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-138">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="2e4ff-139">跨域资源共享 (CORS) 可用于放宽此限制。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-139">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="2e4ff-140">若要允许浏览器应用进行跨域 gRPC-Web 调用，请[在 ASP.NET Core 中设置 CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-140">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="2e4ff-141">使用内置 CORS 支持，并使用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> 公开特定于 gRPC 的标头。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-141">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="2e4ff-142">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-142">The preceding code:</span></span>

* <span data-ttu-id="2e4ff-143">调用 `AddCors` 以添加 CORS 服务，并配置公开特定于 gRPC 的标头的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-143">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="2e4ff-144">调用 `UseCors` 以在路由之后、终结点之前添加 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-144">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="2e4ff-145">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `RequiresCors`的 CORS。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-145">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="2e4ff-146">从浏览器调用 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="2e4ff-146">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="2e4ff-147">浏览器应用可以使用 gRPC-Web 来调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-147">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="2e4ff-148">使用 gRPC-Web 从浏览器调用 gRPC 服务时，存在一些要求和限制：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-148">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="2e4ff-149">服务器必须已配置为支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-149">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="2e4ff-150">不支持客户端流式处理和双向流式处理调用。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-150">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="2e4ff-151">支持服务器流式处理。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-151">Server streaming is supported.</span></span>
* <span data-ttu-id="2e4ff-152">在其他域上调用 gRPC 服务需要在服务器上配置 [CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-152">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="2e4ff-153">JavaScript gRPC-Web 客户端</span><span class="sxs-lookup"><span data-stu-id="2e4ff-153">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="2e4ff-154">存在 JavaScript gRPC-Web 客户端。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-154">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="2e4ff-155">有关如何从 JavaScript 使用 gRPC-Web 的说明，请参阅[使用 gRPC-Web 编写 JavaScript 客户端代码](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-155">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="2e4ff-156">使用 .NET gRPC 客户端配置 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="2e4ff-156">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="2e4ff-157">可以将 .NET gRPC 客户端配置为发出 gRPC-Web 调用。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-157">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="2e4ff-158">这对于托管在浏览器中且具有相同 JavaScript 代码 HTTP 限制的 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 应用来说非常有用。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-158">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="2e4ff-159">使用 .NET 客户端调用 gRPC-Web [与 HTTP/2 gRPC 相同](xref:grpc/client)。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-159">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="2e4ff-160">唯一的修改是创建通道的方式。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-160">The only modification is how the channel is created.</span></span>

<span data-ttu-id="2e4ff-161">若要使用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-161">To use gRPC-Web:</span></span>

* <span data-ttu-id="2e4ff-162">添加对 [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 包的引用。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-162">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="2e4ff-163">确保对 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 包的引用为 2.29.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-163">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="2e4ff-164">配置通道以使用 `GrpcWebHandler`：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-164">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="2e4ff-165">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-165">The preceding code:</span></span>

* <span data-ttu-id="2e4ff-166">配置通道以使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-166">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="2e4ff-167">创建一个客户端并使用通道发出调用。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-167">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="2e4ff-168">`GrpcWebHandler` 具有以下配置选项：</span><span class="sxs-lookup"><span data-stu-id="2e4ff-168">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="2e4ff-169">**InnerHandler**：发出 gRPC HTTP 请求的基础 <xref:System.Net.Http.HttpMessageHandler>，例如 `HttpClientHandler`。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-169">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="2e4ff-170">GrpcWebMode：枚举类型，指定 gRPC HTTP 请求 `Content-Type` 是 `application/grpc-web` 还是 `application/grpc-web-text`。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-170">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="2e4ff-171">`GrpcWebMode.GrpcWeb` 配置不进行编码即发送的内容。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-171">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="2e4ff-172">默认值。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-172">Default value.</span></span>
    * <span data-ttu-id="2e4ff-173">`GrpcWebMode.GrpcWebText` 配置需进行 base64 编码的内容。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-173">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="2e4ff-174">对于浏览器中的服务器流式处理调用是必需的。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-174">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="2e4ff-175">**HttpVersion**：HTTP 协议 `Version` 用于在基础 gRPC HTTP 请求上设置 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-175">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="2e4ff-176">gRPC-Web 不需要特定版本，且除非指定，否则不会替代默认版本。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-176">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2e4ff-177">生成的 gRPC 客户端具有用于调用一元方法的同步和异步方法。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-177">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="2e4ff-178">例如，`SayHello` 是同步的，而 `SayHelloAsync` 是异步的。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-178">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="2e4ff-179">在 Blazor WebAssembly 应用中调用同步方法将导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-179">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="2e4ff-180">必须始终在 Blazor WebAssembly 中使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="2e4ff-180">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e4ff-181">其他资源</span><span class="sxs-lookup"><span data-stu-id="2e4ff-181">Additional resources</span></span>

* [<span data-ttu-id="2e4ff-182">适用于 Web 客户端 GitHub 项目的 gRPC</span><span class="sxs-lookup"><span data-stu-id="2e4ff-182">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
