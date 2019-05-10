---
title: 响应缓存在 ASP.NET Core 中的中间件
author: guardrex
description: 了解如何配置和 ASP.NET Core 中使用缓存响应的中间件。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2019
uid: performance/caching/middleware
ms.openlocfilehash: ddbd547ec0fdd09da1f3e3ce30f8fa35a34870c2
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897364"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="ba4a7-103">响应缓存在 ASP.NET Core 中的中间件</span><span class="sxs-lookup"><span data-stu-id="ba4a7-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="ba4a7-104">通过[Luke Latham](https://github.com/guardrex)和[John 卢奥语](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="ba4a7-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="ba4a7-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ba4a7-106">此文章介绍了如何在 ASP.NET Core 应用程序中配置缓存响应的中间件。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="ba4a7-107">中间件确定何时可缓存的响应、 存储响应和从缓存提供服务响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="ba4a7-108">有关 HTTP 缓存的介绍和`ResponseCache`属性，请参阅[响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-108">For an introduction to HTTP caching and the `ResponseCache` attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="package"></a><span data-ttu-id="ba4a7-109">package</span><span class="sxs-lookup"><span data-stu-id="ba4a7-109">Package</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ba4a7-110">引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或添加到的包引用[Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-110">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="ba4a7-111">引用[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)或添加到的包引用[Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-111">Reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="ba4a7-112">添加到包引用[Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-112">Add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="ba4a7-113">配置</span><span class="sxs-lookup"><span data-stu-id="ba4a7-113">Configuration</span></span>

<span data-ttu-id="ba4a7-114">在`Startup.ConfigureServices`，将中间件添加到服务集合。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-114">In `Startup.ConfigureServices`, add the middleware to the service collection.</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="ba4a7-115">将应用配置为使用与中间件`UseResponseCaching`扩展方法，它将中间件添加到请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-115">Configure the app to use the middleware with the `UseResponseCaching` extension method, which adds the middleware to the request processing pipeline.</span></span> <span data-ttu-id="ba4a7-116">该示例应用将添加[ `Cache-Control` ](https://tools.ietf.org/html/rfc7234#section-5.2)缓存最多 10 秒的可缓存响应的响应标头。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-116">The sample app adds a [`Cache-Control`](https://tools.ietf.org/html/rfc7234#section-5.2) header to the response that caches cacheable responses for up to 10 seconds.</span></span> <span data-ttu-id="ba4a7-117">该示例将发送[ `Vary` ](https://tools.ietf.org/html/rfc7231#section-7.1.4)标头用于配置中间件来提供缓存的响应才[ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4)后续请求标头匹配的原始请求。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-117">The sample sends a [`Vary`](https://tools.ietf.org/html/rfc7231#section-7.1.4) header to configure the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span> <span data-ttu-id="ba4a7-118">中的代码示例中， [CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue)并[HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames)需要`using`语句[Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers)命名空间。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-118">In the code example that follows, [CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue) and [HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames) require a `using` statement for the [Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers) namespace.</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17,22-29)]

<span data-ttu-id="ba4a7-119">响应缓存中间件仅缓存导致 200 （正常） 状态代码的服务器响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-119">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="ba4a7-120">任何其他响应，其中包括[错误页](xref:fundamentals/error-handling)，将忽略由中间件。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-120">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="ba4a7-121">响应包含经过身份验证的客户端的内容必须标记为不可缓存，以防止从存储和处理这些响应中间件。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-121">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="ba4a7-122">请参阅[缓存的条件](#conditions-for-caching)有关中间件如何确定响应是否可缓存的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-122">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="ba4a7-123">选项</span><span class="sxs-lookup"><span data-stu-id="ba4a7-123">Options</span></span>

<span data-ttu-id="ba4a7-124">中间件提供了三个选项用于控制响应缓存。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-124">The middleware offers three options for controlling response caching.</span></span>

| <span data-ttu-id="ba4a7-125">选项</span><span class="sxs-lookup"><span data-stu-id="ba4a7-125">Option</span></span>                | <span data-ttu-id="ba4a7-126">描述</span><span class="sxs-lookup"><span data-stu-id="ba4a7-126">Description</span></span> |
| --------------------- | ----------- |
| <span data-ttu-id="ba4a7-127">UseCaseSensitivePaths</span><span class="sxs-lookup"><span data-stu-id="ba4a7-127">UseCaseSensitivePaths</span></span> | <span data-ttu-id="ba4a7-128">确定是否在区分大小写的路径上会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="ba4a7-129">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-129">The default value is `false`.</span></span> |
| <span data-ttu-id="ba4a7-130">MaximumBodySize</span><span class="sxs-lookup"><span data-stu-id="ba4a7-130">MaximumBodySize</span></span>       | <span data-ttu-id="ba4a7-131">以字节为单位的响应正文最大缓存大小。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-131">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="ba4a7-132">默认值是`64 * 1024 * 1024`(64 MB)。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-132">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <span data-ttu-id="ba4a7-133">SizeLimit</span><span class="sxs-lookup"><span data-stu-id="ba4a7-133">SizeLimit</span></span>             | <span data-ttu-id="ba4a7-134">以字节为单位的响应缓存中间件大小限制。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-134">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="ba4a7-135">默认值是`100 * 1024 * 1024`(100 MB)。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-135">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |

<span data-ttu-id="ba4a7-136">下面的示例配置到中间件：</span><span class="sxs-lookup"><span data-stu-id="ba4a7-136">The following example configures the middleware to:</span></span>

* <span data-ttu-id="ba4a7-137">小于或等于 1024 字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-137">Cache responses smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="ba4a7-138">将响应存储通过区分大小写的路径 (例如，`/page1`和`/Page1`单独存储)。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-138">Store the responses by case-sensitive paths (for example, `/page1` and `/Page1` are stored separately).</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="ba4a7-139">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="ba4a7-139">VaryByQueryKeys</span></span>

<span data-ttu-id="ba4a7-140">在使用 MVC/Web API 控制器或 Razor 页面页模型`ResponseCache`属性指定设置适当的标头为响应缓存所需的参数。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-140">When using MVC/Web API controllers or Razor Pages page models, the `ResponseCache` attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="ba4a7-141">唯一参数`ResponseCache`严格需要中间件的属性是`VaryByQueryKeys`，这并不对应于实际的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-141">The only parameter of the `ResponseCache` attribute that strictly requires the middleware is `VaryByQueryKeys`, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="ba4a7-142">有关详细信息，请参阅[ResponseCache 属性](xref:performance/caching/response#responsecache-attribute)。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-142">For more information, see [ResponseCache Attribute](xref:performance/caching/response#responsecache-attribute).</span></span>

<span data-ttu-id="ba4a7-143">不使用时`ResponseCache`属性中，响应缓存可以与变化`VaryByQueryKeys`功能。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-143">When not using the `ResponseCache` attribute, response caching can be varied with the `VaryByQueryKeys` feature.</span></span> <span data-ttu-id="ba4a7-144">使用`ResponseCachingFeature`直接从`IFeatureCollection`的`HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="ba4a7-144">Use the `ResponseCachingFeature` directly from the `IFeatureCollection` of the `HttpContext`:</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="ba4a7-145">使用单个值等于`*`在`VaryByQueryKeys`随缓存所有请求查询参数而都变化。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-145">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="ba4a7-146">响应缓存中间件所使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="ba4a7-146">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="ba4a7-147">使用 HTTP 标头配置中间件的响应缓存。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-147">Response caching by the middleware is configured using HTTP headers.</span></span>

| <span data-ttu-id="ba4a7-148">Header</span><span class="sxs-lookup"><span data-stu-id="ba4a7-148">Header</span></span> | <span data-ttu-id="ba4a7-149">详细信息</span><span class="sxs-lookup"><span data-stu-id="ba4a7-149">Details</span></span> |
| ------ | ------- |
| <span data-ttu-id="ba4a7-150">授权</span><span class="sxs-lookup"><span data-stu-id="ba4a7-150">Authorization</span></span> | <span data-ttu-id="ba4a7-151">如果标头存在，不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-151">The response isn't cached if the header exists.</span></span> |
| <span data-ttu-id="ba4a7-152">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="ba4a7-152">Cache-Control</span></span> | <span data-ttu-id="ba4a7-153">中间件只考虑缓存响应标有`public`缓存指令。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-153">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="ba4a7-154">控制缓存使用以下参数：</span><span class="sxs-lookup"><span data-stu-id="ba4a7-154">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="ba4a7-155">max-age</span><span class="sxs-lookup"><span data-stu-id="ba4a7-155">max-age</span></span></li><li><span data-ttu-id="ba4a7-156">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="ba4a7-156">max-stale&#8224;</span></span></li><li><span data-ttu-id="ba4a7-157">min-fresh</span><span class="sxs-lookup"><span data-stu-id="ba4a7-157">min-fresh</span></span></li><li><span data-ttu-id="ba4a7-158">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="ba4a7-158">must-revalidate</span></span></li><li><span data-ttu-id="ba4a7-159">无缓存</span><span class="sxs-lookup"><span data-stu-id="ba4a7-159">no-cache</span></span></li><li><span data-ttu-id="ba4a7-160">无存储</span><span class="sxs-lookup"><span data-stu-id="ba4a7-160">no-store</span></span></li><li><span data-ttu-id="ba4a7-161">only-if-cached</span><span class="sxs-lookup"><span data-stu-id="ba4a7-161">only-if-cached</span></span></li><li><span data-ttu-id="ba4a7-162">private</span><span class="sxs-lookup"><span data-stu-id="ba4a7-162">private</span></span></li><li><span data-ttu-id="ba4a7-163">public</span><span class="sxs-lookup"><span data-stu-id="ba4a7-163">public</span></span></li><li><span data-ttu-id="ba4a7-164">s maxage</span><span class="sxs-lookup"><span data-stu-id="ba4a7-164">s-maxage</span></span></li><li><span data-ttu-id="ba4a7-165">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="ba4a7-165">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="ba4a7-166">&#8224;如果没有限制指定为`max-stale`，中间件不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-166">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="ba4a7-167">&#8225;`proxy-revalidate`具有相同的效果`must-revalidate`。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-167">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="ba4a7-168">有关详细信息，请参阅[RFC 7231:请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-168">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| <span data-ttu-id="ba4a7-169">杂注</span><span class="sxs-lookup"><span data-stu-id="ba4a7-169">Pragma</span></span> | <span data-ttu-id="ba4a7-170">一个`Pragma: no-cache`请求标头中的生成相同的效果`Cache-Control: no-cache`。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-170">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="ba4a7-171">此标头中的相关指令重写`Cache-Control`标头，如果存在。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-171">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="ba4a7-172">考虑使用 HTTP/1.0 的向后兼容性。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-172">Considered for backward compatibility with HTTP/1.0.</span></span> |
| <span data-ttu-id="ba4a7-173">Set-Cookie</span><span class="sxs-lookup"><span data-stu-id="ba4a7-173">Set-Cookie</span></span> | <span data-ttu-id="ba4a7-174">如果标头存在，不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-174">The response isn't cached if the header exists.</span></span> <span data-ttu-id="ba4a7-175">设置一个或多个 cookie 在请求处理管道中的任何中间件可防止响应缓存中间件缓存响应 (例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata))。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-175">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| <span data-ttu-id="ba4a7-176">改变</span><span class="sxs-lookup"><span data-stu-id="ba4a7-176">Vary</span></span> | <span data-ttu-id="ba4a7-177">`Vary`标头可用于不同的缓存的响应的另一个标头。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-177">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="ba4a7-178">例如，缓存响应的编码，方法是包括`Vary: Accept-Encoding`标头，来缓存响应的请求的标头`Accept-Encoding: gzip`和`Accept-Encoding: text/plain`单独。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-178">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="ba4a7-179">响应标头值为`*`永远不会存储。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-179">A response with a header value of `*` is never stored.</span></span> |
| <span data-ttu-id="ba4a7-180">过期</span><span class="sxs-lookup"><span data-stu-id="ba4a7-180">Expires</span></span> | <span data-ttu-id="ba4a7-181">此标头被视为陈旧的响应不是存储或检索除非重写由其他`Cache-Control`标头。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-181">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| <span data-ttu-id="ba4a7-182">-None-If-match</span><span class="sxs-lookup"><span data-stu-id="ba4a7-182">If-None-Match</span></span> | <span data-ttu-id="ba4a7-183">如果值不是完整的响应从缓存提供`*`和`ETag`的响应中不匹配任何提供的值。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-183">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="ba4a7-184">否则，就会提供 304 （未修改） 响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-184">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| <span data-ttu-id="ba4a7-185">If-Modified-Since</span><span class="sxs-lookup"><span data-stu-id="ba4a7-185">If-Modified-Since</span></span> | <span data-ttu-id="ba4a7-186">如果`If-None-Match`标头不存在，如果缓存的响应日期比提供的值的完整响应从缓存提供。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-186">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="ba4a7-187">否则，就会提供 304 （未修改） 响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-187">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| <span data-ttu-id="ba4a7-188">日期</span><span class="sxs-lookup"><span data-stu-id="ba4a7-188">Date</span></span> | <span data-ttu-id="ba4a7-189">从缓存中，提供服务时`Date`由中间件设置标头，如果它未提供原始响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-189">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| <span data-ttu-id="ba4a7-190">内容长度</span><span class="sxs-lookup"><span data-stu-id="ba4a7-190">Content-Length</span></span> | <span data-ttu-id="ba4a7-191">从缓存中，提供服务时`Content-Length`由中间件设置标头，如果它未提供原始响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-191">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| <span data-ttu-id="ba4a7-192">年龄</span><span class="sxs-lookup"><span data-stu-id="ba4a7-192">Age</span></span> | <span data-ttu-id="ba4a7-193">`Age`忽略原始响应中发送的标头。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-193">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="ba4a7-194">提供缓存的响应时，中间件会计算新值。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-194">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="ba4a7-195">缓存遵循请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="ba4a7-195">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="ba4a7-196">中间件遵从的规则[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-196">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="ba4a7-197">规则需要遵守是有效的缓存`Cache-Control`客户端发送的标头。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-197">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="ba4a7-198">在规范中，客户端可以发出请求的`no-cache`标头值和强制服务器生成的每个请求的新响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-198">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="ba4a7-199">目前，没有任何开发人员可以控制此缓存行为时使用中间件，因为中间件符合官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-199">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="ba4a7-200">为了更好地控制缓存行为，将介绍其他缓存功能的 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-200">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="ba4a7-201">请参见下面的主题：</span><span class="sxs-lookup"><span data-stu-id="ba4a7-201">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="ba4a7-202">疑难解答</span><span class="sxs-lookup"><span data-stu-id="ba4a7-202">Troubleshooting</span></span>

<span data-ttu-id="ba4a7-203">如果缓存行为不会按预期方式，，确认响应是可缓存，能够从缓存中提供。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-203">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="ba4a7-204">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-204">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="ba4a7-205">启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-205">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="ba4a7-206">当测试和故障排除的缓存行为，浏览器可以设置影响缓存产生不利的请求标头。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-206">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="ba4a7-207">例如，设置浏览器可能`Cache-Control`标头`no-cache`或`max-age=0`刷新页面时。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-207">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="ba4a7-208">以下工具可以显式设置请求标头，并且是测试缓存的首选：</span><span class="sxs-lookup"><span data-stu-id="ba4a7-208">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="ba4a7-209">Fiddler</span><span class="sxs-lookup"><span data-stu-id="ba4a7-209">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="ba4a7-210">Postman</span><span class="sxs-lookup"><span data-stu-id="ba4a7-210">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="ba4a7-211">缓存的条件</span><span class="sxs-lookup"><span data-stu-id="ba4a7-211">Conditions for caching</span></span>

* <span data-ttu-id="ba4a7-212">请求必须导致服务器响应 200 （正常） 状态代码。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-212">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="ba4a7-213">请求方法必须是 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-213">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="ba4a7-214">在`Startup.Configure`，必须在需要压缩的中间件前面放置响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-214">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require compression.</span></span> <span data-ttu-id="ba4a7-215">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-215">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="ba4a7-216">`Authorization`标头不能存在。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-216">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="ba4a7-217">`Cache-Control` 标头参数必须是有效的并且必须标记为响应`public`且未标记为`private`。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-217">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="ba4a7-218">`Pragma: no-cache`标头不能存在如果`Cache-Control`标头不存在，作为`Cache-Control`标头重写`Pragma`标头时存在。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-218">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="ba4a7-219">`Set-Cookie`标头不能存在。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-219">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="ba4a7-220">`Vary` 标头参数必须是有效且不等于`*`。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-220">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="ba4a7-221">`Content-Length`标头值 (如果设置) 必须与匹配的响应正文的大小。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-221">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="ba4a7-222">[IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature)未使用。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-222">The [IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature) isn't used.</span></span>
* <span data-ttu-id="ba4a7-223">响应不能由指定陈旧`Expires`标头和`max-age`和`s-maxage`缓存指令。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-223">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="ba4a7-224">响应缓冲必须成功，并且响应的大小必须小于已配置或默认`SizeLimit`。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-224">Response buffering must be successful, and the size of the response must be smaller than the configured or default `SizeLimit`.</span></span>
* <span data-ttu-id="ba4a7-225">响应必须是可根据缓存[RFC 7234](https://tools.ietf.org/html/rfc7234)规范。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-225">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="ba4a7-226">例如，`no-store`指令不能存在请求或响应标头字段中。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-226">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="ba4a7-227">请参阅*第 3 部分：在缓存中存储响应*的[RFC 7234](https://tools.ietf.org/html/rfc7234)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-227">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="ba4a7-228">防伪系统用于生成安全令牌，以防止跨站点请求伪造 (CSRF) 攻击集`Cache-Control`并`Pragma`标头`no-cache`，以便不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-228">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="ba4a7-229">有关如何禁用 antiforgery 令牌 HTML 窗体元素的信息，请参阅[ASP.NET Core antiforgery 配置](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)。</span><span class="sxs-lookup"><span data-stu-id="ba4a7-229">For information on how to disable antiforgery tokens for HTML form elements, see [ASP.NET Core antiforgery configuration](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba4a7-230">其他资源</span><span class="sxs-lookup"><span data-stu-id="ba4a7-230">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
