---
title: 响应缓存在 ASP.NET Core 中的中间件
author: guardrex
description: 了解如何配置和 ASP.NET Core 中使用缓存响应的中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/caching/middleware
ms.openlocfilehash: 61fa42161560ce2b512a73f1d7e32d11cd9bcb2c
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114791"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="f1217-103">响应缓存在 ASP.NET Core 中的中间件</span><span class="sxs-lookup"><span data-stu-id="f1217-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="f1217-104">作者： [Luke Latham](https://github.com/guardrex)和[John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="f1217-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1217-105">此文章介绍了如何在 ASP.NET Core 应用程序中配置缓存响应的中间件。</span><span class="sxs-lookup"><span data-stu-id="f1217-105">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="f1217-106">中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-106">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="f1217-107">有关 HTTP 缓存和[`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性的介绍，请参阅[响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="f1217-107">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="f1217-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f1217-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="f1217-109">配置</span><span class="sxs-lookup"><span data-stu-id="f1217-109">Configuration</span></span>

<span data-ttu-id="f1217-110">响应缓存中间件可通过共享框架隐式地用于 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f1217-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="f1217-111">在 `Startup.ConfigureServices`中，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="f1217-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="f1217-112">将应用程序配置为将中间件与 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 扩展方法一起使用，该方法将中间件添加到 `Startup.Configure`中的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="f1217-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

<span data-ttu-id="f1217-113">示例应用添加标头以在后续请求时控制缓存：</span><span class="sxs-lookup"><span data-stu-id="f1217-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="f1217-114">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)&ndash; 将可缓存的响应缓存多达10秒。</span><span class="sxs-lookup"><span data-stu-id="f1217-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="f1217-115">[不同](https://tools.ietf.org/html/rfc7231#section-7.1.4)&ndash; 将中间件配置为仅当后续请求的 Accept 编码标头与原始请求的[接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)标头匹配时才提供缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="f1217-116">响应缓存中间件仅缓存服务器响应，导致了200（正常）状态代码。</span><span class="sxs-lookup"><span data-stu-id="f1217-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="f1217-117">中间件将忽略任何其他响应，包括[错误页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="f1217-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="f1217-118">包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="f1217-119">有关中间件如何确定响应是否可缓存的详细信息，请参阅[缓存的条件](#conditions-for-caching)。</span><span class="sxs-lookup"><span data-stu-id="f1217-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="f1217-120">Options</span><span class="sxs-lookup"><span data-stu-id="f1217-120">Options</span></span>

<span data-ttu-id="f1217-121">响应缓存选项如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="f1217-121">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="f1217-122">选项</span><span class="sxs-lookup"><span data-stu-id="f1217-122">Option</span></span> | <span data-ttu-id="f1217-123">说明</span><span class="sxs-lookup"><span data-stu-id="f1217-123">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="f1217-124">响应正文的最大可缓存大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f1217-124">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="f1217-125">默认值为 `64 * 1024 * 1024` （64 MB）。</span><span class="sxs-lookup"><span data-stu-id="f1217-125">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="f1217-126">响应缓存中间件的大小限制（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f1217-126">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="f1217-127">默认值为 `100 * 1024 * 1024` （100 MB）。</span><span class="sxs-lookup"><span data-stu-id="f1217-127">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="f1217-128">确定是否将响应缓存在区分大小写的路径上。</span><span class="sxs-lookup"><span data-stu-id="f1217-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="f1217-129">默认值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="f1217-129">The default value is `false`.</span></span> |

<span data-ttu-id="f1217-130">下面的示例将中间件配置为：</span><span class="sxs-lookup"><span data-stu-id="f1217-130">The following example configures the middleware to:</span></span>

* <span data-ttu-id="f1217-131">大小小于或等于1024字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-131">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="f1217-132">将响应存储为区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="f1217-132">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="f1217-133">例如，`/page1` 和 `/Page1` 单独存储。</span><span class="sxs-lookup"><span data-stu-id="f1217-133">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="f1217-134">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="f1217-134">VaryByQueryKeys</span></span>

<span data-ttu-id="f1217-135">使用 MVC/web API 控制器或 Razor Pages 页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性指定为响应缓存设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="f1217-135">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="f1217-136">严格需要中间件的 `[ResponseCache]` 属性的唯一参数 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>，这与实际 HTTP 标头不对应。</span><span class="sxs-lookup"><span data-stu-id="f1217-136">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="f1217-137">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="f1217-137">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="f1217-138">如果不使用 `[ResponseCache]` 属性，响应缓存可能会与 `VaryByQueryKeys`不同。</span><span class="sxs-lookup"><span data-stu-id="f1217-138">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="f1217-139">直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>：</span><span class="sxs-lookup"><span data-stu-id="f1217-139">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="f1217-140">使用 `VaryByQueryKeys` 中 `*` 的单个值将按所有请求查询参数改变缓存。</span><span class="sxs-lookup"><span data-stu-id="f1217-140">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="f1217-141">响应缓存中间件使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="f1217-141">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="f1217-142">下表提供了有关影响响应缓存的 HTTP 标头的信息。</span><span class="sxs-lookup"><span data-stu-id="f1217-142">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="f1217-143">标头</span><span class="sxs-lookup"><span data-stu-id="f1217-143">Header</span></span> | <span data-ttu-id="f1217-144">详细信息</span><span class="sxs-lookup"><span data-stu-id="f1217-144">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="f1217-145">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-145">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="f1217-146">中间件仅考虑用 `public` 缓存指令标记的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-146">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="f1217-147">具有以下参数的控件缓存：</span><span class="sxs-lookup"><span data-stu-id="f1217-147">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="f1217-148">max-age</span><span class="sxs-lookup"><span data-stu-id="f1217-148">max-age</span></span></li><li><span data-ttu-id="f1217-149">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="f1217-149">max-stale&#8224;</span></span></li><li><span data-ttu-id="f1217-150">最小-新</span><span class="sxs-lookup"><span data-stu-id="f1217-150">min-fresh</span></span></li><li><span data-ttu-id="f1217-151">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="f1217-151">must-revalidate</span></span></li><li><span data-ttu-id="f1217-152">no-cache</span><span class="sxs-lookup"><span data-stu-id="f1217-152">no-cache</span></span></li><li><span data-ttu-id="f1217-153">无-商店</span><span class="sxs-lookup"><span data-stu-id="f1217-153">no-store</span></span></li><li><span data-ttu-id="f1217-154">仅限-缓存</span><span class="sxs-lookup"><span data-stu-id="f1217-154">only-if-cached</span></span></li><li><span data-ttu-id="f1217-155">专用</span><span class="sxs-lookup"><span data-stu-id="f1217-155">private</span></span></li><li><span data-ttu-id="f1217-156">public</span><span class="sxs-lookup"><span data-stu-id="f1217-156">public</span></span></li><li><span data-ttu-id="f1217-157">s-maxage</span><span class="sxs-lookup"><span data-stu-id="f1217-157">s-maxage</span></span></li><li><span data-ttu-id="f1217-158">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="f1217-158">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="f1217-159">&#8224;如果没有指定 `max-stale`的限制，则中间件不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="f1217-159">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="f1217-160">&#8225;`proxy-revalidate` 与 `must-revalidate`的效果相同。</span><span class="sxs-lookup"><span data-stu-id="f1217-160">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="f1217-161">有关详细信息，请参阅[RFC 7231：请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="f1217-161">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="f1217-162">请求中的 `Pragma: no-cache` 标头将产生与 `Cache-Control: no-cache`相同的效果。</span><span class="sxs-lookup"><span data-stu-id="f1217-162">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="f1217-163">如果存在此标头，则由 `Cache-Control` 标头中的相关指令重写。</span><span class="sxs-lookup"><span data-stu-id="f1217-163">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="f1217-164">考虑向后兼容 HTTP/1.0。</span><span class="sxs-lookup"><span data-stu-id="f1217-164">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="f1217-165">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-165">The response isn't cached if the header exists.</span></span> <span data-ttu-id="f1217-166">请求处理管道中设置一个或多个 cookie 的任何中间件会阻止响应缓存中间件缓存响应（例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata)）。</span><span class="sxs-lookup"><span data-stu-id="f1217-166">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="f1217-167">`Vary` 标头用于根据另一个标头改变缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-167">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="f1217-168">例如，通过编码来缓存响应，包括 `Vary: Accept-Encoding` 标头，该标头将缓存标头为 `Accept-Encoding: gzip` 和 `Accept-Encoding: text/plain` 的请求的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-168">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="f1217-169">永远不会存储标头值为 `*` 的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-169">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="f1217-170">除非被其他 `Cache-Control` 标头重写，否则不会存储或检索此标头过时的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-170">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="f1217-171">如果值不为 `*`，响应的 `ETag` 与提供的任何值都不匹配，则将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-171">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="f1217-172">否则，将提供304（未修改）响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-172">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="f1217-173">如果 `If-None-Match` 标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-173">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="f1217-174">否则，将提供*304-未修改*响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-174">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="f1217-175">从缓存提供时，如果未在原始响应中提供，则中间件会设置 `Date` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-175">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="f1217-176">从缓存提供时，如果未在原始响应中提供，则中间件会设置 `Content-Length` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-176">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="f1217-177">忽略原始响应中发送的 `Age` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-177">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="f1217-178">中间件在为缓存的响应提供服务时计算一个新值。</span><span class="sxs-lookup"><span data-stu-id="f1217-178">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="f1217-179">缓存遵从请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="f1217-179">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="f1217-180">中间件遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。</span><span class="sxs-lookup"><span data-stu-id="f1217-180">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="f1217-181">规则要求使用缓存来服从客户端发送的有效 `Cache-Control` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-181">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="f1217-182">在规范下，客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-182">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="f1217-183">目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="f1217-183">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="f1217-184">为了更好地控制缓存行为，将介绍其他缓存功能的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="f1217-184">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="f1217-185">请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="f1217-185">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="f1217-186">故障排除</span><span class="sxs-lookup"><span data-stu-id="f1217-186">Troubleshooting</span></span>

<span data-ttu-id="f1217-187">如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。</span><span class="sxs-lookup"><span data-stu-id="f1217-187">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="f1217-188">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-188">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="f1217-189">启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="f1217-189">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="f1217-190">在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-190">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="f1217-191">例如，浏览器可以将 `Cache-Control` 标题设置为刷新页面时 `no-cache` 或 `max-age=0`。</span><span class="sxs-lookup"><span data-stu-id="f1217-191">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="f1217-192">以下工具可以显式设置请求标头，并优先于测试缓存：</span><span class="sxs-lookup"><span data-stu-id="f1217-192">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="f1217-193">Fiddler</span><span class="sxs-lookup"><span data-stu-id="f1217-193">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="f1217-194">Postman</span><span class="sxs-lookup"><span data-stu-id="f1217-194">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="f1217-195">缓存条件</span><span class="sxs-lookup"><span data-stu-id="f1217-195">Conditions for caching</span></span>

* <span data-ttu-id="f1217-196">请求必须导致服务器响应，状态代码为200（正常）。</span><span class="sxs-lookup"><span data-stu-id="f1217-196">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="f1217-197">请求方法必须为 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="f1217-197">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="f1217-198">在 `Startup.Configure`中，响应缓存中间件必须置于需要缓存的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="f1217-198">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="f1217-199">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="f1217-199">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="f1217-200">`Authorization` 标头不得存在。</span><span class="sxs-lookup"><span data-stu-id="f1217-200">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="f1217-201">`Cache-Control` 标头参数必须是有效的，并且响应必须标记为 "`public`" 且未标记为 "`private`"。</span><span class="sxs-lookup"><span data-stu-id="f1217-201">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="f1217-202">如果 `Cache-Control` 标头不存在，则 `Pragma: no-cache` 标头不得存在，因为 `Cache-Control` 标头在存在时将覆盖 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-202">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="f1217-203">`Set-Cookie` 标头不得存在。</span><span class="sxs-lookup"><span data-stu-id="f1217-203">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="f1217-204">`Vary` 标头参数必须有效且不等于 `*`。</span><span class="sxs-lookup"><span data-stu-id="f1217-204">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="f1217-205">`Content-Length` 标头值（如果已设置）必须与响应正文的大小匹配。</span><span class="sxs-lookup"><span data-stu-id="f1217-205">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="f1217-206">不使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>。</span><span class="sxs-lookup"><span data-stu-id="f1217-206">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="f1217-207">`Expires` 标头和 `max-age` 和 `s-maxage` 缓存指令指定的响应不能过时。</span><span class="sxs-lookup"><span data-stu-id="f1217-207">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="f1217-208">响应缓冲必须成功。</span><span class="sxs-lookup"><span data-stu-id="f1217-208">Response buffering must be successful.</span></span> <span data-ttu-id="f1217-209">响应的大小必须小于配置的或默认 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>。</span><span class="sxs-lookup"><span data-stu-id="f1217-209">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="f1217-210">响应的正文大小必须小于配置的或默认的 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>。</span><span class="sxs-lookup"><span data-stu-id="f1217-210">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="f1217-211">必须根据[RFC 7234](https://tools.ietf.org/html/rfc7234)规范来缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-211">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="f1217-212">例如，"请求" 或 "响应" 标头字段中不得存在 "`no-store`" 指令。</span><span class="sxs-lookup"><span data-stu-id="f1217-212">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="f1217-213">有关详细信息，请参阅*第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234)的缓存中。</span><span class="sxs-lookup"><span data-stu-id="f1217-213">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="f1217-214">用于生成安全令牌以防止跨站点请求伪造（CSRF）攻击的防伪系统将 `Cache-Control` 和 `Pragma` 标头设置为 `no-cache`，以便不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-214">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="f1217-215">有关如何为 HTML 窗体元素禁用防伪标记的信息，请参阅 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>。</span><span class="sxs-lookup"><span data-stu-id="f1217-215">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1217-216">其他资源</span><span class="sxs-lookup"><span data-stu-id="f1217-216">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f1217-217">此文章介绍了如何在 ASP.NET Core 应用程序中配置缓存响应的中间件。</span><span class="sxs-lookup"><span data-stu-id="f1217-217">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="f1217-218">中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-218">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="f1217-219">有关 HTTP 缓存和[`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性的介绍，请参阅[响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="f1217-219">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="f1217-220">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f1217-220">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="f1217-221">配置</span><span class="sxs-lookup"><span data-stu-id="f1217-221">Configuration</span></span>

<span data-ttu-id="f1217-222">使用[AspNetCore 元包](xref:fundamentals/metapackage-app)或添加对[AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包的包引用。</span><span class="sxs-lookup"><span data-stu-id="f1217-222">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="f1217-223">在 `Startup.ConfigureServices`中，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="f1217-223">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="f1217-224">将应用程序配置为将中间件与 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 扩展方法一起使用，该方法将中间件添加到 `Startup.Configure`中的请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="f1217-224">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="f1217-225">示例应用添加标头以在后续请求时控制缓存：</span><span class="sxs-lookup"><span data-stu-id="f1217-225">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="f1217-226">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)&ndash; 将可缓存的响应缓存多达10秒。</span><span class="sxs-lookup"><span data-stu-id="f1217-226">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="f1217-227">[不同](https://tools.ietf.org/html/rfc7231#section-7.1.4)&ndash; 将中间件配置为仅当后续请求的 Accept 编码标头与原始请求的[接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)标头匹配时才提供缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-227">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="f1217-228">响应缓存中间件仅缓存服务器响应，导致了200（正常）状态代码。</span><span class="sxs-lookup"><span data-stu-id="f1217-228">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="f1217-229">中间件将忽略任何其他响应，包括[错误页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="f1217-229">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="f1217-230">包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-230">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="f1217-231">有关中间件如何确定响应是否可缓存的详细信息，请参阅[缓存的条件](#conditions-for-caching)。</span><span class="sxs-lookup"><span data-stu-id="f1217-231">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="f1217-232">Options</span><span class="sxs-lookup"><span data-stu-id="f1217-232">Options</span></span>

<span data-ttu-id="f1217-233">响应缓存选项如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="f1217-233">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="f1217-234">选项</span><span class="sxs-lookup"><span data-stu-id="f1217-234">Option</span></span> | <span data-ttu-id="f1217-235">说明</span><span class="sxs-lookup"><span data-stu-id="f1217-235">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="f1217-236">响应正文的最大可缓存大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f1217-236">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="f1217-237">默认值为 `64 * 1024 * 1024` （64 MB）。</span><span class="sxs-lookup"><span data-stu-id="f1217-237">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="f1217-238">响应缓存中间件的大小限制（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="f1217-238">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="f1217-239">默认值为 `100 * 1024 * 1024` （100 MB）。</span><span class="sxs-lookup"><span data-stu-id="f1217-239">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="f1217-240">确定是否将响应缓存在区分大小写的路径上。</span><span class="sxs-lookup"><span data-stu-id="f1217-240">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="f1217-241">默认值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="f1217-241">The default value is `false`.</span></span> |

<span data-ttu-id="f1217-242">下面的示例将中间件配置为：</span><span class="sxs-lookup"><span data-stu-id="f1217-242">The following example configures the middleware to:</span></span>

* <span data-ttu-id="f1217-243">大小小于或等于1024字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-243">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="f1217-244">将响应存储为区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="f1217-244">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="f1217-245">例如，`/page1` 和 `/Page1` 单独存储。</span><span class="sxs-lookup"><span data-stu-id="f1217-245">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="f1217-246">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="f1217-246">VaryByQueryKeys</span></span>

<span data-ttu-id="f1217-247">使用 MVC/web API 控制器或 Razor Pages 页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性指定为响应缓存设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="f1217-247">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="f1217-248">严格需要中间件的 `[ResponseCache]` 属性的唯一参数 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>，这与实际 HTTP 标头不对应。</span><span class="sxs-lookup"><span data-stu-id="f1217-248">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="f1217-249">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="f1217-249">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="f1217-250">如果不使用 `[ResponseCache]` 属性，响应缓存可能会与 `VaryByQueryKeys`不同。</span><span class="sxs-lookup"><span data-stu-id="f1217-250">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="f1217-251">直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>：</span><span class="sxs-lookup"><span data-stu-id="f1217-251">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="f1217-252">使用 `VaryByQueryKeys` 中 `*` 的单个值将按所有请求查询参数改变缓存。</span><span class="sxs-lookup"><span data-stu-id="f1217-252">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="f1217-253">响应缓存中间件使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="f1217-253">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="f1217-254">下表提供了有关影响响应缓存的 HTTP 标头的信息。</span><span class="sxs-lookup"><span data-stu-id="f1217-254">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="f1217-255">标头</span><span class="sxs-lookup"><span data-stu-id="f1217-255">Header</span></span> | <span data-ttu-id="f1217-256">详细信息</span><span class="sxs-lookup"><span data-stu-id="f1217-256">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="f1217-257">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-257">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="f1217-258">中间件仅考虑用 `public` 缓存指令标记的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-258">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="f1217-259">具有以下参数的控件缓存：</span><span class="sxs-lookup"><span data-stu-id="f1217-259">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="f1217-260">max-age</span><span class="sxs-lookup"><span data-stu-id="f1217-260">max-age</span></span></li><li><span data-ttu-id="f1217-261">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="f1217-261">max-stale&#8224;</span></span></li><li><span data-ttu-id="f1217-262">最小-新</span><span class="sxs-lookup"><span data-stu-id="f1217-262">min-fresh</span></span></li><li><span data-ttu-id="f1217-263">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="f1217-263">must-revalidate</span></span></li><li><span data-ttu-id="f1217-264">no-cache</span><span class="sxs-lookup"><span data-stu-id="f1217-264">no-cache</span></span></li><li><span data-ttu-id="f1217-265">无-商店</span><span class="sxs-lookup"><span data-stu-id="f1217-265">no-store</span></span></li><li><span data-ttu-id="f1217-266">仅限-缓存</span><span class="sxs-lookup"><span data-stu-id="f1217-266">only-if-cached</span></span></li><li><span data-ttu-id="f1217-267">专用</span><span class="sxs-lookup"><span data-stu-id="f1217-267">private</span></span></li><li><span data-ttu-id="f1217-268">public</span><span class="sxs-lookup"><span data-stu-id="f1217-268">public</span></span></li><li><span data-ttu-id="f1217-269">s-maxage</span><span class="sxs-lookup"><span data-stu-id="f1217-269">s-maxage</span></span></li><li><span data-ttu-id="f1217-270">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="f1217-270">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="f1217-271">&#8224;如果没有指定 `max-stale`的限制，则中间件不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="f1217-271">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="f1217-272">&#8225;`proxy-revalidate` 与 `must-revalidate`的效果相同。</span><span class="sxs-lookup"><span data-stu-id="f1217-272">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="f1217-273">有关详细信息，请参阅[RFC 7231：请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="f1217-273">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="f1217-274">请求中的 `Pragma: no-cache` 标头将产生与 `Cache-Control: no-cache`相同的效果。</span><span class="sxs-lookup"><span data-stu-id="f1217-274">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="f1217-275">如果存在此标头，则由 `Cache-Control` 标头中的相关指令重写。</span><span class="sxs-lookup"><span data-stu-id="f1217-275">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="f1217-276">考虑向后兼容 HTTP/1.0。</span><span class="sxs-lookup"><span data-stu-id="f1217-276">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="f1217-277">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-277">The response isn't cached if the header exists.</span></span> <span data-ttu-id="f1217-278">请求处理管道中设置一个或多个 cookie 的任何中间件会阻止响应缓存中间件缓存响应（例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata)）。</span><span class="sxs-lookup"><span data-stu-id="f1217-278">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="f1217-279">`Vary` 标头用于根据另一个标头改变缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-279">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="f1217-280">例如，通过编码来缓存响应，包括 `Vary: Accept-Encoding` 标头，该标头将缓存标头为 `Accept-Encoding: gzip` 和 `Accept-Encoding: text/plain` 的请求的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-280">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="f1217-281">永远不会存储标头值为 `*` 的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-281">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="f1217-282">除非被其他 `Cache-Control` 标头重写，否则不会存储或检索此标头过时的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-282">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="f1217-283">如果值不为 `*`，响应的 `ETag` 与提供的任何值都不匹配，则将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-283">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="f1217-284">否则，将提供304（未修改）响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-284">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="f1217-285">如果 `If-None-Match` 标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-285">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="f1217-286">否则，将提供*304-未修改*响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-286">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="f1217-287">从缓存提供时，如果未在原始响应中提供，则中间件会设置 `Date` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-287">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="f1217-288">从缓存提供时，如果未在原始响应中提供，则中间件会设置 `Content-Length` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-288">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="f1217-289">忽略原始响应中发送的 `Age` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-289">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="f1217-290">中间件在为缓存的响应提供服务时计算一个新值。</span><span class="sxs-lookup"><span data-stu-id="f1217-290">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="f1217-291">缓存遵从请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="f1217-291">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="f1217-292">中间件遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。</span><span class="sxs-lookup"><span data-stu-id="f1217-292">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="f1217-293">规则要求使用缓存来服从客户端发送的有效 `Cache-Control` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-293">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="f1217-294">在规范下，客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-294">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="f1217-295">目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="f1217-295">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="f1217-296">为了更好地控制缓存行为，将介绍其他缓存功能的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="f1217-296">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="f1217-297">请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="f1217-297">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="f1217-298">故障排除</span><span class="sxs-lookup"><span data-stu-id="f1217-298">Troubleshooting</span></span>

<span data-ttu-id="f1217-299">如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。</span><span class="sxs-lookup"><span data-stu-id="f1217-299">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="f1217-300">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-300">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="f1217-301">启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="f1217-301">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="f1217-302">在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-302">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="f1217-303">例如，浏览器可以将 `Cache-Control` 标题设置为刷新页面时 `no-cache` 或 `max-age=0`。</span><span class="sxs-lookup"><span data-stu-id="f1217-303">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="f1217-304">以下工具可以显式设置请求标头，并优先于测试缓存：</span><span class="sxs-lookup"><span data-stu-id="f1217-304">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="f1217-305">Fiddler</span><span class="sxs-lookup"><span data-stu-id="f1217-305">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="f1217-306">Postman</span><span class="sxs-lookup"><span data-stu-id="f1217-306">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="f1217-307">缓存条件</span><span class="sxs-lookup"><span data-stu-id="f1217-307">Conditions for caching</span></span>

* <span data-ttu-id="f1217-308">请求必须导致服务器响应，状态代码为200（正常）。</span><span class="sxs-lookup"><span data-stu-id="f1217-308">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="f1217-309">请求方法必须为 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="f1217-309">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="f1217-310">在 `Startup.Configure`中，响应缓存中间件必须置于需要缓存的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="f1217-310">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="f1217-311">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="f1217-311">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="f1217-312">`Authorization` 标头不得存在。</span><span class="sxs-lookup"><span data-stu-id="f1217-312">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="f1217-313">`Cache-Control` 标头参数必须是有效的，并且响应必须标记为 "`public`" 且未标记为 "`private`"。</span><span class="sxs-lookup"><span data-stu-id="f1217-313">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="f1217-314">如果 `Cache-Control` 标头不存在，则 `Pragma: no-cache` 标头不得存在，因为 `Cache-Control` 标头在存在时将覆盖 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1217-314">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="f1217-315">`Set-Cookie` 标头不得存在。</span><span class="sxs-lookup"><span data-stu-id="f1217-315">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="f1217-316">`Vary` 标头参数必须有效且不等于 `*`。</span><span class="sxs-lookup"><span data-stu-id="f1217-316">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="f1217-317">`Content-Length` 标头值（如果已设置）必须与响应正文的大小匹配。</span><span class="sxs-lookup"><span data-stu-id="f1217-317">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="f1217-318">不使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>。</span><span class="sxs-lookup"><span data-stu-id="f1217-318">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="f1217-319">`Expires` 标头和 `max-age` 和 `s-maxage` 缓存指令指定的响应不能过时。</span><span class="sxs-lookup"><span data-stu-id="f1217-319">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="f1217-320">响应缓冲必须成功。</span><span class="sxs-lookup"><span data-stu-id="f1217-320">Response buffering must be successful.</span></span> <span data-ttu-id="f1217-321">响应的大小必须小于配置的或默认 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>。</span><span class="sxs-lookup"><span data-stu-id="f1217-321">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="f1217-322">响应的正文大小必须小于配置的或默认的 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>。</span><span class="sxs-lookup"><span data-stu-id="f1217-322">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="f1217-323">必须根据[RFC 7234](https://tools.ietf.org/html/rfc7234)规范来缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-323">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="f1217-324">例如，"请求" 或 "响应" 标头字段中不得存在 "`no-store`" 指令。</span><span class="sxs-lookup"><span data-stu-id="f1217-324">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="f1217-325">有关详细信息，请参阅*第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234)的缓存中。</span><span class="sxs-lookup"><span data-stu-id="f1217-325">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="f1217-326">用于生成安全令牌以防止跨站点请求伪造（CSRF）攻击的防伪系统将 `Cache-Control` 和 `Pragma` 标头设置为 `no-cache`，以便不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="f1217-326">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="f1217-327">有关如何为 HTML 窗体元素禁用防伪标记的信息，请参阅 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>。</span><span class="sxs-lookup"><span data-stu-id="f1217-327">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1217-328">其他资源</span><span class="sxs-lookup"><span data-stu-id="f1217-328">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
