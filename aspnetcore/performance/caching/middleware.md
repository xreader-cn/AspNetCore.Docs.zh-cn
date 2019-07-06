---
title: 响应缓存在 ASP.NET Core 中的中间件
author: guardrex
description: 了解如何配置和 ASP.NET Core 中使用缓存响应的中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: performance/caching/middleware
ms.openlocfilehash: d6756ce16396133da643cc08ca0f48369479ad3a
ms.sourcegitcommit: b9e914ef274b5ec359582f299724af6234dce135
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596154"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="868c0-103">响应缓存在 ASP.NET Core 中的中间件</span><span class="sxs-lookup"><span data-stu-id="868c0-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="868c0-104">通过[Luke Latham](https://github.com/guardrex)和[John 卢奥语](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="868c0-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="868c0-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="868c0-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="868c0-106">此文章介绍了如何在 ASP.NET Core 应用程序中配置缓存响应的中间件。</span><span class="sxs-lookup"><span data-stu-id="868c0-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="868c0-107">中间件确定何时可缓存的响应、 存储响应和从缓存提供服务响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="868c0-108">有关 HTTP 缓存的介绍和[[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性，请参阅[响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="868c0-108">For an introduction to HTTP caching and the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="configuration"></a><span data-ttu-id="868c0-109">配置</span><span class="sxs-lookup"><span data-stu-id="868c0-109">Configuration</span></span>

<span data-ttu-id="868c0-110">使用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或添加到的包引用[Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包。</span><span class="sxs-lookup"><span data-stu-id="868c0-110">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="868c0-111">在`Startup.ConfigureServices`，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="868c0-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="868c0-112">将应用配置为使用与中间件<xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>扩展方法，它将中间件添加到请求处理管道中`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="868c0-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="868c0-113">示例应用程序将添加要在后续请求缓存控制标头：</span><span class="sxs-lookup"><span data-stu-id="868c0-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="868c0-114">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)&ndash;缓存最多 10 秒的可缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="868c0-115">[改变](https://tools.ietf.org/html/rfc7231#section-7.1.4)&ndash;配置中间件来提供缓存的响应才[ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4)后续请求标头匹配的原始请求。</span><span class="sxs-lookup"><span data-stu-id="868c0-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="868c0-116">响应缓存中间件仅缓存导致 200 （正常） 状态代码的服务器响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="868c0-117">任何其他响应，其中包括[错误页](xref:fundamentals/error-handling)，将忽略由中间件。</span><span class="sxs-lookup"><span data-stu-id="868c0-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="868c0-118">响应包含经过身份验证的客户端的内容必须标记为不可缓存，以防止从存储和处理这些响应中间件。</span><span class="sxs-lookup"><span data-stu-id="868c0-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="868c0-119">请参阅[缓存的条件](#conditions-for-caching)有关中间件如何确定响应是否可缓存的详细信息。</span><span class="sxs-lookup"><span data-stu-id="868c0-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="868c0-120">选项</span><span class="sxs-lookup"><span data-stu-id="868c0-120">Options</span></span>

<span data-ttu-id="868c0-121">下表中显示响应缓存选项。</span><span class="sxs-lookup"><span data-stu-id="868c0-121">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="868c0-122">选项</span><span class="sxs-lookup"><span data-stu-id="868c0-122">Option</span></span> | <span data-ttu-id="868c0-123">描述</span><span class="sxs-lookup"><span data-stu-id="868c0-123">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="868c0-124">以字节为单位的响应正文最大缓存大小。</span><span class="sxs-lookup"><span data-stu-id="868c0-124">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="868c0-125">默认值是`64 * 1024 * 1024`(64 MB)。</span><span class="sxs-lookup"><span data-stu-id="868c0-125">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="868c0-126">以字节为单位的响应缓存中间件大小限制。</span><span class="sxs-lookup"><span data-stu-id="868c0-126">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="868c0-127">默认值是`100 * 1024 * 1024`(100 MB)。</span><span class="sxs-lookup"><span data-stu-id="868c0-127">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="868c0-128">确定是否在区分大小写的路径上会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="868c0-129">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="868c0-129">The default value is `false`.</span></span> |

<span data-ttu-id="868c0-130">下面的示例配置到中间件：</span><span class="sxs-lookup"><span data-stu-id="868c0-130">The following example configures the middleware to:</span></span>

* <span data-ttu-id="868c0-131">缓存大小小于或等于 1024 字节的正文的响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-131">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="868c0-132">将响应存储通过区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="868c0-132">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="868c0-133">例如，`/page1`和`/Page1`分开存储。</span><span class="sxs-lookup"><span data-stu-id="868c0-133">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="868c0-134">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="868c0-134">VaryByQueryKeys</span></span>

<span data-ttu-id="868c0-135">当使用 MVC / web API 控制器或 Razor 页面页模型[[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性指定设置适当的标头为响应缓存所需的参数。</span><span class="sxs-lookup"><span data-stu-id="868c0-135">When using MVC / web API controllers or Razor Pages page models, the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="868c0-136">唯一参数`[ResponseCache]`严格需要中间件的属性是<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>，这并不对应于实际的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="868c0-136">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="868c0-137">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="868c0-137">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="868c0-138">不使用时`[ResponseCache]`属性中，响应缓存可以与变化`VaryByQueryKeys`。</span><span class="sxs-lookup"><span data-stu-id="868c0-138">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="868c0-139">使用<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span><span class="sxs-lookup"><span data-stu-id="868c0-139">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="868c0-140">使用单个值等于`*`在`VaryByQueryKeys`随缓存所有请求查询参数而都变化。</span><span class="sxs-lookup"><span data-stu-id="868c0-140">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="868c0-141">响应缓存中间件所使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="868c0-141">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="868c0-142">下表提供有关影响缓存响应的 HTTP 标头信息。</span><span class="sxs-lookup"><span data-stu-id="868c0-142">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="868c0-143">Header</span><span class="sxs-lookup"><span data-stu-id="868c0-143">Header</span></span> | <span data-ttu-id="868c0-144">详细信息</span><span class="sxs-lookup"><span data-stu-id="868c0-144">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="868c0-145">如果标头存在，不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-145">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="868c0-146">中间件只考虑缓存响应标有`public`缓存指令。</span><span class="sxs-lookup"><span data-stu-id="868c0-146">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="868c0-147">控制缓存使用以下参数：</span><span class="sxs-lookup"><span data-stu-id="868c0-147">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="868c0-148">max-age</span><span class="sxs-lookup"><span data-stu-id="868c0-148">max-age</span></span></li><li><span data-ttu-id="868c0-149">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="868c0-149">max-stale&#8224;</span></span></li><li><span data-ttu-id="868c0-150">min-fresh</span><span class="sxs-lookup"><span data-stu-id="868c0-150">min-fresh</span></span></li><li><span data-ttu-id="868c0-151">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="868c0-151">must-revalidate</span></span></li><li><span data-ttu-id="868c0-152">无缓存</span><span class="sxs-lookup"><span data-stu-id="868c0-152">no-cache</span></span></li><li><span data-ttu-id="868c0-153">无存储</span><span class="sxs-lookup"><span data-stu-id="868c0-153">no-store</span></span></li><li><span data-ttu-id="868c0-154">only-if-cached</span><span class="sxs-lookup"><span data-stu-id="868c0-154">only-if-cached</span></span></li><li><span data-ttu-id="868c0-155">private</span><span class="sxs-lookup"><span data-stu-id="868c0-155">private</span></span></li><li><span data-ttu-id="868c0-156">public</span><span class="sxs-lookup"><span data-stu-id="868c0-156">public</span></span></li><li><span data-ttu-id="868c0-157">s maxage</span><span class="sxs-lookup"><span data-stu-id="868c0-157">s-maxage</span></span></li><li><span data-ttu-id="868c0-158">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="868c0-158">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="868c0-159">&#8224;如果没有限制指定为`max-stale`，中间件不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="868c0-159">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="868c0-160">&#8225;`proxy-revalidate`具有相同的效果`must-revalidate`。</span><span class="sxs-lookup"><span data-stu-id="868c0-160">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="868c0-161">有关详细信息，请参阅[RFC 7231:请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="868c0-161">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="868c0-162">一个`Pragma: no-cache`请求标头中的生成相同的效果`Cache-Control: no-cache`。</span><span class="sxs-lookup"><span data-stu-id="868c0-162">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="868c0-163">此标头中的相关指令重写`Cache-Control`标头，如果存在。</span><span class="sxs-lookup"><span data-stu-id="868c0-163">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="868c0-164">考虑使用 HTTP/1.0 的向后兼容性。</span><span class="sxs-lookup"><span data-stu-id="868c0-164">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="868c0-165">如果标头存在，不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-165">The response isn't cached if the header exists.</span></span> <span data-ttu-id="868c0-166">设置一个或多个 cookie 在请求处理管道中的任何中间件可防止响应缓存中间件缓存响应 (例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata))。</span><span class="sxs-lookup"><span data-stu-id="868c0-166">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="868c0-167">`Vary`标头可用于不同的缓存的响应的另一个标头。</span><span class="sxs-lookup"><span data-stu-id="868c0-167">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="868c0-168">例如，缓存响应的编码，方法是包括`Vary: Accept-Encoding`标头，来缓存响应的请求的标头`Accept-Encoding: gzip`和`Accept-Encoding: text/plain`单独。</span><span class="sxs-lookup"><span data-stu-id="868c0-168">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="868c0-169">响应标头值为`*`永远不会存储。</span><span class="sxs-lookup"><span data-stu-id="868c0-169">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="868c0-170">此标头被视为陈旧的响应不是存储或检索除非重写由其他`Cache-Control`标头。</span><span class="sxs-lookup"><span data-stu-id="868c0-170">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="868c0-171">如果值不是完整的响应从缓存提供`*`和`ETag`的响应中不匹配任何提供的值。</span><span class="sxs-lookup"><span data-stu-id="868c0-171">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="868c0-172">否则，就会提供 304 （未修改） 响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-172">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="868c0-173">如果`If-None-Match`标头不存在，如果缓存的响应日期比提供的值的完整响应从缓存提供。</span><span class="sxs-lookup"><span data-stu-id="868c0-173">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="868c0-174">否则为*304-未修改*提供响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-174">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="868c0-175">从缓存中，提供服务时`Date`由中间件设置标头，如果它未提供原始响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-175">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="868c0-176">从缓存中，提供服务时`Content-Length`由中间件设置标头，如果它未提供原始响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-176">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="868c0-177">`Age`忽略原始响应中发送的标头。</span><span class="sxs-lookup"><span data-stu-id="868c0-177">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="868c0-178">提供缓存的响应时，中间件会计算新值。</span><span class="sxs-lookup"><span data-stu-id="868c0-178">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="868c0-179">缓存遵循请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="868c0-179">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="868c0-180">中间件遵从的规则[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)。</span><span class="sxs-lookup"><span data-stu-id="868c0-180">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="868c0-181">规则需要遵守是有效的缓存`Cache-Control`客户端发送的标头。</span><span class="sxs-lookup"><span data-stu-id="868c0-181">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="868c0-182">在规范中，客户端可以发出请求的`no-cache`标头值和强制服务器生成的每个请求的新响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-182">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="868c0-183">目前，没有任何开发人员可以控制此缓存行为时使用中间件，因为中间件符合官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="868c0-183">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="868c0-184">为了更好地控制缓存行为，将介绍其他缓存功能的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="868c0-184">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="868c0-185">请参见下面的主题：</span><span class="sxs-lookup"><span data-stu-id="868c0-185">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="868c0-186">疑难解答</span><span class="sxs-lookup"><span data-stu-id="868c0-186">Troubleshooting</span></span>

<span data-ttu-id="868c0-187">如果缓存行为不会按预期方式，，确认响应是可缓存，能够从缓存中提供。</span><span class="sxs-lookup"><span data-stu-id="868c0-187">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="868c0-188">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="868c0-188">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="868c0-189">启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="868c0-189">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="868c0-190">当测试和故障排除的缓存行为，浏览器可以设置影响缓存产生不利的请求标头。</span><span class="sxs-lookup"><span data-stu-id="868c0-190">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="868c0-191">例如，设置浏览器可能`Cache-Control`标头`no-cache`或`max-age=0`刷新页面时。</span><span class="sxs-lookup"><span data-stu-id="868c0-191">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="868c0-192">以下工具可以显式设置请求标头，并且是测试缓存的首选：</span><span class="sxs-lookup"><span data-stu-id="868c0-192">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="868c0-193">Fiddler</span><span class="sxs-lookup"><span data-stu-id="868c0-193">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="868c0-194">Postman</span><span class="sxs-lookup"><span data-stu-id="868c0-194">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="868c0-195">缓存的条件</span><span class="sxs-lookup"><span data-stu-id="868c0-195">Conditions for caching</span></span>

* <span data-ttu-id="868c0-196">请求必须导致服务器响应 200 （正常） 状态代码。</span><span class="sxs-lookup"><span data-stu-id="868c0-196">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="868c0-197">请求方法必须是 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="868c0-197">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="868c0-198">在`Startup.Configure`，必须在需要缓存的中间件前面放置响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="868c0-198">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="868c0-199">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="868c0-199">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="868c0-200">`Authorization`标头不能存在。</span><span class="sxs-lookup"><span data-stu-id="868c0-200">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="868c0-201">`Cache-Control` 标头参数必须是有效的并且必须标记为响应`public`且未标记为`private`。</span><span class="sxs-lookup"><span data-stu-id="868c0-201">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="868c0-202">`Pragma: no-cache`标头不能存在如果`Cache-Control`标头不存在，作为`Cache-Control`标头重写`Pragma`标头时存在。</span><span class="sxs-lookup"><span data-stu-id="868c0-202">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="868c0-203">`Set-Cookie`标头不能存在。</span><span class="sxs-lookup"><span data-stu-id="868c0-203">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="868c0-204">`Vary` 标头参数必须是有效且不等于`*`。</span><span class="sxs-lookup"><span data-stu-id="868c0-204">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="868c0-205">`Content-Length`标头值 (如果设置) 必须与匹配的响应正文的大小。</span><span class="sxs-lookup"><span data-stu-id="868c0-205">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="868c0-206"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>未使用。</span><span class="sxs-lookup"><span data-stu-id="868c0-206">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="868c0-207">响应不能由指定陈旧`Expires`标头和`max-age`和`s-maxage`缓存指令。</span><span class="sxs-lookup"><span data-stu-id="868c0-207">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="868c0-208">响应缓冲必须成功完成。</span><span class="sxs-lookup"><span data-stu-id="868c0-208">Response buffering must be successful.</span></span> <span data-ttu-id="868c0-209">响应的大小必须小于已配置或默认<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>。</span><span class="sxs-lookup"><span data-stu-id="868c0-209">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="868c0-210">响应的正文大小必须小于已配置或默认<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>。</span><span class="sxs-lookup"><span data-stu-id="868c0-210">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="868c0-211">响应必须是可根据缓存[RFC 7234](https://tools.ietf.org/html/rfc7234)规范。</span><span class="sxs-lookup"><span data-stu-id="868c0-211">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="868c0-212">例如，`no-store`指令不能存在请求或响应标头字段中。</span><span class="sxs-lookup"><span data-stu-id="868c0-212">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="868c0-213">请参阅*第 3 部分：在缓存中存储响应*的[RFC 7234](https://tools.ietf.org/html/rfc7234)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="868c0-213">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="868c0-214">防伪系统用于生成安全令牌，以防止跨站点请求伪造 (CSRF) 攻击集`Cache-Control`并`Pragma`标头`no-cache`，以便不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="868c0-214">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="868c0-215">有关如何禁用 HTML 窗体元素的防伪令牌的信息，请参阅<xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>。</span><span class="sxs-lookup"><span data-stu-id="868c0-215">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="868c0-216">其他资源</span><span class="sxs-lookup"><span data-stu-id="868c0-216">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
