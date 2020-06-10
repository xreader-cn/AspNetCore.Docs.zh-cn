---
title: ASP.NET Core 中的响应缓存中间件
author: rick-anderson
description: 了解如何在 ASP.NET Core 中配置和使用响应缓存中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/middleware
ms.openlocfilehash: 2ee75b1af9ffc23ff9ae1763059364de3ec8f426
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106502"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="1f8bc-103">ASP.NET Core 中的响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="1f8bc-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="1f8bc-104">作者：[John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="1f8bc-104">By [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1f8bc-105">本文介绍如何在 ASP.NET Core 应用程序中配置响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-105">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="1f8bc-106">中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-106">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="1f8bc-107">有关 HTTP 缓存和属性的介绍 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，请参阅[响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-107">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="1f8bc-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1f8bc-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="1f8bc-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="1f8bc-109">Configuration</span></span>

<span data-ttu-id="1f8bc-110">响应缓存中间件可通过共享框架隐式地用于 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="1f8bc-111">在中 `Startup.ConfigureServices` ，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="1f8bc-112">将应用程序配置为将中间件与 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 扩展方法一起使用，该扩展方法将中间件添加到中的请求处理管道 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

<span data-ttu-id="1f8bc-113">示例应用添加标头以在后续请求时控制缓存：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="1f8bc-114">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)：将可缓存的响应缓存多达10秒。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="1f8bc-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：仅当后续请求的接受编码标头与原始请求的[接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)标头匹配时，才将中间件配置为提供缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="1f8bc-116">前面的标头不会写入响应，并在控制器、操作或页上被重写 Razor ：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-116">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="1f8bc-117">具有[[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-117">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="1f8bc-118">即使未设置属性，这也适用。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-118">This applies even if a property isn't set.</span></span> <span data-ttu-id="1f8bc-119">例如，省略[VaryByHeader](/aspnet/core/performance/caching/response#vary)属性将导致从响应中删除相应的标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-119">For example, omitting the [VaryByHeader](/aspnet/core/performance/caching/response#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="1f8bc-120">响应缓存中间件仅缓存服务器响应，导致了200（正常）状态代码。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-120">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="1f8bc-121">中间件将忽略任何其他响应，包括[错误页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-121">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="1f8bc-122">包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-122">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="1f8bc-123">有关中间件如何确定响应是否可缓存的详细信息，请参阅[缓存的条件](#conditions-for-caching)。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-123">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="1f8bc-124">选项</span><span class="sxs-lookup"><span data-stu-id="1f8bc-124">Options</span></span>

<span data-ttu-id="1f8bc-125">响应缓存选项如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-125">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="1f8bc-126">选项</span><span class="sxs-lookup"><span data-stu-id="1f8bc-126">Option</span></span> | <span data-ttu-id="1f8bc-127">描述</span><span class="sxs-lookup"><span data-stu-id="1f8bc-127">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="1f8bc-128">响应正文的最大可缓存大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-128">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="1f8bc-129">默认值为 `64 * 1024 * 1024` （64 MB）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-129">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="1f8bc-130">响应缓存中间件的大小限制（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-130">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="1f8bc-131">默认值为 `100 * 1024 * 1024` （100 MB）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-131">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="1f8bc-132">确定是否将响应缓存在区分大小写的路径上。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-132">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="1f8bc-133">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-133">The default value is `false`.</span></span> |

<span data-ttu-id="1f8bc-134">下面的示例将中间件配置为：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-134">The following example configures the middleware to:</span></span>

* <span data-ttu-id="1f8bc-135">大小小于或等于1024字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-135">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="1f8bc-136">将响应存储为区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-136">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="1f8bc-137">例如， `/page1` 和 `/Page1` 分别存储。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-137">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="1f8bc-138">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="1f8bc-138">VaryByQueryKeys</span></span>

<span data-ttu-id="1f8bc-139">使用 MVC/web API 控制器或 Razor 页面页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性指定为响应缓存设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-139">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="1f8bc-140">`[ResponseCache]`严格要求中间件的属性的唯一参数是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，它不对应于实际 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-140">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="1f8bc-141">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-141">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="1f8bc-142">如果不使用 `[ResponseCache]` 属性，响应缓存可能会随而变化 `VaryByQueryKeys` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-142">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="1f8bc-143"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-143">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="1f8bc-144">使用等于中的单个值 `*` 会 `VaryByQueryKeys` 根据所有请求查询参数改变缓存。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-144">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="1f8bc-145">响应缓存中间件使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="1f8bc-145">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="1f8bc-146">下表提供了有关影响响应缓存的 HTTP 标头的信息。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-146">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="1f8bc-147">Header</span><span class="sxs-lookup"><span data-stu-id="1f8bc-147">Header</span></span> | <span data-ttu-id="1f8bc-148">详细信息</span><span class="sxs-lookup"><span data-stu-id="1f8bc-148">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="1f8bc-149">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-149">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="1f8bc-150">中间件仅考虑用缓存指令标记的缓存响应 `public` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-150">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="1f8bc-151">具有以下参数的控件缓存：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-151">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="1f8bc-152">最大期限</span><span class="sxs-lookup"><span data-stu-id="1f8bc-152">max-age</span></span></li><li><span data-ttu-id="1f8bc-153">最大过期&#8224;</span><span class="sxs-lookup"><span data-stu-id="1f8bc-153">max-stale&#8224;</span></span></li><li><span data-ttu-id="1f8bc-154">最小-新</span><span class="sxs-lookup"><span data-stu-id="1f8bc-154">min-fresh</span></span></li><li><span data-ttu-id="1f8bc-155">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="1f8bc-155">must-revalidate</span></span></li><li><span data-ttu-id="1f8bc-156">no-cache</span><span class="sxs-lookup"><span data-stu-id="1f8bc-156">no-cache</span></span></li><li><span data-ttu-id="1f8bc-157">无-商店</span><span class="sxs-lookup"><span data-stu-id="1f8bc-157">no-store</span></span></li><li><span data-ttu-id="1f8bc-158">仅限-缓存</span><span class="sxs-lookup"><span data-stu-id="1f8bc-158">only-if-cached</span></span></li><li><span data-ttu-id="1f8bc-159">private</span><span class="sxs-lookup"><span data-stu-id="1f8bc-159">private</span></span></li><li><span data-ttu-id="1f8bc-160">public</span><span class="sxs-lookup"><span data-stu-id="1f8bc-160">public</span></span></li><li><span data-ttu-id="1f8bc-161">s-maxage</span><span class="sxs-lookup"><span data-stu-id="1f8bc-161">s-maxage</span></span></li><li><span data-ttu-id="1f8bc-162">代理重新验证&#8225;</span><span class="sxs-lookup"><span data-stu-id="1f8bc-162">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="1f8bc-163">&#8224;如果未指定任何限制 `max-stale` ，则中间件不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-163">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="1f8bc-164">&#8225;`proxy-revalidate` 的效果与相同 `must-revalidate` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-164">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="1f8bc-165">有关详细信息，请参阅[RFC 7231：请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-165">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="1f8bc-166">`Pragma: no-cache`请求中的标头将产生与相同的效果 `Cache-Control: no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-166">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="1f8bc-167">标头中的相关指令 `Cache-Control` （如果存在）将重写此标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-167">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="1f8bc-168">考虑向后兼容 HTTP/1.0。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-168">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="1f8bc-169">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-169">The response isn't cached if the header exists.</span></span> <span data-ttu-id="1f8bc-170">请求处理管道中设置一个或多个 cookie 的任何中间件会阻止响应缓存中间件缓存响应（例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata)）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-170">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="1f8bc-171">`Vary`标头用于根据另一个标头改变缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-171">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="1f8bc-172">例如，通过包含标头来缓存响应， `Vary: Accept-Encoding` 此标头将使用标头和单独的请求来缓存响应 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-172">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="1f8bc-173">永远不会存储标头值 `*` 为的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-173">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="1f8bc-174">除非由其他标头重写，否则不会存储或检索此标头过时的响应 `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-174">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="1f8bc-175">如果值不为 `*` ，并且响应的与提供的任何值都不匹配，则将从缓存中提供完整响应 `ETag` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-175">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="1f8bc-176">否则，将提供304（未修改）响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-176">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="1f8bc-177">如果 `If-None-Match` 标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-177">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="1f8bc-178">否则，将提供*304-未修改*响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-178">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="1f8bc-179">从缓存提供时， `Date` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-179">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="1f8bc-180">从缓存提供时， `Content-Length` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-180">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="1f8bc-181">`Age`忽略原始响应中发送的标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-181">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="1f8bc-182">中间件在为缓存的响应提供服务时计算一个新值。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-182">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="1f8bc-183">缓存遵从请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="1f8bc-183">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="1f8bc-184">中间件遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-184">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="1f8bc-185">规则要求使用缓存来服从 `Cache-Control` 客户端发送的有效标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-185">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="1f8bc-186">在规范下，客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-186">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="1f8bc-187">目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-187">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="1f8bc-188">为了更好地控制缓存行为，浏览 ASP.NET Core 的其他缓存功能。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-188">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="1f8bc-189">请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-189">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="1f8bc-190">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1f8bc-190">Troubleshooting</span></span>

<span data-ttu-id="1f8bc-191">如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-191">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="1f8bc-192">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-192">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="1f8bc-193">启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-193">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="1f8bc-194">在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-194">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="1f8bc-195">例如，浏览器可以在 `Cache-Control` `no-cache` 刷新页面时将标题设置为或 `max-age=0` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-195">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="1f8bc-196">以下工具可以显式设置请求标头，并优先于测试缓存：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-196">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="1f8bc-197">Fiddler</span><span class="sxs-lookup"><span data-stu-id="1f8bc-197">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="1f8bc-198">Postman</span><span class="sxs-lookup"><span data-stu-id="1f8bc-198">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="1f8bc-199">缓存条件</span><span class="sxs-lookup"><span data-stu-id="1f8bc-199">Conditions for caching</span></span>

* <span data-ttu-id="1f8bc-200">请求必须导致服务器响应，状态代码为200（正常）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-200">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="1f8bc-201">请求方法必须为 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-201">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="1f8bc-202">在中 `Startup.Configure` ，必须将响应缓存中间件置于需要缓存的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-202">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="1f8bc-203">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-203">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="1f8bc-204">`Authorization`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-204">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="1f8bc-205">`Cache-Control`标头参数必须是有效的，并且响应必须标记 `public` 且未标记 `private` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-205">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="1f8bc-206">`Pragma: no-cache`如果标头不存在，则标头不得出现 `Cache-Control` ，因为标头会 `Cache-Control` `Pragma` 在存在时覆盖标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-206">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="1f8bc-207">`Set-Cookie`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-207">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="1f8bc-208">`Vary`标头参数必须有效并且不等于 `*` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-208">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="1f8bc-209">`Content-Length`标头值（如果已设置）必须与响应正文的大小匹配。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-209">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="1f8bc-210"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>不使用。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-210">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="1f8bc-211">`Expires`标头和 `max-age` 和缓存指令指定的响应不能过时 `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-211">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="1f8bc-212">响应缓冲必须成功。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-212">Response buffering must be successful.</span></span> <span data-ttu-id="1f8bc-213">响应的大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-213">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="1f8bc-214">响应的正文大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-214">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="1f8bc-215">必须根据[RFC 7234](https://tools.ietf.org/html/rfc7234)规范来缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-215">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="1f8bc-216">例如， `no-store` 指令在请求或响应标头字段中不得存在。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-216">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="1f8bc-217">有关详细信息，请参阅*第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234)的缓存中。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-217">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="1f8bc-218">用于生成安全令牌以防止跨站点请求伪造（CSRF）攻击的防伪系统将 `Cache-Control` 和 `Pragma` 标头设置为， `no-cache` 以便不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-218">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="1f8bc-219">有关如何为 HTML 窗体元素禁用防伪标记的信息，请参阅 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-219">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f8bc-220">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f8bc-220">Additional resources</span></span>

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

<span data-ttu-id="1f8bc-221">本文介绍如何在 ASP.NET Core 应用程序中配置响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-221">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="1f8bc-222">中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-222">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="1f8bc-223">有关 HTTP 缓存和属性的介绍 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，请参阅[响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-223">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="1f8bc-224">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1f8bc-224">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="1f8bc-225">Configuration</span><span class="sxs-lookup"><span data-stu-id="1f8bc-225">Configuration</span></span>

<span data-ttu-id="1f8bc-226">使用[AspNetCore 元包](xref:fundamentals/metapackage-app)或添加对[AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包的包引用。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-226">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="1f8bc-227">在中 `Startup.ConfigureServices` ，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-227">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="1f8bc-228">将应用程序配置为将中间件与 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 扩展方法一起使用，该扩展方法将中间件添加到中的请求处理管道 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-228">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="1f8bc-229">示例应用添加标头以在后续请求时控制缓存：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-229">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="1f8bc-230">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)：将可缓存的响应缓存多达10秒。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-230">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="1f8bc-231">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：仅当后续请求的接受编码标头与原始请求的[接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)标头匹配时，才将中间件配置为提供缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-231">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="1f8bc-232">前面的标头不会写入响应，并在控制器、操作或页上被重写 Razor ：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-232">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="1f8bc-233">具有[[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-233">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="1f8bc-234">即使未设置属性，这也适用。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-234">This applies even if a property isn't set.</span></span> <span data-ttu-id="1f8bc-235">例如，省略[VaryByHeader](/aspnet/core/performance/caching/response#vary)属性将导致从响应中删除相应的标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-235">For example, omitting the [VaryByHeader](/aspnet/core/performance/caching/response#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="1f8bc-236">响应缓存中间件仅缓存服务器响应，导致了200（正常）状态代码。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-236">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="1f8bc-237">中间件将忽略任何其他响应，包括[错误页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-237">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="1f8bc-238">包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-238">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="1f8bc-239">有关中间件如何确定响应是否可缓存的详细信息，请参阅[缓存的条件](#conditions-for-caching)。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-239">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="1f8bc-240">选项</span><span class="sxs-lookup"><span data-stu-id="1f8bc-240">Options</span></span>

<span data-ttu-id="1f8bc-241">响应缓存选项如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-241">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="1f8bc-242">选项</span><span class="sxs-lookup"><span data-stu-id="1f8bc-242">Option</span></span> | <span data-ttu-id="1f8bc-243">描述</span><span class="sxs-lookup"><span data-stu-id="1f8bc-243">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="1f8bc-244">响应正文的最大可缓存大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-244">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="1f8bc-245">默认值为 `64 * 1024 * 1024` （64 MB）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-245">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="1f8bc-246">响应缓存中间件的大小限制（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-246">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="1f8bc-247">默认值为 `100 * 1024 * 1024` （100 MB）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-247">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="1f8bc-248">确定是否将响应缓存在区分大小写的路径上。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-248">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="1f8bc-249">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-249">The default value is `false`.</span></span> |

<span data-ttu-id="1f8bc-250">下面的示例将中间件配置为：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-250">The following example configures the middleware to:</span></span>

* <span data-ttu-id="1f8bc-251">大小小于或等于1024字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-251">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="1f8bc-252">将响应存储为区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-252">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="1f8bc-253">例如， `/page1` 和 `/Page1` 分别存储。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-253">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="1f8bc-254">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="1f8bc-254">VaryByQueryKeys</span></span>

<span data-ttu-id="1f8bc-255">使用 MVC/web API 控制器或 Razor 页面页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性指定为响应缓存设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-255">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="1f8bc-256">`[ResponseCache]`严格要求中间件的属性的唯一参数是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，它不对应于实际 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-256">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="1f8bc-257">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-257">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="1f8bc-258">如果不使用 `[ResponseCache]` 属性，响应缓存可能会随而变化 `VaryByQueryKeys` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-258">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="1f8bc-259"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-259">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="1f8bc-260">使用等于中的单个值 `*` 会 `VaryByQueryKeys` 根据所有请求查询参数改变缓存。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-260">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="1f8bc-261">响应缓存中间件使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="1f8bc-261">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="1f8bc-262">下表提供了有关影响响应缓存的 HTTP 标头的信息。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-262">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="1f8bc-263">Header</span><span class="sxs-lookup"><span data-stu-id="1f8bc-263">Header</span></span> | <span data-ttu-id="1f8bc-264">详细信息</span><span class="sxs-lookup"><span data-stu-id="1f8bc-264">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="1f8bc-265">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-265">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="1f8bc-266">中间件仅考虑用缓存指令标记的缓存响应 `public` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-266">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="1f8bc-267">具有以下参数的控件缓存：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-267">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="1f8bc-268">最大期限</span><span class="sxs-lookup"><span data-stu-id="1f8bc-268">max-age</span></span></li><li><span data-ttu-id="1f8bc-269">最大过期&#8224;</span><span class="sxs-lookup"><span data-stu-id="1f8bc-269">max-stale&#8224;</span></span></li><li><span data-ttu-id="1f8bc-270">最小-新</span><span class="sxs-lookup"><span data-stu-id="1f8bc-270">min-fresh</span></span></li><li><span data-ttu-id="1f8bc-271">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="1f8bc-271">must-revalidate</span></span></li><li><span data-ttu-id="1f8bc-272">no-cache</span><span class="sxs-lookup"><span data-stu-id="1f8bc-272">no-cache</span></span></li><li><span data-ttu-id="1f8bc-273">无-商店</span><span class="sxs-lookup"><span data-stu-id="1f8bc-273">no-store</span></span></li><li><span data-ttu-id="1f8bc-274">仅限-缓存</span><span class="sxs-lookup"><span data-stu-id="1f8bc-274">only-if-cached</span></span></li><li><span data-ttu-id="1f8bc-275">private</span><span class="sxs-lookup"><span data-stu-id="1f8bc-275">private</span></span></li><li><span data-ttu-id="1f8bc-276">public</span><span class="sxs-lookup"><span data-stu-id="1f8bc-276">public</span></span></li><li><span data-ttu-id="1f8bc-277">s-maxage</span><span class="sxs-lookup"><span data-stu-id="1f8bc-277">s-maxage</span></span></li><li><span data-ttu-id="1f8bc-278">代理重新验证&#8225;</span><span class="sxs-lookup"><span data-stu-id="1f8bc-278">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="1f8bc-279">&#8224;如果未指定任何限制 `max-stale` ，则中间件不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-279">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="1f8bc-280">&#8225;`proxy-revalidate` 的效果与相同 `must-revalidate` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-280">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="1f8bc-281">有关详细信息，请参阅[RFC 7231：请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-281">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="1f8bc-282">`Pragma: no-cache`请求中的标头将产生与相同的效果 `Cache-Control: no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-282">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="1f8bc-283">标头中的相关指令 `Cache-Control` （如果存在）将重写此标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-283">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="1f8bc-284">考虑向后兼容 HTTP/1.0。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-284">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="1f8bc-285">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-285">The response isn't cached if the header exists.</span></span> <span data-ttu-id="1f8bc-286">请求处理管道中设置一个或多个 cookie 的任何中间件会阻止响应缓存中间件缓存响应（例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata)）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-286">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="1f8bc-287">`Vary`标头用于根据另一个标头改变缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-287">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="1f8bc-288">例如，通过包含标头来缓存响应， `Vary: Accept-Encoding` 此标头将使用标头和单独的请求来缓存响应 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-288">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="1f8bc-289">永远不会存储标头值 `*` 为的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-289">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="1f8bc-290">除非由其他标头重写，否则不会存储或检索此标头过时的响应 `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-290">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="1f8bc-291">如果值不为 `*` ，并且响应的与提供的任何值都不匹配，则将从缓存中提供完整响应 `ETag` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-291">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="1f8bc-292">否则，将提供304（未修改）响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-292">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="1f8bc-293">如果 `If-None-Match` 标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-293">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="1f8bc-294">否则，将提供*304-未修改*响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-294">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="1f8bc-295">从缓存提供时， `Date` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-295">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="1f8bc-296">从缓存提供时， `Content-Length` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-296">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="1f8bc-297">`Age`忽略原始响应中发送的标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-297">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="1f8bc-298">中间件在为缓存的响应提供服务时计算一个新值。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-298">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="1f8bc-299">缓存遵从请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="1f8bc-299">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="1f8bc-300">中间件遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-300">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="1f8bc-301">规则要求使用缓存来服从 `Cache-Control` 客户端发送的有效标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-301">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="1f8bc-302">在规范下，客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-302">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="1f8bc-303">目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-303">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="1f8bc-304">为了更好地控制缓存行为，浏览 ASP.NET Core 的其他缓存功能。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-304">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="1f8bc-305">请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-305">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="1f8bc-306">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1f8bc-306">Troubleshooting</span></span>

<span data-ttu-id="1f8bc-307">如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-307">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="1f8bc-308">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-308">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="1f8bc-309">启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-309">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="1f8bc-310">在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-310">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="1f8bc-311">例如，浏览器可以在 `Cache-Control` `no-cache` 刷新页面时将标题设置为或 `max-age=0` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-311">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="1f8bc-312">以下工具可以显式设置请求标头，并优先于测试缓存：</span><span class="sxs-lookup"><span data-stu-id="1f8bc-312">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="1f8bc-313">Fiddler</span><span class="sxs-lookup"><span data-stu-id="1f8bc-313">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="1f8bc-314">Postman</span><span class="sxs-lookup"><span data-stu-id="1f8bc-314">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="1f8bc-315">缓存条件</span><span class="sxs-lookup"><span data-stu-id="1f8bc-315">Conditions for caching</span></span>

* <span data-ttu-id="1f8bc-316">请求必须导致服务器响应，状态代码为200（正常）。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-316">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="1f8bc-317">请求方法必须为 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-317">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="1f8bc-318">在中 `Startup.Configure` ，必须将响应缓存中间件置于需要缓存的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-318">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="1f8bc-319">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-319">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="1f8bc-320">`Authorization`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-320">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="1f8bc-321">`Cache-Control`标头参数必须是有效的，并且响应必须标记 `public` 且未标记 `private` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-321">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="1f8bc-322">`Pragma: no-cache`如果标头不存在，则标头不得出现 `Cache-Control` ，因为标头会 `Cache-Control` `Pragma` 在存在时覆盖标头。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-322">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="1f8bc-323">`Set-Cookie`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-323">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="1f8bc-324">`Vary`标头参数必须有效并且不等于 `*` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-324">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="1f8bc-325">`Content-Length`标头值（如果已设置）必须与响应正文的大小匹配。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-325">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="1f8bc-326"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>不使用。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-326">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="1f8bc-327">`Expires`标头和 `max-age` 和缓存指令指定的响应不能过时 `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-327">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="1f8bc-328">响应缓冲必须成功。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-328">Response buffering must be successful.</span></span> <span data-ttu-id="1f8bc-329">响应的大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-329">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="1f8bc-330">响应的正文大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-330">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="1f8bc-331">必须根据[RFC 7234](https://tools.ietf.org/html/rfc7234)规范来缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-331">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="1f8bc-332">例如， `no-store` 指令在请求或响应标头字段中不得存在。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-332">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="1f8bc-333">有关详细信息，请参阅*第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234)的缓存中。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-333">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="1f8bc-334">用于生成安全令牌以防止跨站点请求伪造（CSRF）攻击的防伪系统将 `Cache-Control` 和 `Pragma` 标头设置为， `no-cache` 以便不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-334">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="1f8bc-335">有关如何为 HTML 窗体元素禁用防伪标记的信息，请参阅 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="1f8bc-335">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f8bc-336">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f8bc-336">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
