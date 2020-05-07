---
title: ASP.NET Core 中的请求功能
author: ardalis
description: 了解与 ASP.NET Core 的接口中定义的 HTTP 请求和响应相关的 Web 服务器实现详细信息。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/request-features
ms.openlocfilehash: e26a1a7b35d40c1214bbc40269571545cbd2c235
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776020"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="f10f2-103">ASP.NET Core 中的请求功能</span><span class="sxs-lookup"><span data-stu-id="f10f2-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="f10f2-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f10f2-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f10f2-105">与 HTTP 请求和响应相关的 Web 服务器实现详细信息在接口中定义。</span><span class="sxs-lookup"><span data-stu-id="f10f2-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="f10f2-106">服务器实现和中间件使用这些接口来创建和修改应用程序的托管管道。</span><span class="sxs-lookup"><span data-stu-id="f10f2-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="f10f2-107">功能接口</span><span class="sxs-lookup"><span data-stu-id="f10f2-107">Feature interfaces</span></span>

<span data-ttu-id="f10f2-108">ASP.NET Core 在 `Microsoft.AspNetCore.Http.Features` 中定义了许多 HTTP 功能接口，服务器使用这些接口来标识其支持的功能。</span><span class="sxs-lookup"><span data-stu-id="f10f2-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="f10f2-109">以下功能接口处理请求并返回响应：</span><span class="sxs-lookup"><span data-stu-id="f10f2-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="f10f2-110">`IHttpRequestFeature` 定义 HTTP 请求的结构，包括协议、路径、查询字符串、标头和正文。</span><span class="sxs-lookup"><span data-stu-id="f10f2-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="f10f2-111">`IHttpResponseFeature` 定义 HTTP 响应的结构，包括状态代码、标头和响应的正文。</span><span class="sxs-lookup"><span data-stu-id="f10f2-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="f10f2-112">`IHttpAuthenticationFeature` 定义支持基于 `ClaimsPrincipal` 来标识用户并指定身份验证处理程序。</span><span class="sxs-lookup"><span data-stu-id="f10f2-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="f10f2-113">`IHttpUpgradeFeature` 定义对 [HTTP 升级](https://tools.ietf.org/html/rfc2616.html#section-14.42)的支持，允许客户端指定在服务器需要切换协议时要使用的其他协议。</span><span class="sxs-lookup"><span data-stu-id="f10f2-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="f10f2-114">`IHttpBufferingFeature` 定义禁用请求和/或响应缓冲的方法。</span><span class="sxs-lookup"><span data-stu-id="f10f2-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="f10f2-115">`IHttpConnectionFeature` 为本地和远程地址以及端口定义属性。</span><span class="sxs-lookup"><span data-stu-id="f10f2-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="f10f2-116">`IHttpRequestLifetimeFeature` 定义支持中止连接，或者检测是否已提前终止请求（如由于客户端断开连接）。</span><span class="sxs-lookup"><span data-stu-id="f10f2-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="f10f2-117">`IHttpSendFileFeature` 定义异步发送文件的方法。</span><span class="sxs-lookup"><span data-stu-id="f10f2-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="f10f2-118">`IHttpWebSocketFeature` 定义支持 Web 套接字的 API。</span><span class="sxs-lookup"><span data-stu-id="f10f2-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="f10f2-119">`IHttpRequestIdentifierFeature` 添加一个可以实现的属性来唯一标识请求。</span><span class="sxs-lookup"><span data-stu-id="f10f2-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="f10f2-120">`ISessionFeature` 为支持用户会话定义 `ISessionFactory` 和 `ISession` 抽象。</span><span class="sxs-lookup"><span data-stu-id="f10f2-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="f10f2-121">`ITlsConnectionFeature` 定义用于检索客户端证书的 API。</span><span class="sxs-lookup"><span data-stu-id="f10f2-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="f10f2-122">`ITlsTokenBindingFeature` 定义使用 TLS 令牌绑定参数的方法。</span><span class="sxs-lookup"><span data-stu-id="f10f2-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="f10f2-123">`ISessionFeature` 不是服务器功能，而是由 `SessionMiddleware` 实现（请参阅[管理应用程序状态](app-state.md)）。</span><span class="sxs-lookup"><span data-stu-id="f10f2-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="f10f2-124">功能集合</span><span class="sxs-lookup"><span data-stu-id="f10f2-124">Feature collections</span></span>

