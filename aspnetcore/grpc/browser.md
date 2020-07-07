---
title: 在浏览器应用中使用 gRPC
author: jamesnk
description: 了解如何配置 ASP.NET Core 上的 gRPC 服务，以使其可以从使用 gRPC-Web 的浏览器应用中进行调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 20f72deb9895111a6e691eb1ee5cd7419c8c4cb4
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793510"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="35aa6-103">在浏览器应用中使用 gRPC</span><span class="sxs-lookup"><span data-stu-id="35aa6-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="35aa6-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="35aa6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="35aa6-105">了解如何配置现有 ASP.NET Core gRPC 服务，以使其可以从使用 [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) 协议的浏览器应用中进行调用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="35aa6-106">gRPC-Web 允许浏览器 JavaScript 和 Blazor 应用调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="35aa6-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="35aa6-107">无法从基于浏览器的应用中调用 HTTP/2 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="35aa6-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="35aa6-108">可将托管于 ASP.NET Core 中的 gRPC 服务配置为随 HTTP/2 gRPC 一起支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="35aa6-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="35aa6-109">有关将 gRPC 服务添加到现有 ASP.NET Core 应用的说明，请参阅[将 gRPC 服务添加到 ASP.NET Core 应用](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app)。</span><span class="sxs-lookup"><span data-stu-id="35aa6-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="35aa6-110">有关创建 gRPC 项目的说明，请参阅 <xref:tutorials/grpc/grpc-start>。</span><span class="sxs-lookup"><span data-stu-id="35aa6-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="35aa6-111">.NET Core 中的 gRPC-Web 与Envoy</span><span class="sxs-lookup"><span data-stu-id="35aa6-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="35aa6-112">有两种方式可将 gRPC-Web 添加到 ASP.NET Core 应用中：</span><span class="sxs-lookup"><span data-stu-id="35aa6-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="35aa6-113">在 ASP.NET Core 中同时支持 gRPC-Web 和 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="35aa6-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="35aa6-114">此选项会使用 `Grpc.AspNetCore.Web` 包提供的中间件。</span><span class="sxs-lookup"><span data-stu-id="35aa6-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="35aa6-115">使用 [Envoy 代理](https://www.envoyproxy.io/)的 gRPC-Web 支持将 gRPC-Web 转换为 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="35aa6-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="35aa6-116">转换后的调用随后会转发给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="35aa6-117">每种方法既有优点，也有缺点。</span><span class="sxs-lookup"><span data-stu-id="35aa6-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="35aa6-118">如果应用的环境已将 Envoy 用作代理，则也可以使用 Envoy 提供 gRPC-Web 支持。</span><span class="sxs-lookup"><span data-stu-id="35aa6-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="35aa6-119">对于仅需要 ASP.NET Core 的 gRPC-Web 的基本解决方案，`Grpc.AspNetCore.Web` 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="35aa6-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="35aa6-120">配置 ASP.NET Core 中的 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="35aa6-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="35aa6-121">可将托管于 ASP.NET Core 中的 gRPC 服务配置为随 HTTP/2 gRPC 一起支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="35aa6-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="35aa6-122">gRPC-Web 不需要对服务进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="35aa6-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="35aa6-123">唯一的修改是启动配置。</span><span class="sxs-lookup"><span data-stu-id="35aa6-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="35aa6-124">若要使用 ASP.NET Core gRPC 服务启用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="35aa6-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="35aa6-125">添加对 [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 包的引用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="35aa6-126">配置应用以使用 gRPC-Web，方法是将 `UseGrpcWeb` 和 `EnableGrpcWeb` 添加到 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="35aa6-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="35aa6-127">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="35aa6-127">The preceding code:</span></span>

* <span data-ttu-id="35aa6-128">在路由之后、终结点之前添加 gRPC-Web 中间件 `UseGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="35aa6-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="35aa6-129">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `EnableGrpcWeb` 的 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="35aa6-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="35aa6-130">或者，可以配置 gRPC-Web 中间件，使所有服务在默认情况下都支持 gRPC-Web，而不需要 `EnableGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="35aa6-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="35aa6-131">在添加中间件时指定 `new GrpcWebOptions { DefaultEnabled = true }`。</span><span class="sxs-lookup"><span data-stu-id="35aa6-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="35aa6-132">存在一个已知问题，它导致在 .NET Core 3.x 中[由 Http.sys 托管](xref:fundamentals/servers/httpsys)时 gRPC-Web 会失败。</span><span class="sxs-lookup"><span data-stu-id="35aa6-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="35aa6-133">[此处](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)提供了一种变通方法，可用来获取在 Http.sys 上工作 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="35aa6-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="35aa6-134">gRPC-Web 和 CORS</span><span class="sxs-lookup"><span data-stu-id="35aa6-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="35aa6-135">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="35aa6-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="35aa6-136">此限制适用于使用浏览器应用发出 gRPC-Web 调用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="35aa6-137">例如，由 `https://www.contoso.com` 提供服务的浏览器应用对托管于 `https://services.contoso.com` 上的 gRPC-Web 服务的调用会被阻止。</span><span class="sxs-lookup"><span data-stu-id="35aa6-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="35aa6-138">跨域资源共享 (CORS) 可用于放宽此限制。</span><span class="sxs-lookup"><span data-stu-id="35aa6-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="35aa6-139">若要允许浏览器应用进行跨域 gRPC-Web 调用，请[在 ASP.NET Core 中设置 CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="35aa6-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="35aa6-140">使用内置 CORS 支持，并使用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> 公开特定于 gRPC 的标头。</span><span class="sxs-lookup"><span data-stu-id="35aa6-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="35aa6-141">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="35aa6-141">The preceding code:</span></span>

* <span data-ttu-id="35aa6-142">调用 `AddCors` 以添加 CORS 服务，并配置公开特定于 gRPC 的标头的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="35aa6-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="35aa6-143">调用 `UseCors` 以在路由之后、终结点之前添加 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="35aa6-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="35aa6-144">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `RequiresCors`的 CORS。</span><span class="sxs-lookup"><span data-stu-id="35aa6-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="35aa6-145">从浏览器调用 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="35aa6-145">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="35aa6-146">浏览器应用可以使用 gRPC-Web 来调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="35aa6-146">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="35aa6-147">使用 gRPC-Web 从浏览器调用 gRPC 服务时，存在一些要求和限制：</span><span class="sxs-lookup"><span data-stu-id="35aa6-147">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="35aa6-148">服务器必须已配置为支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="35aa6-148">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="35aa6-149">不支持客户端流式处理和双向流式处理调用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-149">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="35aa6-150">支持服务器流式处理。</span><span class="sxs-lookup"><span data-stu-id="35aa6-150">Server streaming is supported.</span></span>
* <span data-ttu-id="35aa6-151">在其他域上调用 gRPC 服务需要在服务器上配置 [CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="35aa6-151">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="35aa6-152">JavaScript gRPC-Web 客户端</span><span class="sxs-lookup"><span data-stu-id="35aa6-152">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="35aa6-153">存在 JavaScript gRPC-Web 客户端。</span><span class="sxs-lookup"><span data-stu-id="35aa6-153">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="35aa6-154">有关如何从 JavaScript 使用 gRPC-Web 的说明，请参阅[使用 gRPC-Web 编写 JavaScript 客户端代码](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。</span><span class="sxs-lookup"><span data-stu-id="35aa6-154">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="35aa6-155">使用 .NET gRPC 客户端配置 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="35aa6-155">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="35aa6-156">可以将 .NET gRPC 客户端配置为发出 gRPC-Web 调用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-156">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="35aa6-157">这对于托管在浏览器中且具有相同 JavaScript 代码 HTTP 限制的 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 应用来说非常有用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-157">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="35aa6-158">使用 .NET 客户端调用 gRPC-Web [与 HTTP/2 gRPC 相同](xref:grpc/client)。</span><span class="sxs-lookup"><span data-stu-id="35aa6-158">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="35aa6-159">唯一的修改是创建通道的方式。</span><span class="sxs-lookup"><span data-stu-id="35aa6-159">The only modification is how the channel is created.</span></span>

<span data-ttu-id="35aa6-160">若要使用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="35aa6-160">To use gRPC-Web:</span></span>

* <span data-ttu-id="35aa6-161">添加对 [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 包的引用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-161">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="35aa6-162">确保对 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 包的引用为 2.29.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="35aa6-162">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="35aa6-163">配置通道以使用 `GrpcWebHandler`：</span><span class="sxs-lookup"><span data-stu-id="35aa6-163">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="35aa6-164">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="35aa6-164">The preceding code:</span></span>

* <span data-ttu-id="35aa6-165">配置通道以使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="35aa6-165">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="35aa6-166">创建一个客户端并使用通道发出调用。</span><span class="sxs-lookup"><span data-stu-id="35aa6-166">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="35aa6-167">`GrpcWebHandler` 具有以下配置选项：</span><span class="sxs-lookup"><span data-stu-id="35aa6-167">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="35aa6-168">**InnerHandler**：发出 gRPC HTTP 请求的基础 <xref:System.Net.Http.HttpMessageHandler>，例如 `HttpClientHandler`。</span><span class="sxs-lookup"><span data-stu-id="35aa6-168">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="35aa6-169">GrpcWebMode：枚举类型，指定 gRPC HTTP 请求 `Content-Type` 是 `application/grpc-web` 还是 `application/grpc-web-text`。</span><span class="sxs-lookup"><span data-stu-id="35aa6-169">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="35aa6-170">`GrpcWebMode.GrpcWeb` 配置不进行编码即发送的内容。</span><span class="sxs-lookup"><span data-stu-id="35aa6-170">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="35aa6-171">默认值。</span><span class="sxs-lookup"><span data-stu-id="35aa6-171">Default value.</span></span>
    * <span data-ttu-id="35aa6-172">`GrpcWebMode.GrpcWebText` 配置需进行 base64 编码的内容。</span><span class="sxs-lookup"><span data-stu-id="35aa6-172">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="35aa6-173">对于浏览器中的服务器流式处理调用是必需的。</span><span class="sxs-lookup"><span data-stu-id="35aa6-173">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="35aa6-174">**HttpVersion**：HTTP 协议 `Version` 用于在基础 gRPC HTTP 请求上设置 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)。</span><span class="sxs-lookup"><span data-stu-id="35aa6-174">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="35aa6-175">gRPC-Web 不需要特定版本，且除非指定，否则不会替代默认版本。</span><span class="sxs-lookup"><span data-stu-id="35aa6-175">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="35aa6-176">生成的 gRPC 客户端具有用于调用一元方法的同步和异步方法。</span><span class="sxs-lookup"><span data-stu-id="35aa6-176">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="35aa6-177">例如，`SayHello` 是同步的，而 `SayHelloAsync` 是异步的。</span><span class="sxs-lookup"><span data-stu-id="35aa6-177">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="35aa6-178">在 Blazor WebAssembly 应用中调用同步方法将导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="35aa6-178">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="35aa6-179">必须始终在 Blazor WebAssembly 中使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="35aa6-179">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="35aa6-180">其他资源</span><span class="sxs-lookup"><span data-stu-id="35aa6-180">Additional resources</span></span>

* [<span data-ttu-id="35aa6-181">适用于 Web 客户端 GitHub 项目的 gRPC</span><span class="sxs-lookup"><span data-stu-id="35aa6-181">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
