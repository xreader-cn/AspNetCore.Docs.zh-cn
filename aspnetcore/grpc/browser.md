---
title: 浏览器应用中的 gRPC
author: jamesnk
description: 了解如何在 ASP.NET Core 上配置 gRPC 服务，以便从使用 gRPC 的浏览器应用程序调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830632"
---
# <a name="grpc-in-browser-apps"></a><span data-ttu-id="f4ce1-103">浏览器应用中的 gRPC</span><span class="sxs-lookup"><span data-stu-id="f4ce1-103">gRPC in browser apps</span></span>

<span data-ttu-id="f4ce1-104">按[James 牛顿-k](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f4ce1-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f4ce1-105">**gRPC-.NET 中的 Web 支持是试验性的**</span><span class="sxs-lookup"><span data-stu-id="f4ce1-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="f4ce1-106">gRPC-Web for .NET 是一个试验性项目，而不是已提交的产品。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="f4ce1-107">我们想要：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-107">We want to:</span></span>
>
> * <span data-ttu-id="f4ce1-108">测试实现 gRPC 的方法。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="f4ce1-109">如果对 .NET 开发人员而言，此方法对 .NET 开发人员非常有用，这种方法与通过代理设置 gRPC 的传统方法有关。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="f4ce1-110">请在[https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet)上留下反馈，以确保我们的开发人员喜欢和的工作效率。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="f4ce1-111">不能从基于浏览器的应用程序中调用 HTTP/2 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="f4ce1-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)是一种允许浏览器 JavaScript 和 Blazor 应用调用 gRPC 服务的协议。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="f4ce1-113">本文介绍如何在 .NET Core 中使用 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="f4ce1-114">在 ASP.NET Core 中配置 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="f4ce1-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="f4ce1-115">ASP.NET Core 中托管的 gRPC services 可配置为支持 gRPC 和 HTTP/2 gRPC。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="f4ce1-116">gRPC-Web 不需要对服务进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="f4ce1-117">唯一的修改是启动配置。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="f4ce1-118">使用 ASP.NET Core gRPC 服务启用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="f4ce1-119">添加对[Grpc](https://www.nuget.org/packages/Grpc.AspNetCore.Web)包的引用。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="f4ce1-120">将应用程序配置为使用 gRPC，方法是将 `AddGrpcWeb` 和 `UseGrpcWeb` 添加到*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

<span data-ttu-id="f4ce1-121">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-121">The preceding code:</span></span>

* <span data-ttu-id="f4ce1-122">在路由之后和终结点之前添加 gRPC-Web 中间件、`UseGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="f4ce1-123">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持具有 `EnableGrpcWeb`的 gRPC Web。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="f4ce1-124">或者，通过将 `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` 添加到 ConfigureServices，将所有服务配置为支持 gRPC。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

<span data-ttu-id="f4ce1-125">可能需要一些其他配置才能从浏览器调用 gRPC，例如配置 ASP.NET Core 以支持 CORS。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="f4ce1-126">有关详细信息，请参阅[支持 CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="f4ce1-127">从浏览器调用 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="f4ce1-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="f4ce1-128">浏览器应用可以使用 gRPC 来调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="f4ce1-129">通过浏览器从 gRPC 调用 gRPC 服务时，有一些要求和限制：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="f4ce1-130">服务器必须配置为支持 gRPC。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="f4ce1-131">不支持客户端流式处理和双向流式处理调用。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="f4ce1-132">支持服务器流式处理。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-132">Server streaming is supported.</span></span>
* <span data-ttu-id="f4ce1-133">若要在不同的域上调用 gRPC 服务，需要在服务器上配置[CORS](xref:security/cors) 。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="f4ce1-134">JavaScript gRPC-Web 客户端</span><span class="sxs-lookup"><span data-stu-id="f4ce1-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="f4ce1-135">存在 JavaScript gRPC Web 客户端。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="f4ce1-136">有关如何从 JavaScript 使用 gRPC-Web 的说明，请参阅[使用 gRPC 编写 JavaScript 客户端代码](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="f4ce1-137">通过 .NET gRPC 客户端配置 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="f4ce1-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="f4ce1-138">可以将 .NET gRPC 客户端配置为进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="f4ce1-139">这对于在浏览器中承载的[Blazor WebAssembly](xref:blazor/index#blazor-webassembly)应用程序非常有用，并且具有与 JavaScript 代码相同的 HTTP 限制。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="f4ce1-140">使用 .NET 客户端调用 gRPC 与[HTTP/2 gRPC 相同](xref:grpc/client)。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="f4ce1-141">唯一的修改就是创建通道的方式。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="f4ce1-142">使用 gRPC：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="f4ce1-143">添加对[Grpc](https://www.nuget.org/packages/Grpc.Net.Client.Web)包的引用。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="f4ce1-144">将通道配置为使用 `GrpcWebHandler`：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-144">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="f4ce1-145">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-145">The preceding code:</span></span>

* <span data-ttu-id="f4ce1-146">将通道配置为使用 gRPC。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-146">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="f4ce1-147">创建一个客户端，并使用通道发出调用。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-147">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="f4ce1-148">创建时，`GrpcWebHandler` 具有以下配置选项：</span><span class="sxs-lookup"><span data-stu-id="f4ce1-148">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="f4ce1-149">**InnerHandler**：进行 HTTP 调用的基础 <xref:System.Net.Http.HttpMessageHandler>，例如 `HttpClientHandler`。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-149">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the HTTP call, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="f4ce1-150">**Mode**： `GrpcWebMode` enum。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-150">**Mode**: `GrpcWebMode` enum.</span></span> <span data-ttu-id="f4ce1-151">`GrpcWebMode.GrpcWebText` 将内容配置为 base64 编码，这是支持服务器流式处理调用所必需的。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-151">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded, which is required to support server streaming calls.</span></span>
* <span data-ttu-id="f4ce1-152">**HttpVersion**： HTTP 协议 `Version`。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-152">**HttpVersion**: HTTP protocol `Version`.</span></span> <span data-ttu-id="f4ce1-153">gRPC-Web 不需要特定协议，因此在发出请求（除非已配置）时，不会指定一个协议。</span><span class="sxs-lookup"><span data-stu-id="f4ce1-153">gRPC-Web doesn't require a specific protocol and won't specify one when making a request unless configured.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4ce1-154">其他资源</span><span class="sxs-lookup"><span data-stu-id="f4ce1-154">Additional resources</span></span>

* [<span data-ttu-id="f4ce1-155">Web 客户端 GitHub 项目的 gRPC</span><span class="sxs-lookup"><span data-stu-id="f4ce1-155">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
