---
title: 在浏览器应用中使用 gRPC
author: jamesnk
description: 了解如何在 ASP.NET Core 上配置 gRPC 服务，以便从使用 gRPC 的浏览器应用程序调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/10/2020
uid: grpc/browser
ms.openlocfilehash: 333fc8c4277bbac47042d4904c276e963186914a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172274"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="70cb8-103">在浏览器应用中使用 gRPC</span><span class="sxs-lookup"><span data-stu-id="70cb8-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="70cb8-104">按[James 牛顿-k](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="70cb8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="70cb8-105">**gRPC-.NET 中的 Web 支持是试验性的**</span><span class="sxs-lookup"><span data-stu-id="70cb8-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="70cb8-106">gRPC-Web for .NET 是一个试验性项目，而不是已提交的产品。</span><span class="sxs-lookup"><span data-stu-id="70cb8-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="70cb8-107">我们想要：</span><span class="sxs-lookup"><span data-stu-id="70cb8-107">We want to:</span></span>
>
> * <span data-ttu-id="70cb8-108">测试实现 gRPC 的方法。</span><span class="sxs-lookup"><span data-stu-id="70cb8-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="70cb8-109">如果对 .NET 开发人员而言，此方法对 .NET 开发人员非常有用，这种方法与通过代理设置 gRPC 的传统方法有关。</span><span class="sxs-lookup"><span data-stu-id="70cb8-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="70cb8-110">请在[https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet)上留下反馈，以确保我们的开发人员喜欢和的工作效率。</span><span class="sxs-lookup"><span data-stu-id="70cb8-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="70cb8-111">不能从基于浏览器的应用程序中调用 HTTP/2 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="70cb8-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="70cb8-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)是一种允许浏览器 JavaScript 和 Blazor 应用调用 gRPC 服务的协议。</span><span class="sxs-lookup"><span data-stu-id="70cb8-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="70cb8-113">本文介绍如何在 .NET Core 中使用 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="70cb8-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="70cb8-114">在 ASP.NET Core 中配置 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="70cb8-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="70cb8-115">ASP.NET Core 中托管的 gRPC services 可配置为支持 gRPC 和 HTTP/2 gRPC。</span><span class="sxs-lookup"><span data-stu-id="70cb8-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="70cb8-116">gRPC-Web 不需要对服务进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="70cb8-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="70cb8-117">唯一的修改是启动配置。</span><span class="sxs-lookup"><span data-stu-id="70cb8-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="70cb8-118">使用 ASP.NET Core gRPC 服务启用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="70cb8-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="70cb8-119">添加对[Grpc](https://www.nuget.org/packages/Grpc.AspNetCore.Web)包的引用。</span><span class="sxs-lookup"><span data-stu-id="70cb8-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="70cb8-120">将应用程序配置为使用 gRPC，方法是将 `AddGrpcWeb` 和 `UseGrpcWeb` 添加到*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="70cb8-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="70cb8-121">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="70cb8-121">The preceding code:</span></span>

* <span data-ttu-id="70cb8-122">在路由之后和终结点之前添加 gRPC-Web 中间件、`UseGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="70cb8-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="70cb8-123">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持具有 `EnableGrpcWeb`的 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="70cb8-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="70cb8-124">或者，通过将 `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` 添加到 ConfigureServices，将所有服务配置为支持 gRPC。</span><span class="sxs-lookup"><span data-stu-id="70cb8-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

<span data-ttu-id="70cb8-125">可能需要一些其他配置才能从浏览器调用 gRPC，例如配置 ASP.NET Core 以支持 CORS。</span><span class="sxs-lookup"><span data-stu-id="70cb8-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="70cb8-126">有关详细信息，请参阅[支持 CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="70cb8-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="70cb8-127">从浏览器调用 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="70cb8-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="70cb8-128">浏览器应用可以使用 gRPC 来调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="70cb8-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="70cb8-129">通过浏览器从 gRPC 调用 gRPC 服务时，有一些要求和限制：</span><span class="sxs-lookup"><span data-stu-id="70cb8-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="70cb8-130">服务器必须配置为支持 gRPC。</span><span class="sxs-lookup"><span data-stu-id="70cb8-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="70cb8-131">不支持客户端流式处理和双向流式处理调用。</span><span class="sxs-lookup"><span data-stu-id="70cb8-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="70cb8-132">支持服务器流式处理。</span><span class="sxs-lookup"><span data-stu-id="70cb8-132">Server streaming is supported.</span></span>
* <span data-ttu-id="70cb8-133">若要在不同的域上调用 gRPC 服务，需要在服务器上配置[CORS](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="70cb8-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="70cb8-134">JavaScript gRPC-Web 客户端</span><span class="sxs-lookup"><span data-stu-id="70cb8-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="70cb8-135">存在 JavaScript gRPC Web 客户端。</span><span class="sxs-lookup"><span data-stu-id="70cb8-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="70cb8-136">有关如何从 JavaScript 使用 gRPC-Web 的说明，请参阅[使用 gRPC 编写 JavaScript 客户端代码](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。</span><span class="sxs-lookup"><span data-stu-id="70cb8-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="70cb8-137">通过 .NET gRPC 客户端配置 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="70cb8-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="70cb8-138">可以将 .NET gRPC 客户端配置为进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="70cb8-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="70cb8-139">这对于在浏览器中承载的[Blazor WebAssembly](xref:blazor/index#blazor-webassembly)应用程序非常有用，并且具有与 JavaScript 代码相同的 HTTP 限制。</span><span class="sxs-lookup"><span data-stu-id="70cb8-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="70cb8-140">使用 .NET 客户端调用 gRPC 与[HTTP/2 gRPC 相同](xref:grpc/client)。</span><span class="sxs-lookup"><span data-stu-id="70cb8-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="70cb8-141">唯一的修改就是创建通道的方式。</span><span class="sxs-lookup"><span data-stu-id="70cb8-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="70cb8-142">使用 gRPC：</span><span class="sxs-lookup"><span data-stu-id="70cb8-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="70cb8-143">添加对[Grpc](https://www.nuget.org/packages/Grpc.Net.Client.Web)包的引用。</span><span class="sxs-lookup"><span data-stu-id="70cb8-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="70cb8-144">请确保对[Grpc .net](https://www.nuget.org/packages/Grpc.Net.Client)的引用2.27.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="70cb8-144">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="70cb8-145">将通道配置为使用 `GrpcWebHandler`：</span><span class="sxs-lookup"><span data-stu-id="70cb8-145">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="70cb8-146">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="70cb8-146">The preceding code:</span></span>

* <span data-ttu-id="70cb8-147">将通道配置为使用 gRPC。</span><span class="sxs-lookup"><span data-stu-id="70cb8-147">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="70cb8-148">创建一个客户端，并使用通道发出调用。</span><span class="sxs-lookup"><span data-stu-id="70cb8-148">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="70cb8-149">创建时，`GrpcWebHandler` 具有以下配置选项：</span><span class="sxs-lookup"><span data-stu-id="70cb8-149">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="70cb8-150">**InnerHandler**：使 gRPC HTTP 请求的基础 <xref:System.Net.Http.HttpMessageHandler> 例如，`HttpClientHandler`。</span><span class="sxs-lookup"><span data-stu-id="70cb8-150">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="70cb8-151">**Mode**：指定是否 `application/grpc-web` 或 `application/grpc-web-text``Content-Type` gRPC HTTP 请求请求的枚举类型。</span><span class="sxs-lookup"><span data-stu-id="70cb8-151">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="70cb8-152">`GrpcWebMode.GrpcWeb` 配置在不进行编码的情况下发送的内容。</span><span class="sxs-lookup"><span data-stu-id="70cb8-152">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="70cb8-153">默认值。</span><span class="sxs-lookup"><span data-stu-id="70cb8-153">Default value.</span></span>
    * <span data-ttu-id="70cb8-154">`GrpcWebMode.GrpcWebText` 将内容配置为 base64 编码。</span><span class="sxs-lookup"><span data-stu-id="70cb8-154">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="70cb8-155">对于浏览器中的服务器流式处理调用是必需的。</span><span class="sxs-lookup"><span data-stu-id="70cb8-155">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="70cb8-156">**HttpVersion**： http 协议 `Version` 用于在基础 gRPC HTTP 请求上设置[HttpRequestMessage。](xref:System.Net.Http.HttpRequestMessage.Version)</span><span class="sxs-lookup"><span data-stu-id="70cb8-156">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="70cb8-157">gRPC-Web 不需要特定版本，并且除非指定，否则不会覆盖默认版本。</span><span class="sxs-lookup"><span data-stu-id="70cb8-157">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="70cb8-158">生成的 gRPC 客户端具有用于调用一元方法的同步和异步方法。</span><span class="sxs-lookup"><span data-stu-id="70cb8-158">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="70cb8-159">例如，`SayHello` 为 sync，`SayHelloAsync` 为 async。</span><span class="sxs-lookup"><span data-stu-id="70cb8-159">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="70cb8-160">在 Blazor WebAssembly 应用中调用同步方法将导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="70cb8-160">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="70cb8-161">异步方法必须始终在 Blazor WebAssembly 中使用。</span><span class="sxs-lookup"><span data-stu-id="70cb8-161">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70cb8-162">其他资源</span><span class="sxs-lookup"><span data-stu-id="70cb8-162">Additional resources</span></span>

* [<span data-ttu-id="70cb8-163">Web 客户端 GitHub 项目的 gRPC</span><span class="sxs-lookup"><span data-stu-id="70cb8-163">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
