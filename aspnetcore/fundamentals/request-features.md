---
title: ASP.NET Core 中的请求功能
author: ardalis
description: 了解与 ASP.NET Core 的接口中定义的 HTTP 请求和响应相关的 Web 服务器实现详细信息。
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- appsettings.json
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870420"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="8e77c-103">ASP.NET Core 中的请求功能</span><span class="sxs-lookup"><span data-stu-id="8e77c-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="8e77c-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8e77c-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8e77c-105">应用程序和中间件用于处理请求的 `HttpContext` API 具有一个抽象层，它将其称为“功能接口”。</span><span class="sxs-lookup"><span data-stu-id="8e77c-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer underneath it called *feature interfaces*.</span></span> <span data-ttu-id="8e77c-106">每个功能接口都提供 `HttpContext` 公开的功能的粒度子集。</span><span class="sxs-lookup"><span data-stu-id="8e77c-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="8e77c-107">在处理请求时，服务器或中间件可以添加、修改、包装、替换甚至删除这些接口，而不必重新实现整个 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="8e77c-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="8e77c-108">它们还可以在测试时用于模拟功能。</span><span class="sxs-lookup"><span data-stu-id="8e77c-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="8e77c-109">功能集合</span><span class="sxs-lookup"><span data-stu-id="8e77c-109">Feature collections</span></span>

<span data-ttu-id="8e77c-110">`HttpContext` 的 <xref:Microsoft.AspNetCore.Http.HttpContext.Features> 属性提供对当前请求的功能接口集合的访问。</span><span class="sxs-lookup"><span data-stu-id="8e77c-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="8e77c-111">由于功能集合即使在请求的上下文中也是可变的，所以可使用中间件来修改集合并添加对其他功能的支持。</span><span class="sxs-lookup"><span data-stu-id="8e77c-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="8e77c-112">某些高级功能只能通过功能集合访问关联接口提供。</span><span class="sxs-lookup"><span data-stu-id="8e77c-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="8e77c-113">功能接口</span><span class="sxs-lookup"><span data-stu-id="8e77c-113">Feature interfaces</span></span>

