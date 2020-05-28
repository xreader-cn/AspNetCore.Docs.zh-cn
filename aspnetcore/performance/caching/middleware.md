---
<span data-ttu-id="2cfc9-101">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-102">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-103">'Identity'</span></span>
- <span data-ttu-id="2cfc9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-105">'Razor'</span></span>
- <span data-ttu-id="2cfc9-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="2cfc9-107">ASP.NET Core 中的响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="2cfc9-107">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="2cfc9-108">作者：[John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="2cfc9-108">By [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2cfc9-109">本文介绍如何在 ASP.NET Core 应用程序中配置响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-109">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="2cfc9-110">中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-110">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="2cfc9-111">有关 HTTP 缓存和属性的介绍 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，请参阅[响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-111">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="2cfc9-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2cfc9-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="2cfc9-113">配置</span><span class="sxs-lookup"><span data-stu-id="2cfc9-113">Configuration</span></span>

<span data-ttu-id="2cfc9-114">响应缓存中间件可通过共享框架隐式地用于 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-114">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="2cfc9-115">在中 `Startup.ConfigureServices` ，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-115">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="2cfc9-116">将应用程序配置为将中间件与 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 扩展方法一起使用，该扩展方法将中间件添加到中的请求处理管道 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-116">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

<span data-ttu-id="2cfc9-117">示例应用添加标头以在后续请求时控制缓存：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-117">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="2cfc9-118">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)：将可缓存的响应缓存多达10秒。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-118">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="2cfc9-119">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：仅当后续请求的接受编码标头与原始请求的[接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)标头匹配时，才将中间件配置为提供缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-119">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="2cfc9-120">前面的标头不会写入响应，并在控制器、操作或页上被重写 Razor ：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-120">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="2cfc9-121">具有[[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-121">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="2cfc9-122">即使未设置属性，这也适用。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-122">This applies even if a property isn't set.</span></span> <span data-ttu-id="2cfc9-123">例如，省略[VaryByHeader](/aspnet/core/performance/caching/response#vary)属性将导致从响应中删除相应的标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-123">For example, omitting the [VaryByHeader](/aspnet/core/performance/caching/response#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="2cfc9-124">响应缓存中间件仅缓存服务器响应，导致了200（正常）状态代码。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-124">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="2cfc9-125">中间件将忽略任何其他响应，包括[错误页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-125">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="2cfc9-126">包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-126">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="2cfc9-127">有关中间件如何确定响应是否可缓存的详细信息，请参阅[缓存的条件](#conditions-for-caching)。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-127">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="2cfc9-128">选项</span><span class="sxs-lookup"><span data-stu-id="2cfc9-128">Options</span></span>

<span data-ttu-id="2cfc9-129">响应缓存选项如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-129">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="2cfc9-130">选项</span><span class="sxs-lookup"><span data-stu-id="2cfc9-130">Option</span></span> | <span data-ttu-id="2cfc9-131">说明</span><span class="sxs-lookup"><span data-stu-id="2cfc9-131">Description</span></span> |
| ---
<span data-ttu-id="2cfc9-132">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-133">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-134">'Identity'</span></span>
- <span data-ttu-id="2cfc9-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-136">'Razor'</span></span>
- <span data-ttu-id="2cfc9-137">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-137">'SignalR' uid:</span></span> 

<span data-ttu-id="2cfc9-138">--- |---标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-138">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-139">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-140">'Identity'</span></span>
- <span data-ttu-id="2cfc9-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-142">'Razor'</span></span>
- <span data-ttu-id="2cfc9-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cfc9-144">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-145">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-146">'Identity'</span></span>
- <span data-ttu-id="2cfc9-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-148">'Razor'</span></span>
- <span data-ttu-id="2cfc9-149">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cfc9-150">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-151">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-152">'Identity'</span></span>
- <span data-ttu-id="2cfc9-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-154">'Razor'</span></span>
- <span data-ttu-id="2cfc9-155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-155">'SignalR' uid:</span></span> 

<span data-ttu-id="2cfc9-156">------ | |<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> |响应正文的最大可缓存大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-156">------ | | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="2cfc9-157">默认值为 `64 * 1024 * 1024` （64 MB）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-157">The default value is `64 * 1024 * 1024` (64 MB).</span></span> <span data-ttu-id="2cfc9-158">| |<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> |响应缓存中间件的大小限制（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-158">| | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="2cfc9-159">默认值为 `100 * 1024 * 1024` （100 MB）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-159">The default value is `100 * 1024 * 1024` (100 MB).</span></span> <span data-ttu-id="2cfc9-160">| |<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> |确定是否将响应缓存在区分大小写的路径上。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-160">| | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="2cfc9-161">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-161">The default value is `false`.</span></span> |

<span data-ttu-id="2cfc9-162">下面的示例将中间件配置为：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-162">The following example configures the middleware to:</span></span>

* <span data-ttu-id="2cfc9-163">大小小于或等于1024字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-163">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="2cfc9-164">将响应存储为区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-164">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="2cfc9-165">例如， `/page1` 和 `/Page1` 分别存储。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-165">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="2cfc9-166">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="2cfc9-166">VaryByQueryKeys</span></span>

<span data-ttu-id="2cfc9-167">使用 MVC/web API 控制器或 Razor 页面页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性指定为响应缓存设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-167">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="2cfc9-168">`[ResponseCache]`严格要求中间件的属性的唯一参数是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，它不对应于实际 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-168">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="2cfc9-169">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-169">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="2cfc9-170">如果不使用 `[ResponseCache]` 属性，响应缓存可能会随而变化 `VaryByQueryKeys` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-170">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="2cfc9-171"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-171">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="2cfc9-172">使用等于中的单个值 `*` 会 `VaryByQueryKeys` 根据所有请求查询参数改变缓存。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-172">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="2cfc9-173">响应缓存中间件使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="2cfc9-173">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="2cfc9-174">下表提供了有关影响响应缓存的 HTTP 标头的信息。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-174">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="2cfc9-175">Header</span><span class="sxs-lookup"><span data-stu-id="2cfc9-175">Header</span></span> | <span data-ttu-id="2cfc9-176">详细信息</span><span class="sxs-lookup"><span data-stu-id="2cfc9-176">Details</span></span> |
| ---
<span data-ttu-id="2cfc9-177">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-178">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-179">'Identity'</span></span>
- <span data-ttu-id="2cfc9-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-181">'Razor'</span></span>
- <span data-ttu-id="2cfc9-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-182">'SignalR' uid:</span></span> 

<span data-ttu-id="2cfc9-183">--- |---标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-183">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-184">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-185">'Identity'</span></span>
- <span data-ttu-id="2cfc9-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-187">'Razor'</span></span>
- <span data-ttu-id="2cfc9-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-188">'SignalR' uid:</span></span> 

<span data-ttu-id="2cfc9-189">---- | |`Authorization` |如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-189">---- | | `Authorization` | The response isn't cached if the header exists.</span></span> <span data-ttu-id="2cfc9-190">| |`Cache-Control` |中间件仅考虑用缓存指令标记的缓存响应 `public` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-190">| | `Cache-Control` | The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="2cfc9-191">具有以下参数的控件缓存：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-191">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="2cfc9-192">最大期限</span><span class="sxs-lookup"><span data-stu-id="2cfc9-192">max-age</span></span></li><li><span data-ttu-id="2cfc9-193">最大过期&#8224;</span><span class="sxs-lookup"><span data-stu-id="2cfc9-193">max-stale&#8224;</span></span></li><li><span data-ttu-id="2cfc9-194">最小-新</span><span class="sxs-lookup"><span data-stu-id="2cfc9-194">min-fresh</span></span></li><li><span data-ttu-id="2cfc9-195">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="2cfc9-195">must-revalidate</span></span></li><li><span data-ttu-id="2cfc9-196">no-cache</span><span class="sxs-lookup"><span data-stu-id="2cfc9-196">no-cache</span></span></li><li><span data-ttu-id="2cfc9-197">无-商店</span><span class="sxs-lookup"><span data-stu-id="2cfc9-197">no-store</span></span></li><li><span data-ttu-id="2cfc9-198">仅限-缓存</span><span class="sxs-lookup"><span data-stu-id="2cfc9-198">only-if-cached</span></span></li><li><span data-ttu-id="2cfc9-199">private</span><span class="sxs-lookup"><span data-stu-id="2cfc9-199">private</span></span></li><li><span data-ttu-id="2cfc9-200">public</span><span class="sxs-lookup"><span data-stu-id="2cfc9-200">public</span></span></li><li><span data-ttu-id="2cfc9-201">s-maxage</span><span class="sxs-lookup"><span data-stu-id="2cfc9-201">s-maxage</span></span></li><li><span data-ttu-id="2cfc9-202">代理重新验证&#8225;</span><span class="sxs-lookup"><span data-stu-id="2cfc9-202">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="2cfc9-203">&#8224;如果未指定任何限制 `max-stale` ，则中间件不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-203">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="2cfc9-204">&#8225;`proxy-revalidate` 的效果与相同 `must-revalidate` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-204">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="2cfc9-205">有关详细信息，请参阅[RFC 7231：请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-205">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> <span data-ttu-id="2cfc9-206">| |`Pragma` |`Pragma: no-cache`请求中的标头将产生与相同的效果 `Cache-Control: no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-206">| | `Pragma` | A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="2cfc9-207">标头中的相关指令 `Cache-Control` （如果存在）将重写此标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-207">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="2cfc9-208">考虑向后兼容 HTTP/1.0。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-208">Considered for backward compatibility with HTTP/1.0.</span></span> <span data-ttu-id="2cfc9-209">| |`Set-Cookie` |如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-209">| | `Set-Cookie` | The response isn't cached if the header exists.</span></span> <span data-ttu-id="2cfc9-210">请求处理管道中设置一个或多个 cookie 的任何中间件会阻止响应缓存中间件缓存响应（例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata)）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-210">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  <span data-ttu-id="2cfc9-211">| |`Vary` |`Vary`标头用于根据另一个标头改变缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-211">| | `Vary` | The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="2cfc9-212">例如，通过包含标头来缓存响应， `Vary: Accept-Encoding` 此标头将使用标头和单独的请求来缓存响应 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-212">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="2cfc9-213">永远不会存储标头值 `*` 为的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-213">A response with a header value of `*` is never stored.</span></span> <span data-ttu-id="2cfc9-214">| |`Expires` |除非由其他标头重写，否则不会存储或检索此标头过时的响应 `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-214">| | `Expires` | A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> <span data-ttu-id="2cfc9-215">| |`If-None-Match` |如果值不为 `*` ，并且响应的与提供的任何值都不匹配，则将从缓存中提供完整响应 `ETag` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-215">| | `If-None-Match` | The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="2cfc9-216">否则，将提供304（未修改）响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-216">Otherwise, a 304 (Not Modified) response is served.</span></span> <span data-ttu-id="2cfc9-217">| |`If-Modified-Since` |如果 `If-None-Match` 标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-217">| | `If-Modified-Since` | If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="2cfc9-218">否则，将提供*304-未修改*响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-218">Otherwise, a *304 - Not Modified* response is served.</span></span> <span data-ttu-id="2cfc9-219">| |`Date` |从缓存提供时， `Date` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-219">| | `Date` | When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> <span data-ttu-id="2cfc9-220">| |`Content-Length` |从缓存提供时， `Content-Length` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-220">| | `Content-Length` | When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> <span data-ttu-id="2cfc9-221">| |`Age` |`Age`忽略原始响应中发送的标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-221">| | `Age` | The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="2cfc9-222">中间件在为缓存的响应提供服务时计算一个新值。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-222">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="2cfc9-223">缓存遵从请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="2cfc9-223">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="2cfc9-224">中间件遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-224">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="2cfc9-225">规则要求使用缓存来服从 `Cache-Control` 客户端发送的有效标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-225">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="2cfc9-226">在规范下，客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-226">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="2cfc9-227">目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-227">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="2cfc9-228">为了更好地控制缓存行为，浏览 ASP.NET Core 的其他缓存功能。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-228">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="2cfc9-229">请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-229">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="2cfc9-230">疑难解答</span><span class="sxs-lookup"><span data-stu-id="2cfc9-230">Troubleshooting</span></span>

<span data-ttu-id="2cfc9-231">如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-231">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="2cfc9-232">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-232">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="2cfc9-233">启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-233">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="2cfc9-234">在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-234">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="2cfc9-235">例如，浏览器可以在 `Cache-Control` `no-cache` 刷新页面时将标题设置为或 `max-age=0` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-235">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="2cfc9-236">以下工具可以显式设置请求标头，并优先于测试缓存：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-236">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="2cfc9-237">Fiddler</span><span class="sxs-lookup"><span data-stu-id="2cfc9-237">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="2cfc9-238">Postman</span><span class="sxs-lookup"><span data-stu-id="2cfc9-238">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="2cfc9-239">缓存条件</span><span class="sxs-lookup"><span data-stu-id="2cfc9-239">Conditions for caching</span></span>

* <span data-ttu-id="2cfc9-240">请求必须导致服务器响应，状态代码为200（正常）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-240">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="2cfc9-241">请求方法必须为 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-241">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="2cfc9-242">在中 `Startup.Configure` ，必须将响应缓存中间件置于需要缓存的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-242">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="2cfc9-243">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-243">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="2cfc9-244">`Authorization`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-244">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="2cfc9-245">`Cache-Control`标头参数必须是有效的，并且响应必须标记 `public` 且未标记 `private` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-245">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="2cfc9-246">`Pragma: no-cache`如果标头不存在，则标头不得出现 `Cache-Control` ，因为标头会 `Cache-Control` `Pragma` 在存在时覆盖标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-246">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="2cfc9-247">`Set-Cookie`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-247">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="2cfc9-248">`Vary`标头参数必须有效并且不等于 `*` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-248">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="2cfc9-249">`Content-Length`标头值（如果已设置）必须与响应正文的大小匹配。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-249">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="2cfc9-250"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>不使用。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-250">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="2cfc9-251">`Expires`标头和 `max-age` 和缓存指令指定的响应不能过时 `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-251">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="2cfc9-252">响应缓冲必须成功。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-252">Response buffering must be successful.</span></span> <span data-ttu-id="2cfc9-253">响应的大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-253">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="2cfc9-254">响应的正文大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-254">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="2cfc9-255">必须根据[RFC 7234](https://tools.ietf.org/html/rfc7234)规范来缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-255">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="2cfc9-256">例如， `no-store` 指令在请求或响应标头字段中不得存在。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-256">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="2cfc9-257">有关详细信息，请参阅*第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234)的缓存中。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-257">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="2cfc9-258">用于生成安全令牌以防止跨站点请求伪造（CSRF）攻击的防伪系统将 `Cache-Control` 和 `Pragma` 标头设置为， `no-cache` 以便不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-258">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="2cfc9-259">有关如何为 HTML 窗体元素禁用防伪标记的信息，请参阅 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-259">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2cfc9-260">其他资源</span><span class="sxs-lookup"><span data-stu-id="2cfc9-260">Additional resources</span></span>

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

<span data-ttu-id="2cfc9-261">本文介绍如何在 ASP.NET Core 应用程序中配置响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-261">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="2cfc9-262">中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-262">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="2cfc9-263">有关 HTTP 缓存和属性的介绍 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，请参阅[响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-263">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="2cfc9-264">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2cfc9-264">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="2cfc9-265">配置</span><span class="sxs-lookup"><span data-stu-id="2cfc9-265">Configuration</span></span>

<span data-ttu-id="2cfc9-266">使用[AspNetCore 元包](xref:fundamentals/metapackage-app)或添加对[AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包的包引用。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-266">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="2cfc9-267">在中 `Startup.ConfigureServices` ，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-267">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="2cfc9-268">将应用程序配置为将中间件与 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 扩展方法一起使用，该扩展方法将中间件添加到中的请求处理管道 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-268">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="2cfc9-269">示例应用添加标头以在后续请求时控制缓存：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-269">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="2cfc9-270">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)：将可缓存的响应缓存多达10秒。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-270">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="2cfc9-271">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：仅当后续请求的接受编码标头与原始请求的[接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)标头匹配时，才将中间件配置为提供缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-271">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="2cfc9-272">前面的标头不会写入响应，并在控制器、操作或页上被重写 Razor ：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-272">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="2cfc9-273">具有[[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-273">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="2cfc9-274">即使未设置属性，这也适用。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-274">This applies even if a property isn't set.</span></span> <span data-ttu-id="2cfc9-275">例如，省略[VaryByHeader](/aspnet/core/performance/caching/response#vary)属性将导致从响应中删除相应的标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-275">For example, omitting the [VaryByHeader](/aspnet/core/performance/caching/response#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="2cfc9-276">响应缓存中间件仅缓存服务器响应，导致了200（正常）状态代码。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-276">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="2cfc9-277">中间件将忽略任何其他响应，包括[错误页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-277">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="2cfc9-278">包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-278">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="2cfc9-279">有关中间件如何确定响应是否可缓存的详细信息，请参阅[缓存的条件](#conditions-for-caching)。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-279">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="2cfc9-280">选项</span><span class="sxs-lookup"><span data-stu-id="2cfc9-280">Options</span></span>

<span data-ttu-id="2cfc9-281">响应缓存选项如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-281">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="2cfc9-282">选项</span><span class="sxs-lookup"><span data-stu-id="2cfc9-282">Option</span></span> | <span data-ttu-id="2cfc9-283">说明</span><span class="sxs-lookup"><span data-stu-id="2cfc9-283">Description</span></span> |
| ---
<span data-ttu-id="2cfc9-284">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-285">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-286">'Identity'</span></span>
- <span data-ttu-id="2cfc9-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-288">'Razor'</span></span>
- <span data-ttu-id="2cfc9-289">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-289">'SignalR' uid:</span></span> 

<span data-ttu-id="2cfc9-290">--- |---标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-290">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-291">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-292">'Identity'</span></span>
- <span data-ttu-id="2cfc9-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-294">'Razor'</span></span>
- <span data-ttu-id="2cfc9-295">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cfc9-296">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-297">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-298">'Identity'</span></span>
- <span data-ttu-id="2cfc9-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-300">'Razor'</span></span>
- <span data-ttu-id="2cfc9-301">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cfc9-302">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-303">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-304">'Identity'</span></span>
- <span data-ttu-id="2cfc9-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-306">'Razor'</span></span>
- <span data-ttu-id="2cfc9-307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-307">'SignalR' uid:</span></span> 

<span data-ttu-id="2cfc9-308">------ | |<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> |响应正文的最大可缓存大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-308">------ | | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="2cfc9-309">默认值为 `64 * 1024 * 1024` （64 MB）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-309">The default value is `64 * 1024 * 1024` (64 MB).</span></span> <span data-ttu-id="2cfc9-310">| |<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> |响应缓存中间件的大小限制（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-310">| | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="2cfc9-311">默认值为 `100 * 1024 * 1024` （100 MB）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-311">The default value is `100 * 1024 * 1024` (100 MB).</span></span> <span data-ttu-id="2cfc9-312">| |<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> |确定是否将响应缓存在区分大小写的路径上。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-312">| | <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="2cfc9-313">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-313">The default value is `false`.</span></span> |

<span data-ttu-id="2cfc9-314">下面的示例将中间件配置为：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-314">The following example configures the middleware to:</span></span>

* <span data-ttu-id="2cfc9-315">大小小于或等于1024字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-315">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="2cfc9-316">将响应存储为区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-316">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="2cfc9-317">例如， `/page1` 和 `/Page1` 分别存储。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-317">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="2cfc9-318">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="2cfc9-318">VaryByQueryKeys</span></span>

<span data-ttu-id="2cfc9-319">使用 MVC/web API 控制器或 Razor 页面页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性指定为响应缓存设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-319">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="2cfc9-320">`[ResponseCache]`严格要求中间件的属性的唯一参数是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，它不对应于实际 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-320">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="2cfc9-321">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-321">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="2cfc9-322">如果不使用 `[ResponseCache]` 属性，响应缓存可能会随而变化 `VaryByQueryKeys` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-322">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="2cfc9-323"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-323">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="2cfc9-324">使用等于中的单个值 `*` 会 `VaryByQueryKeys` 根据所有请求查询参数改变缓存。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-324">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="2cfc9-325">响应缓存中间件使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="2cfc9-325">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="2cfc9-326">下表提供了有关影响响应缓存的 HTTP 标头的信息。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-326">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="2cfc9-327">Header</span><span class="sxs-lookup"><span data-stu-id="2cfc9-327">Header</span></span> | <span data-ttu-id="2cfc9-328">详细信息</span><span class="sxs-lookup"><span data-stu-id="2cfc9-328">Details</span></span> |
| ---
<span data-ttu-id="2cfc9-329">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-329">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-330">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-331">'Identity'</span></span>
- <span data-ttu-id="2cfc9-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-333">'Razor'</span></span>
- <span data-ttu-id="2cfc9-334">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-334">'SignalR' uid:</span></span> 

<span data-ttu-id="2cfc9-335">--- |---标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-335">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cfc9-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-336">'Blazor'</span></span>
- <span data-ttu-id="2cfc9-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-337">'Identity'</span></span>
- <span data-ttu-id="2cfc9-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cfc9-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cfc9-339">'Razor'</span></span>
- <span data-ttu-id="2cfc9-340">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2cfc9-340">'SignalR' uid:</span></span> 

<span data-ttu-id="2cfc9-341">---- | |`Authorization` |如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-341">---- | | `Authorization` | The response isn't cached if the header exists.</span></span> <span data-ttu-id="2cfc9-342">| |`Cache-Control` |中间件仅考虑用缓存指令标记的缓存响应 `public` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-342">| | `Cache-Control` | The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="2cfc9-343">具有以下参数的控件缓存：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-343">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="2cfc9-344">最大期限</span><span class="sxs-lookup"><span data-stu-id="2cfc9-344">max-age</span></span></li><li><span data-ttu-id="2cfc9-345">最大过期&#8224;</span><span class="sxs-lookup"><span data-stu-id="2cfc9-345">max-stale&#8224;</span></span></li><li><span data-ttu-id="2cfc9-346">最小-新</span><span class="sxs-lookup"><span data-stu-id="2cfc9-346">min-fresh</span></span></li><li><span data-ttu-id="2cfc9-347">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="2cfc9-347">must-revalidate</span></span></li><li><span data-ttu-id="2cfc9-348">no-cache</span><span class="sxs-lookup"><span data-stu-id="2cfc9-348">no-cache</span></span></li><li><span data-ttu-id="2cfc9-349">无-商店</span><span class="sxs-lookup"><span data-stu-id="2cfc9-349">no-store</span></span></li><li><span data-ttu-id="2cfc9-350">仅限-缓存</span><span class="sxs-lookup"><span data-stu-id="2cfc9-350">only-if-cached</span></span></li><li><span data-ttu-id="2cfc9-351">private</span><span class="sxs-lookup"><span data-stu-id="2cfc9-351">private</span></span></li><li><span data-ttu-id="2cfc9-352">public</span><span class="sxs-lookup"><span data-stu-id="2cfc9-352">public</span></span></li><li><span data-ttu-id="2cfc9-353">s-maxage</span><span class="sxs-lookup"><span data-stu-id="2cfc9-353">s-maxage</span></span></li><li><span data-ttu-id="2cfc9-354">代理重新验证&#8225;</span><span class="sxs-lookup"><span data-stu-id="2cfc9-354">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="2cfc9-355">&#8224;如果未指定任何限制 `max-stale` ，则中间件不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-355">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="2cfc9-356">&#8225;`proxy-revalidate` 的效果与相同 `must-revalidate` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-356">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="2cfc9-357">有关详细信息，请参阅[RFC 7231：请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-357">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> <span data-ttu-id="2cfc9-358">| |`Pragma` |`Pragma: no-cache`请求中的标头将产生与相同的效果 `Cache-Control: no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-358">| | `Pragma` | A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="2cfc9-359">标头中的相关指令 `Cache-Control` （如果存在）将重写此标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-359">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="2cfc9-360">考虑向后兼容 HTTP/1.0。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-360">Considered for backward compatibility with HTTP/1.0.</span></span> <span data-ttu-id="2cfc9-361">| |`Set-Cookie` |如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-361">| | `Set-Cookie` | The response isn't cached if the header exists.</span></span> <span data-ttu-id="2cfc9-362">请求处理管道中设置一个或多个 cookie 的任何中间件会阻止响应缓存中间件缓存响应（例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata)）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-362">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  <span data-ttu-id="2cfc9-363">| |`Vary` |`Vary`标头用于根据另一个标头改变缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-363">| | `Vary` | The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="2cfc9-364">例如，通过包含标头来缓存响应， `Vary: Accept-Encoding` 此标头将使用标头和单独的请求来缓存响应 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-364">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="2cfc9-365">永远不会存储标头值 `*` 为的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-365">A response with a header value of `*` is never stored.</span></span> <span data-ttu-id="2cfc9-366">| |`Expires` |除非由其他标头重写，否则不会存储或检索此标头过时的响应 `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-366">| | `Expires` | A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> <span data-ttu-id="2cfc9-367">| |`If-None-Match` |如果值不为 `*` ，并且响应的与提供的任何值都不匹配，则将从缓存中提供完整响应 `ETag` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-367">| | `If-None-Match` | The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="2cfc9-368">否则，将提供304（未修改）响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-368">Otherwise, a 304 (Not Modified) response is served.</span></span> <span data-ttu-id="2cfc9-369">| |`If-Modified-Since` |如果 `If-None-Match` 标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-369">| | `If-Modified-Since` | If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="2cfc9-370">否则，将提供*304-未修改*响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-370">Otherwise, a *304 - Not Modified* response is served.</span></span> <span data-ttu-id="2cfc9-371">| |`Date` |从缓存提供时， `Date` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-371">| | `Date` | When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> <span data-ttu-id="2cfc9-372">| |`Content-Length` |从缓存提供时， `Content-Length` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-372">| | `Content-Length` | When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> <span data-ttu-id="2cfc9-373">| |`Age` |`Age`忽略原始响应中发送的标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-373">| | `Age` | The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="2cfc9-374">中间件在为缓存的响应提供服务时计算一个新值。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-374">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="2cfc9-375">缓存遵从请求缓存控制指令</span><span class="sxs-lookup"><span data-stu-id="2cfc9-375">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="2cfc9-376">中间件遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-376">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="2cfc9-377">规则要求使用缓存来服从 `Cache-Control` 客户端发送的有效标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-377">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="2cfc9-378">在规范下，客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-378">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="2cfc9-379">目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-379">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="2cfc9-380">为了更好地控制缓存行为，浏览 ASP.NET Core 的其他缓存功能。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-380">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="2cfc9-381">请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-381">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="2cfc9-382">疑难解答</span><span class="sxs-lookup"><span data-stu-id="2cfc9-382">Troubleshooting</span></span>

<span data-ttu-id="2cfc9-383">如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-383">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="2cfc9-384">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-384">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="2cfc9-385">启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-385">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="2cfc9-386">在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-386">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="2cfc9-387">例如，浏览器可以在 `Cache-Control` `no-cache` 刷新页面时将标题设置为或 `max-age=0` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-387">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="2cfc9-388">以下工具可以显式设置请求标头，并优先于测试缓存：</span><span class="sxs-lookup"><span data-stu-id="2cfc9-388">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="2cfc9-389">Fiddler</span><span class="sxs-lookup"><span data-stu-id="2cfc9-389">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="2cfc9-390">Postman</span><span class="sxs-lookup"><span data-stu-id="2cfc9-390">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="2cfc9-391">缓存条件</span><span class="sxs-lookup"><span data-stu-id="2cfc9-391">Conditions for caching</span></span>

* <span data-ttu-id="2cfc9-392">请求必须导致服务器响应，状态代码为200（正常）。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-392">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="2cfc9-393">请求方法必须为 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-393">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="2cfc9-394">在中 `Startup.Configure` ，必须将响应缓存中间件置于需要缓存的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-394">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="2cfc9-395">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-395">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="2cfc9-396">`Authorization`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-396">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="2cfc9-397">`Cache-Control`标头参数必须是有效的，并且响应必须标记 `public` 且未标记 `private` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-397">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="2cfc9-398">`Pragma: no-cache`如果标头不存在，则标头不得出现 `Cache-Control` ，因为标头会 `Cache-Control` `Pragma` 在存在时覆盖标头。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-398">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="2cfc9-399">`Set-Cookie`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-399">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="2cfc9-400">`Vary`标头参数必须有效并且不等于 `*` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-400">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="2cfc9-401">`Content-Length`标头值（如果已设置）必须与响应正文的大小匹配。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-401">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="2cfc9-402"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>不使用。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-402">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="2cfc9-403">`Expires`标头和 `max-age` 和缓存指令指定的响应不能过时 `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-403">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="2cfc9-404">响应缓冲必须成功。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-404">Response buffering must be successful.</span></span> <span data-ttu-id="2cfc9-405">响应的大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-405">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="2cfc9-406">响应的正文大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-406">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="2cfc9-407">必须根据[RFC 7234](https://tools.ietf.org/html/rfc7234)规范来缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-407">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="2cfc9-408">例如， `no-store` 指令在请求或响应标头字段中不得存在。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-408">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="2cfc9-409">有关详细信息，请参阅*第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234)的缓存中。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-409">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="2cfc9-410">用于生成安全令牌以防止跨站点请求伪造（CSRF）攻击的防伪系统将 `Cache-Control` 和 `Pragma` 标头设置为， `no-cache` 以便不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-410">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="2cfc9-411">有关如何为 HTML 窗体元素禁用防伪标记的信息，请参阅 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="2cfc9-411">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2cfc9-412">其他资源</span><span class="sxs-lookup"><span data-stu-id="2cfc9-412">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
