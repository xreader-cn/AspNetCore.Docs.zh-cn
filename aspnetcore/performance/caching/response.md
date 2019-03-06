---
title: 响应缓存在 ASP.NET Core
author: rick-anderson
description: 了解如何使用缓存到较低带宽要求的响应，并增加的 ASP.NET Core 应用的性能。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/28/2019
uid: performance/caching/response
ms.openlocfilehash: efcf443b1487827fe6cf4d43b6dda69adf4d61fb
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345741"
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="4aece-103">响应缓存在 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4aece-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="4aece-104">通过[John 卢奥语](https://github.com/JunTaoLuo)， [Rick Anderson](https://twitter.com/RickAndMSFT)， [Steve Smith](https://ardalis.com/)，和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4aece-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4aece-105">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4aece-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4aece-106">响应缓存可减少客户端或代理到 web 服务器发出的请求数。</span><span class="sxs-lookup"><span data-stu-id="4aece-106">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="4aece-107">响应缓存还减少了工作的 web 服务器执行以生成响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-107">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="4aece-108">响应缓存控制标头，指定要如何客户端、 代理和响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="4aece-108">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="4aece-109">[ResponseCache 属性](#responsecache-attribute)参与设置缓存标头，哪些客户端可能会接受缓存的响应时的响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-109">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers, which clients may honor when caching responses.</span></span> <span data-ttu-id="4aece-110">[响应缓存中间件](xref:performance/caching/middleware)可用于在服务器上的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-110">[Response Caching Middleware](xref:performance/caching/middleware) can be used to cache responses on the server.</span></span> <span data-ttu-id="4aece-111">可以使用中间件<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>属性，以改变服务器端的缓存行为。</span><span class="sxs-lookup"><span data-stu-id="4aece-111">The middleware can use <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="4aece-112">基于 HTTP 的响应缓存</span><span class="sxs-lookup"><span data-stu-id="4aece-112">HTTP-based response caching</span></span>

<span data-ttu-id="4aece-113">[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)介绍 Internet 缓存的行为方式。</span><span class="sxs-lookup"><span data-stu-id="4aece-113">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="4aece-114">是用于缓存的主 HTTP 标头[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)，用于指定缓存*指令*。</span><span class="sxs-lookup"><span data-stu-id="4aece-114">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="4aece-115">指令控制缓存行为，随着请求来自客户端对服务器进行自己的方式以及响应回客户端从服务器进行自己的方式。</span><span class="sxs-lookup"><span data-stu-id="4aece-115">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="4aece-116">请求和响应将通过代理服务器和代理服务器还必须符合 HTTP 1.1 缓存规范。</span><span class="sxs-lookup"><span data-stu-id="4aece-116">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="4aece-117">常见`Cache-Control`指令下表中所示。</span><span class="sxs-lookup"><span data-stu-id="4aece-117">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="4aece-118">指令</span><span class="sxs-lookup"><span data-stu-id="4aece-118">Directive</span></span>                                                       | <span data-ttu-id="4aece-119">操作</span><span class="sxs-lookup"><span data-stu-id="4aece-119">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="4aece-120">public</span><span class="sxs-lookup"><span data-stu-id="4aece-120">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="4aece-121">缓存可能会存储响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-121">A cache may store the response.</span></span> |
| [<span data-ttu-id="4aece-122">private</span><span class="sxs-lookup"><span data-stu-id="4aece-122">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="4aece-123">响应不是由共享缓存存储。</span><span class="sxs-lookup"><span data-stu-id="4aece-123">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="4aece-124">专用缓存可能会存储和重用响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-124">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="4aece-125">max-age</span><span class="sxs-lookup"><span data-stu-id="4aece-125">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="4aece-126">客户端不接受其年龄大于指定的秒数的响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-126">The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="4aece-127">示例：`max-age=60` （60 秒）， `max-age=2592000` （1 个月）</span><span class="sxs-lookup"><span data-stu-id="4aece-127">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="4aece-128">no-cache</span><span class="sxs-lookup"><span data-stu-id="4aece-128">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="4aece-129">**在请求**:缓存必须使用存储的响应来满足请求。</span><span class="sxs-lookup"><span data-stu-id="4aece-129">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="4aece-130">源服务器重新响应生成的客户端和中间件更新其缓存中存储的响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-130">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="4aece-131">**响应**:响应不必须用于不带验证的源服务器上的后续请求。</span><span class="sxs-lookup"><span data-stu-id="4aece-131">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="4aece-132">no-store</span><span class="sxs-lookup"><span data-stu-id="4aece-132">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="4aece-133">**在请求**:缓存不得存储请求。</span><span class="sxs-lookup"><span data-stu-id="4aece-133">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="4aece-134">**响应**:缓存不得存储任何响应的一部分。</span><span class="sxs-lookup"><span data-stu-id="4aece-134">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="4aece-135">下表中显示其他播放的角色中缓存的缓存标头。</span><span class="sxs-lookup"><span data-stu-id="4aece-135">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="4aece-136">Header</span><span class="sxs-lookup"><span data-stu-id="4aece-136">Header</span></span>                                                     | <span data-ttu-id="4aece-137">函数</span><span class="sxs-lookup"><span data-stu-id="4aece-137">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="4aece-138">年龄</span><span class="sxs-lookup"><span data-stu-id="4aece-138">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="4aece-139">以秒为单位因为响应已生成或在源服务器已成功验证的时间量的估计值。</span><span class="sxs-lookup"><span data-stu-id="4aece-139">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="4aece-140">Expires</span><span class="sxs-lookup"><span data-stu-id="4aece-140">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="4aece-141">过时的内容后，被视为响应的时间。</span><span class="sxs-lookup"><span data-stu-id="4aece-141">The time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="4aece-142">杂注</span><span class="sxs-lookup"><span data-stu-id="4aece-142">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="4aece-143">存在向后兼容性，使用 HTTP/1.0 缓存设置`no-cache`行为。</span><span class="sxs-lookup"><span data-stu-id="4aece-143">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="4aece-144">如果`Cache-Control`标头存在，则`Pragma`忽略标头。</span><span class="sxs-lookup"><span data-stu-id="4aece-144">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="4aece-145">改变</span><span class="sxs-lookup"><span data-stu-id="4aece-145">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="4aece-146">指定缓存的响应必须不发送除非所有的`Vary`中缓存的响应的原始请求和新的请求标头字段匹配。</span><span class="sxs-lookup"><span data-stu-id="4aece-146">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="4aece-147">基于 HTTP 的缓存方面请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="4aece-147">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="4aece-148">[HTTP 1.1 缓存的缓存控制标头的规范](https://tools.ietf.org/html/rfc7234#section-5.2)需要接受是有效的缓存`Cache-Control`客户端发送的标头。</span><span class="sxs-lookup"><span data-stu-id="4aece-148">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="4aece-149">客户端可以发出请求的`no-cache`标头值和强制服务器生成的每个请求的新响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-149">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="4aece-150">始终遵循客户端`Cache-Control`请求标头是有意义，如果您考虑 HTTP 缓存的目标。</span><span class="sxs-lookup"><span data-stu-id="4aece-150">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="4aece-151">在正式规范，缓存旨在减少的满足请求的客户端、 代理和服务器的网络延迟和网络开销。</span><span class="sxs-lookup"><span data-stu-id="4aece-151">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="4aece-152">它不一定是一种方法来控制源服务器上的负载。</span><span class="sxs-lookup"><span data-stu-id="4aece-152">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="4aece-153">没有任何开发人员可以控制此缓存的行为使用时[响应缓存中间件](xref:performance/caching/middleware)因为中间件遵循正式缓存规范。</span><span class="sxs-lookup"><span data-stu-id="4aece-153">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="4aece-154">[计划的中间件的增强功能](https://github.com/aspnet/AspNetCore/issues/2612)是配置要忽略的请求的中间件的机会`Cache-Control`标头决定用于缓存的响应时。</span><span class="sxs-lookup"><span data-stu-id="4aece-154">[Planned enhancements to the middleware](https://github.com/aspnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="4aece-155">计划内的增强功能提供更好地控制服务器负载的机会。</span><span class="sxs-lookup"><span data-stu-id="4aece-155">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="4aece-156">在 ASP.NET Core 中其他缓存技术</span><span class="sxs-lookup"><span data-stu-id="4aece-156">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="4aece-157">内存中缓存</span><span class="sxs-lookup"><span data-stu-id="4aece-157">In-memory caching</span></span>

<span data-ttu-id="4aece-158">内存中缓存使用的服务器内存来存储缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="4aece-158">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="4aece-159">此类型的缓存是适用于单个服务器或多个服务器使用*粘性会话*。</span><span class="sxs-lookup"><span data-stu-id="4aece-159">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="4aece-160">粘性会话意味着，客户端的请求始终路由到同一个服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="4aece-160">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="4aece-161">有关详细信息，请参阅 <xref:performance/caching/memory>。</span><span class="sxs-lookup"><span data-stu-id="4aece-161">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="4aece-162">分布式缓存</span><span class="sxs-lookup"><span data-stu-id="4aece-162">Distributed Cache</span></span>

<span data-ttu-id="4aece-163">使用分布式的缓存在云或服务器场中托管应用时，将数据存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="4aece-163">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="4aece-164">处理请求的服务器之间共享缓存。</span><span class="sxs-lookup"><span data-stu-id="4aece-164">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="4aece-165">客户端可以提交的请求，如果客户端的缓存的数据可由任何组中的服务器。</span><span class="sxs-lookup"><span data-stu-id="4aece-165">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="4aece-166">ASP.NET Core 提供 SQL Server 和分布式的 Redis 缓存。</span><span class="sxs-lookup"><span data-stu-id="4aece-166">ASP.NET Core offers SQL Server and Redis distributed caches.</span></span>

<span data-ttu-id="4aece-167">有关详细信息，请参阅 <xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="4aece-167">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="4aece-168">缓存标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="4aece-168">Cache Tag Helper</span></span>

<span data-ttu-id="4aece-169">与缓存标记帮助程序缓存中的 MVC 视图或 Razor 页面的内容。</span><span class="sxs-lookup"><span data-stu-id="4aece-169">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="4aece-170">缓存标记帮助程序使用内存中缓存来存储数据。</span><span class="sxs-lookup"><span data-stu-id="4aece-170">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="4aece-171">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="4aece-171">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="4aece-172">分布式缓存标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="4aece-172">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="4aece-173">使用分布式缓存标记帮助程序缓存中的 MVC 视图或 Razor 页面在分布式云或 web 场方案中的内容。</span><span class="sxs-lookup"><span data-stu-id="4aece-173">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="4aece-174">分布式缓存标记帮助程序使用 SQL Server 或 Redis 存储数据。</span><span class="sxs-lookup"><span data-stu-id="4aece-174">The Distributed Cache Tag Helper uses SQL Server or Redis to store data.</span></span>

<span data-ttu-id="4aece-175">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="4aece-175">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="4aece-176">ResponseCache 属性</span><span class="sxs-lookup"><span data-stu-id="4aece-176">ResponseCache attribute</span></span>

<span data-ttu-id="4aece-177"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>指定缓存响应中设置相应的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="4aece-177">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="4aece-178">禁用缓存的内容，其中包含已经过身份验证的客户端的信息。</span><span class="sxs-lookup"><span data-stu-id="4aece-178">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="4aece-179">仅应为不会更改基于用户的标识或是否在用户登录的内容启用缓存。</span><span class="sxs-lookup"><span data-stu-id="4aece-179">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="4aece-180"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 存储的响应因查询密钥的指定列表的值。</span><span class="sxs-lookup"><span data-stu-id="4aece-180"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="4aece-181">时的单个值`*`是所有的响应请求查询字符串参数提供中间件会有所不同。</span><span class="sxs-lookup"><span data-stu-id="4aece-181">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="4aece-182">[响应缓存中间件](xref:performance/caching/middleware)必须能够设置<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>属性。</span><span class="sxs-lookup"><span data-stu-id="4aece-182">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="4aece-183">否则，会引发运行时异常。</span><span class="sxs-lookup"><span data-stu-id="4aece-183">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="4aece-184">没有为相应的 HTTP 标头<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>属性。</span><span class="sxs-lookup"><span data-stu-id="4aece-184">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="4aece-185">该属性是由响应缓存中间件处理的 HTTP 功能。</span><span class="sxs-lookup"><span data-stu-id="4aece-185">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="4aece-186">中间件来提供缓存的响应，查询字符串和查询字符串值必须匹配上一个请求。</span><span class="sxs-lookup"><span data-stu-id="4aece-186">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="4aece-187">例如，考虑请求和下表中所示的结果的序列。</span><span class="sxs-lookup"><span data-stu-id="4aece-187">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="4aece-188">请求</span><span class="sxs-lookup"><span data-stu-id="4aece-188">Request</span></span>                          | <span data-ttu-id="4aece-189">结果</span><span class="sxs-lookup"><span data-stu-id="4aece-189">Result</span></span>                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | <span data-ttu-id="4aece-190">从服务器返回。</span><span class="sxs-lookup"><span data-stu-id="4aece-190">Returned from the server.</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="4aece-191">返回从中间件。</span><span class="sxs-lookup"><span data-stu-id="4aece-191">Returned from middleware.</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="4aece-192">从服务器返回。</span><span class="sxs-lookup"><span data-stu-id="4aece-192">Returned from the server.</span></span> |

<span data-ttu-id="4aece-193">第一个请求将由服务器返回并缓存在中间件中。</span><span class="sxs-lookup"><span data-stu-id="4aece-193">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="4aece-194">因为查询字符串匹配上一个请求，中间件会返回第二个请求。</span><span class="sxs-lookup"><span data-stu-id="4aece-194">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="4aece-195">因为查询字符串值不匹配上一个请求，第三个请求不是中间件缓存中。</span><span class="sxs-lookup"><span data-stu-id="4aece-195">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="4aece-196"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>用于配置和创建 (通过<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>。</span><span class="sxs-lookup"><span data-stu-id="4aece-196">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>.</span></span> <span data-ttu-id="4aece-197"><xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>执行更新的适当的 HTTP 标头和响应的功能的工作。</span><span class="sxs-lookup"><span data-stu-id="4aece-197">The <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter> performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="4aece-198">筛选器：</span><span class="sxs-lookup"><span data-stu-id="4aece-198">The filter:</span></span>

* <span data-ttu-id="4aece-199">删除任何现有标头`Vary`， `Cache-Control`，和`Pragma`。</span><span class="sxs-lookup"><span data-stu-id="4aece-199">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="4aece-200">写出适当的标头中设置的属性上基于<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>。</span><span class="sxs-lookup"><span data-stu-id="4aece-200">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="4aece-201">更新缓存项 HTTP 功能中，如果响应<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>设置。</span><span class="sxs-lookup"><span data-stu-id="4aece-201">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="4aece-202">改变</span><span class="sxs-lookup"><span data-stu-id="4aece-202">Vary</span></span>

<span data-ttu-id="4aece-203">此标头时仅写入<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader>属性设置。</span><span class="sxs-lookup"><span data-stu-id="4aece-203">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="4aece-204">将属性设置为`Vary`属性的值。</span><span class="sxs-lookup"><span data-stu-id="4aece-204">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="4aece-205">下面的示例使用<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader>属性：</span><span class="sxs-lookup"><span data-stu-id="4aece-205">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="4aece-206">使用示例应用程序，查看浏览器的网络工具的响应标头。</span><span class="sxs-lookup"><span data-stu-id="4aece-206">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="4aece-207">以下响应标头是与 Cache1 页面响应一起发送：</span><span class="sxs-lookup"><span data-stu-id="4aece-207">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="4aece-208">NoStore 和 Location.None</span><span class="sxs-lookup"><span data-stu-id="4aece-208">NoStore and Location.None</span></span>

<span data-ttu-id="4aece-209"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 重写的大多数其他属性。</span><span class="sxs-lookup"><span data-stu-id="4aece-209"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="4aece-210">当此属性设置为`true`，则`Cache-Control`标头设置为`no-store`。</span><span class="sxs-lookup"><span data-stu-id="4aece-210">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="4aece-211">如果<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>设置为`None`:</span><span class="sxs-lookup"><span data-stu-id="4aece-211">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="4aece-212">将 `Cache-Control` 设置为 `no-store,no-cache`。</span><span class="sxs-lookup"><span data-stu-id="4aece-212">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="4aece-213">将 `Pragma` 设置为 `no-cache`。</span><span class="sxs-lookup"><span data-stu-id="4aece-213">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="4aece-214">如果<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>是`false`并<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>是`None`， `Cache-Control`，并且`Pragma`设置为`no-cache`。</span><span class="sxs-lookup"><span data-stu-id="4aece-214">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="4aece-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 通常设置为`true`错误页。</span><span class="sxs-lookup"><span data-stu-id="4aece-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="4aece-216">示例应用程序中的 Cache2 页面生成响应标头，指示客户端不存储响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-216">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="4aece-217">示例应用程序返回 Cache2 页面包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="4aece-217">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="4aece-218">位置和持续时间</span><span class="sxs-lookup"><span data-stu-id="4aece-218">Location and Duration</span></span>

<span data-ttu-id="4aece-219">若要启用缓存，<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>必须设置为正值和<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>必须是`Any`（默认值） 或`Client`。</span><span class="sxs-lookup"><span data-stu-id="4aece-219">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="4aece-220">在这种情况下，`Cache-Control`标头设置为位置值后, 接`max-age`的响应。</span><span class="sxs-lookup"><span data-stu-id="4aece-220">In this case, the `Cache-Control` header is set to the location value followed by the `max-age` of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="4aece-221"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>选项`Any`并`Client`转换为`Cache-Control`标头值的`public`和`private`分别。</span><span class="sxs-lookup"><span data-stu-id="4aece-221"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="4aece-222">按前面所述，设置<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>到`None`设置同时`Cache-Control`并`Pragma`标头`no-cache`。</span><span class="sxs-lookup"><span data-stu-id="4aece-222">As noted previously, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="4aece-223">下面的示例演示 Cache3 页面模型从示例应用和通过如下设置生成的标头<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>并保留默认值<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>值：</span><span class="sxs-lookup"><span data-stu-id="4aece-223">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="4aece-224">示例应用程序返回 Cache3 页面包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="4aece-224">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="4aece-225">缓存配置文件</span><span class="sxs-lookup"><span data-stu-id="4aece-225">Cache profiles</span></span>

<span data-ttu-id="4aece-226">而不是重复响应缓存设置在多个控制器操作属性，设置 MVC/Razor 页面时可作为选项配置缓存配置文件`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="4aece-226">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4aece-227">引用的缓存配置文件中找到的值用作默认值由<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>和由属性指定任何属性重写。</span><span class="sxs-lookup"><span data-stu-id="4aece-227">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="4aece-228">设置缓存配置文件。</span><span class="sxs-lookup"><span data-stu-id="4aece-228">Set up a cache profile.</span></span> <span data-ttu-id="4aece-229">下面的示例演示 30 的第二个缓存配置文件在示例应用的`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4aece-229">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="4aece-230">示例应用 Cache4 页面模型引用`Default30`缓存配置文件：</span><span class="sxs-lookup"><span data-stu-id="4aece-230">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="4aece-231"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>可应用于：</span><span class="sxs-lookup"><span data-stu-id="4aece-231">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* <span data-ttu-id="4aece-232">Razor 页面处理程序 （类）&ndash;属性不能应用于处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="4aece-232">Razor Page handlers (classes) &ndash; Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="4aece-233">MVC 控制器 （类）。</span><span class="sxs-lookup"><span data-stu-id="4aece-233">MVC controllers (classes).</span></span>
* <span data-ttu-id="4aece-234">MVC 操作 （方法）&ndash;方法级属性重写在类级别特性中指定的设置。</span><span class="sxs-lookup"><span data-stu-id="4aece-234">MVC actions (methods) &ndash; Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="4aece-235">生成的标头应用到由该 Cache4 页响应`Default30`缓存配置文件：</span><span class="sxs-lookup"><span data-stu-id="4aece-235">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="4aece-236">其他资源</span><span class="sxs-lookup"><span data-stu-id="4aece-236">Additional resources</span></span>

* [<span data-ttu-id="4aece-237">在缓存中存储的响应</span><span class="sxs-lookup"><span data-stu-id="4aece-237">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="4aece-238">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="4aece-238">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