<span data-ttu-id="f10f2-125">`Features` 的 `HttpContext` 属性为获取和设置当前请求的可用 HTTP 功能提供了一个接口。</span><span class="sxs-lookup"><span data-stu-id="f10f2-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="f10f2-126">由于功能集合即使在请求的上下文中也是可变的，所以可使用中间件来修改集合并添加对其他功能的支持。</span><span class="sxs-lookup"><span data-stu-id="f10f2-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="f10f2-127">中间件和请求功能</span><span class="sxs-lookup"><span data-stu-id="f10f2-127">Middleware and request features</span></span>

<span data-ttu-id="f10f2-128">虽然服务器负责创建功能集合，但中间件既可以添加到该集合中，也可以使用集合中的功能。</span><span class="sxs-lookup"><span data-stu-id="f10f2-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="f10f2-129">例如，`StaticFileMiddleware` 访问 `IHttpSendFileFeature` 功能。</span><span class="sxs-lookup"><span data-stu-id="f10f2-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="f10f2-130">如果该功能存在，则用于从其物理路径发送所请求的静态文件。</span><span class="sxs-lookup"><span data-stu-id="f10f2-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="f10f2-131">否则，使用较慢的替代方法来发送文件。</span><span class="sxs-lookup"><span data-stu-id="f10f2-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="f10f2-132">如果可用，`IHttpSendFileFeature` 允许操作系统打开文件并执行直接内核模式复制到网卡。</span><span class="sxs-lookup"><span data-stu-id="f10f2-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="f10f2-133">另外，中间件可以添加到由服务器建立的功能集合中。</span><span class="sxs-lookup"><span data-stu-id="f10f2-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="f10f2-134">中间件甚至可以取代现有的功能，以便增加服务器的功能。</span><span class="sxs-lookup"><span data-stu-id="f10f2-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="f10f2-135">添加到集合中的功能稍后将在请求管道中立即用于其他中间件或基础应用程序本身。</span><span class="sxs-lookup"><span data-stu-id="f10f2-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="f10f2-136">通过结合自定义服务器实现和特定的中间件增强功能，可构造应用程序所需的精确功能集。</span><span class="sxs-lookup"><span data-stu-id="f10f2-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="f10f2-137">这样一来，无需更改服务器即可添加缺少的功能，并确保只公开最少的功能，从而限制攻击外围应用并提高性能。</span><span class="sxs-lookup"><span data-stu-id="f10f2-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="f10f2-138">摘要</span><span class="sxs-lookup"><span data-stu-id="f10f2-138">Summary</span></span>

<span data-ttu-id="f10f2-139">功能接口定义给定请求可能支持的特定 HTTP 功能。</span><span class="sxs-lookup"><span data-stu-id="f10f2-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="f10f2-140">服务器定义功能的集合，以及该服务器支持的初始功能集，但中间件可用于增强这些功能。</span><span class="sxs-lookup"><span data-stu-id="f10f2-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f10f2-141">其他资源</span><span class="sxs-lookup"><span data-stu-id="f10f2-141">Additional resources</span></span>

* [<span data-ttu-id="f10f2-142">服务器</span><span class="sxs-lookup"><span data-stu-id="f10f2-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="f10f2-143">中间件</span><span class="sxs-lookup"><span data-stu-id="f10f2-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="f10f2-144">.NET 的开放 Web 接口 (OWIN)</span><span class="sxs-lookup"><span data-stu-id="f10f2-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