<span data-ttu-id="8e77c-114">ASP.NET Core 在 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> 中定义了许多常见的 HTTP 功能接口，各种服务器和中间件共享这些接口来标识其支持的功能。</span><span class="sxs-lookup"><span data-stu-id="8e77c-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="8e77c-115">服务器和中间件还可以提供自己的具有附加功能的接口。</span><span class="sxs-lookup"><span data-stu-id="8e77c-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="8e77c-116">大多数功能接口都提供可选的点亮功能，如果该功能不存在，则它们的相关 `HttpContext` API 提供默认值。</span><span class="sxs-lookup"><span data-stu-id="8e77c-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="8e77c-117">以下内容中会根据需要指出几个接口，因为它们提供核心请求和响应功能，必须实现它们才能处理请求。</span><span class="sxs-lookup"><span data-stu-id="8e77c-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="8e77c-118">以下功能接口来自 <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>：</span><span class="sxs-lookup"><span data-stu-id="8e77c-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="8e77c-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>：定义 HTTP 请求的结构，包括协议、路径、查询字符串、标头和正文。</span><span class="sxs-lookup"><span data-stu-id="8e77c-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="8e77c-120">此功能是处理请求所必需的。</span><span class="sxs-lookup"><span data-stu-id="8e77c-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="8e77c-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>：定义 HTTP 响应的结构，包括状态代码、标头和响应的正文。</span><span class="sxs-lookup"><span data-stu-id="8e77c-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="8e77c-122">此功能是处理请求所必需的。</span><span class="sxs-lookup"><span data-stu-id="8e77c-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e77c-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>：定义使用 `Stream`、`PipeWriter` 或文件写出响应正文的不同方式。</span><span class="sxs-lookup"><span data-stu-id="8e77c-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="8e77c-124">此功能是处理请求所必需的。</span><span class="sxs-lookup"><span data-stu-id="8e77c-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="8e77c-125">这会替换 `IHttpResponseFeature.Body` 和 `IHttpSendFileFeature`。</span><span class="sxs-lookup"><span data-stu-id="8e77c-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="8e77c-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>：保存当前与请求关联的 <xref:System.Security.Claims.ClaimsPrincipal>。</span><span class="sxs-lookup"><span data-stu-id="8e77c-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="8e77c-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>：用于分析和缓存传入的 HTTP 和多部分表单提交。</span><span class="sxs-lookup"><span data-stu-id="8e77c-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8e77c-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>：用于控制请求或响应正文是否允许同步 IO 操作。</span><span class="sxs-lookup"><span data-stu-id="8e77c-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8e77c-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>：定义禁用请求和/或响应缓冲的方法。</span><span class="sxs-lookup"><span data-stu-id="8e77c-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="8e77c-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>：为连接 ID 以及本地和远程地址和端口定义属性。</span><span class="sxs-lookup"><span data-stu-id="8e77c-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8e77c-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：控制当前请求允许的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="8e77c-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8e77c-132">`IHttpRequestBodyDetectionFeature`：指示请求是否可以有正文。</span><span class="sxs-lookup"><span data-stu-id="8e77c-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="8e77c-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>：添加一个可以实现的属性来唯一标识请求。</span><span class="sxs-lookup"><span data-stu-id="8e77c-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="8e77c-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>：定义支持中止连接，或者检测是否已提前终止请求（如由于客户端断开连接）。</span><span class="sxs-lookup"><span data-stu-id="8e77c-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e77c-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>：提供对请求尾部标头（如果有）的访问权限。</span><span class="sxs-lookup"><span data-stu-id="8e77c-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="8e77c-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>：用于为支持它们的协议（如 HTTP/2 或 HTTP/3）发送重置消息。</span><span class="sxs-lookup"><span data-stu-id="8e77c-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8e77c-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>：允许应用程序提供响应尾部标头（如支持）。</span><span class="sxs-lookup"><span data-stu-id="8e77c-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8e77c-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>：定义异步发送文件的方法。</span><span class="sxs-lookup"><span data-stu-id="8e77c-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="8e77c-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>：定义对 [HTTP 升级](https://tools.ietf.org/html/rfc2616.html#section-14.42)的支持，允许客户端指定在服务器需要切换协议时要使用的其他协议。</span><span class="sxs-lookup"><span data-stu-id="8e77c-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="8e77c-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>：定义支持 Web 套接字的 API。</span><span class="sxs-lookup"><span data-stu-id="8e77c-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e77c-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>：控制是否应通过 HTTPS 连接使用响应压缩。</span><span class="sxs-lookup"><span data-stu-id="8e77c-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="8e77c-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>：存储每个请求应用程序状态的 <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> 集合。</span><span class="sxs-lookup"><span data-stu-id="8e77c-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="8e77c-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>：分析并缓存查询字符串。</span><span class="sxs-lookup"><span data-stu-id="8e77c-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e77c-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>：将请求正文表示为 <xref:System.IO.Pipelines.PipeReader>。</span><span class="sxs-lookup"><span data-stu-id="8e77c-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="8e77c-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>：分析并缓存请求 `Cookie` 标头值。</span><span class="sxs-lookup"><span data-stu-id="8e77c-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="8e77c-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>：控制如何将响应 cookie 应用到 `Set-Cookie` 标头。</span><span class="sxs-lookup"><span data-stu-id="8e77c-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8e77c-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>：此功能可用于访问请求服务器变量，如 IIS 提供的变量。</span><span class="sxs-lookup"><span data-stu-id="8e77c-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="8e77c-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>：提供对具有限定范围的请求服务的 <xref:System.IServiceProvider> 的访问。</span><span class="sxs-lookup"><span data-stu-id="8e77c-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="8e77c-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>：为支持用户会话定义 `ISessionFactory` 和 <xref:Microsoft.AspNetCore.Http.ISession> 抽象。</span><span class="sxs-lookup"><span data-stu-id="8e77c-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="8e77c-150">`ISessionFeature` 是由 <xref:Microsoft.AspNetCore.Session.SessionMiddleware> 实现的（请参见 <xref:fundamentals/app-state>）。</span><span class="sxs-lookup"><span data-stu-id="8e77c-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="8e77c-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>：定义用于检索客户端证书的 API。</span><span class="sxs-lookup"><span data-stu-id="8e77c-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="8e77c-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>：定义使用 TLS 令牌绑定参数的方法。</span><span class="sxs-lookup"><span data-stu-id="8e77c-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="8e77c-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>：用于查询、授予和撤消有关存储与站点活动和功能相关的用户信息的用户同意。</span><span class="sxs-lookup"><span data-stu-id="8e77c-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8e77c-154">其他资源</span><span class="sxs-lookup"><span data-stu-id="8e77c-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
