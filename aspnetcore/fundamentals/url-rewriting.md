---
<span data-ttu-id="2c057-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-102">'Blazor'</span></span>
- <span data-ttu-id="2c057-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-103">'Identity'</span></span>
- <span data-ttu-id="2c057-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-105">'Razor'</span></span>
- <span data-ttu-id="2c057-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="2c057-107">ASP.NET Core 中的 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="2c057-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="2c057-108">作者：[Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="2c057-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2c057-109">本文档介绍 URL 重写并说明如何在 ASP.NET Core 应用中使用 URL 重写中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="2c057-110">URL 重写是根据一个或多个预定义规则修改请求 URL 的行为。</span><span class="sxs-lookup"><span data-stu-id="2c057-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="2c057-111">URL 重写会在资源位置和地址之间创建一个抽象，使位置和地址不紧密相连。</span><span class="sxs-lookup"><span data-stu-id="2c057-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="2c057-112">在以下几种方案中，URL 重写很有价值：</span><span class="sxs-lookup"><span data-stu-id="2c057-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="2c057-113">暂时或永久移动或替换服务器资源，并维护这些资源的稳定定位符。</span><span class="sxs-lookup"><span data-stu-id="2c057-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="2c057-114">拆分在不同应用或同一应用的不同区域中处理的请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="2c057-115">删除、添加或重新组织传入请求上的 URL 段。</span><span class="sxs-lookup"><span data-stu-id="2c057-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="2c057-116">优化搜索引擎优化 (SEO) 的公共 URL。</span><span class="sxs-lookup"><span data-stu-id="2c057-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="2c057-117">允许使用友好的公共 URL 来帮助访问者预测请求资源后返回的内容。</span><span class="sxs-lookup"><span data-stu-id="2c057-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="2c057-118">将不安全请求重定向到安全终结点。</span><span class="sxs-lookup"><span data-stu-id="2c057-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="2c057-119">防止热链接，外部站点会通过热链接将其他站点的资产链接到其自己的内容，从而利用托管在其他站点上的静态资产。</span><span class="sxs-lookup"><span data-stu-id="2c057-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="2c057-120">URL 重写可能会降低应用的性能。</span><span class="sxs-lookup"><span data-stu-id="2c057-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="2c057-121">如果可行，应限制规则的数量和复杂度。</span><span class="sxs-lookup"><span data-stu-id="2c057-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="2c057-122">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2c057-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="2c057-123">URL 重定向和 URL 重写</span><span class="sxs-lookup"><span data-stu-id="2c057-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="2c057-124">URL 重定向和 URL 重写之间的用词差异很细微，但这对于向客户端提供资源具有重要意义 。</span><span class="sxs-lookup"><span data-stu-id="2c057-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="2c057-125">ASP.NET Core 的 URL 重写中间件能够满足两者的需求。</span><span class="sxs-lookup"><span data-stu-id="2c057-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="2c057-126">URL 重定向涉及客户端操作，指示客户端访问与客户端最初请求地址不同的资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="2c057-127">这需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="2c057-127">This requires a round trip to the server.</span></span> <span data-ttu-id="2c057-128">客户端对资源发出新请求时，返回客户端的重定向 URL 会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="2c057-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="2c057-129">如果 `/resource` 被重定向到 `/different-resource`，则服务器作出响应，指示客户端应在 `/different-resource` 获取资源，所提供的状态代码指示重定向是临时的还是永久的。</span><span class="sxs-lookup"><span data-stu-id="2c057-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 服务终结点已暂时从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="2c057-135">将请求重定向到不同 URL 时，通过使用响应指定状态代码来指示重定向是永久还是临时：</span><span class="sxs-lookup"><span data-stu-id="2c057-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="2c057-136">如果资源有一个新的永久性 URL，并且你希望指示客户端所有将来的资源请求都使用新 URL，则使用“301 (永久移动)”状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="2c057-137">收到 301 状态代码时，客户端可能会缓存响应并重用这段代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="2c057-138">“302 (找到)”状态代码用于后列情况：重定向操作是临时的或通常会发生变化。</span><span class="sxs-lookup"><span data-stu-id="2c057-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="2c057-139">302 状态代码向客户端指示不存储 URL 并在将来使用。</span><span class="sxs-lookup"><span data-stu-id="2c057-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="2c057-140">有关状态代码的详细信息，请参阅 [RFC 2616：状态代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="2c057-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="2c057-141">URL 重写是服务器端操作，它从与客户端请求的资源地址不同的资源地址提供资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="2c057-142">重写 URL 不需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="2c057-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="2c057-143">重写的 URL 不会返回客户端，也不会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="2c057-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="2c057-144">如果 `/resource` 重写到 `/different-resource`，服务器会在内部提取并返回 `/different-resource` 处的资源 。</span><span class="sxs-lookup"><span data-stu-id="2c057-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="2c057-145">尽管客户端可能能够检索已重写 URL 处的资源，但是，客户端发出请求并收到响应时，并不知道已重写 URL 处存在的资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 服务终结点已从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="2c057-150">URL 重写示例应用</span><span class="sxs-lookup"><span data-stu-id="2c057-150">URL rewriting sample app</span></span>

<span data-ttu-id="2c057-151">可使用[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)了解 URL 重写中间件的功能。</span><span class="sxs-lookup"><span data-stu-id="2c057-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="2c057-152">该应用程序应用重定向和重写规则，并显示多个方案的重定向或重写的 URL。</span><span class="sxs-lookup"><span data-stu-id="2c057-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="2c057-153">何时使用 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="2c057-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="2c057-154">如果无法使用以下方法，请使用 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="2c057-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="2c057-155">在 Windows Server 上使用带 IIS 的 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="2c057-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="2c057-156">在 Apache 服务器上使用 Apache mod_rewrite 模块</span><span class="sxs-lookup"><span data-stu-id="2c057-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="2c057-157">Nginx 上的 URL 重写</span><span class="sxs-lookup"><span data-stu-id="2c057-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="2c057-158">此外，如果应用程序在 [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（旧称 WebListener）上托管，请使用中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="2c057-159">使用 IIS、Apache 和 Nginx 中的基于服务器的 URL 重写技术的主要原因：</span><span class="sxs-lookup"><span data-stu-id="2c057-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="2c057-160">中间件不支持这些模块的完整功能。</span><span class="sxs-lookup"><span data-stu-id="2c057-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="2c057-161">服务器模块的一些功能不适用于 ASP.NET Core 项目，例如 IIS 重写模块的 `IsFile` 和 `IsDirectory` 约束。</span><span class="sxs-lookup"><span data-stu-id="2c057-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="2c057-162">在这些情况下，请改为使用中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="2c057-163">中间件性能与模块性能不匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="2c057-164">基准测试是确定哪种方法会最大程度降低性能或降低的性能是否可忽略不计的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="2c057-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="2c057-165">Package</span><span class="sxs-lookup"><span data-stu-id="2c057-165">Package</span></span>

<span data-ttu-id="2c057-166">URL 重写中间件由 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 包提供，该包隐式包含在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="2c057-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="2c057-167">扩展和选项</span><span class="sxs-lookup"><span data-stu-id="2c057-167">Extension and options</span></span>

<span data-ttu-id="2c057-168">通过使用扩展方法为每条重写规则创建 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 类的实例，建立 URL 重写和重写定向规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="2c057-169">按所需的处理顺序链接多个规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="2c057-170">使用 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 将 `RewriteOptions` 添加到请求管道时，它会被传递到 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="2c057-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="2c057-171">将非 www 重定向到 www</span><span class="sxs-lookup"><span data-stu-id="2c057-171">Redirect non-www to www</span></span>

<span data-ttu-id="2c057-172">三个选项允许应用将非 `www` 重新定向到 `www`：</span><span class="sxs-lookup"><span data-stu-id="2c057-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="2c057-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>：如果请求是非 `www`，则将请求永久重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="2c057-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="2c057-174">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="2c057-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="2c057-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>：如果传入请求是非 `www`，则将请求重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="2c057-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="2c057-176">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="2c057-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="2c057-177">重载允许提供响应状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="2c057-178">使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 类的字段实现状态代码分配。</span><span class="sxs-lookup"><span data-stu-id="2c057-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="2c057-179">URL 重定向</span><span class="sxs-lookup"><span data-stu-id="2c057-179">URL redirect</span></span>

<span data-ttu-id="2c057-180">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 将请求重定向。</span><span class="sxs-lookup"><span data-stu-id="2c057-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="2c057-181">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2c057-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="2c057-182">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="2c057-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="2c057-183">第三个参数（如有）指定状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="2c057-184">如不指定状态代码，则状态代码默认为“302 (已找到)”，指示资源暂时移动或替换。</span><span class="sxs-lookup"><span data-stu-id="2c057-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="2c057-185">在启用了开发人员工具的浏览器中，向路径为 `/redirect-rule/1234/5678` 的示例应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="2c057-186">正则表达式匹配 `redirect-rule/(.*)` 上的请求路径，且该路径会被 `/redirected/1234/5678` 替代。</span><span class="sxs-lookup"><span data-stu-id="2c057-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="2c057-187">重定向 URL 以“302 (已找到)”状态代码发回客户端。</span><span class="sxs-lookup"><span data-stu-id="2c057-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="2c057-188">浏览器会在浏览器地址栏中出现的重定向 URL 上发出新请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="2c057-189">由于示例应用中的规则都不匹配重定向 URL：</span><span class="sxs-lookup"><span data-stu-id="2c057-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="2c057-190">第二个请求从应用程序收到“200 (正常)”响应。</span><span class="sxs-lookup"><span data-stu-id="2c057-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="2c057-191">响应正文显示了重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="2c057-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="2c057-192">重定向 URL 时，系统将向服务器进行一次往返。</span><span class="sxs-lookup"><span data-stu-id="2c057-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="2c057-193">建立重定向规则时务必小心。</span><span class="sxs-lookup"><span data-stu-id="2c057-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="2c057-194">系统会根据应用的每个请求（包括重定向后的请求）对重定向规则进行评估。</span><span class="sxs-lookup"><span data-stu-id="2c057-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="2c057-195">很容易便会意外创建无限重定向循环。</span><span class="sxs-lookup"><span data-stu-id="2c057-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="2c057-196">原始请求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="2c057-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="2c057-198">括号内的表达式部分称为“捕获组”。</span><span class="sxs-lookup"><span data-stu-id="2c057-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="2c057-199">表达式的点 (`.`) 表示匹配任何字符。</span><span class="sxs-lookup"><span data-stu-id="2c057-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="2c057-200">星号 (`*`) 表示零次或多次匹配前面的字符。</span><span class="sxs-lookup"><span data-stu-id="2c057-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="2c057-201">因此，URL 的最后两个路径段 `1234/5678` 由捕获组 `(.*)` 捕获。</span><span class="sxs-lookup"><span data-stu-id="2c057-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="2c057-202">在请求 URL 中提供的位于 `redirect-rule/` 之后的任何值均由此单个捕获组捕获。</span><span class="sxs-lookup"><span data-stu-id="2c057-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="2c057-203">在替换字符串中，将捕获组注入带有美元符号 (`$`)、后跟捕获序列号的字符串中。</span><span class="sxs-lookup"><span data-stu-id="2c057-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="2c057-204">获取的第一个捕获组值为 `$1`，第二个为 `$2`，并且正则表达式中的其他捕获组值将依次继续排列。</span><span class="sxs-lookup"><span data-stu-id="2c057-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="2c057-205">示例应用的重定向规则正则表达式中只有一个捕获组，因此替换字符串中只有一个注入组，即 `$1`。</span><span class="sxs-lookup"><span data-stu-id="2c057-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="2c057-206">如果应用此规则，URL 将变为 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="2c057-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="2c057-207">URL 重定向到安全的终结点</span><span class="sxs-lookup"><span data-stu-id="2c057-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="2c057-208">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 将 HTTP 请求重定向到采用 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="2c057-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="2c057-209">如不提供状态代码，则中间件默认为“302(已找到)”。</span><span class="sxs-lookup"><span data-stu-id="2c057-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="2c057-210">如果不提供端口：</span><span class="sxs-lookup"><span data-stu-id="2c057-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="2c057-211">中间件默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="2c057-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="2c057-212">方案更改为 `https`（HTTPS 协议），客户端访问端口 443 上的资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="2c057-213">下面的示例演示如何将状态代码设置为“301(永久移动)”并将端口更改为 5001。</span><span class="sxs-lookup"><span data-stu-id="2c057-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="2c057-214">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 将不安全的请求重定向到端口 443 上的采用安全 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="2c057-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="2c057-215">中间件将状态代码设置为“301 (永久移动)”。</span><span class="sxs-lookup"><span data-stu-id="2c057-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="2c057-216">当重定向到安全的终结点并且不需要其他重定向规则时，建议使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="2c057-217">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl#require-https)主题。</span><span class="sxs-lookup"><span data-stu-id="2c057-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="2c057-218">示例应用能够演示如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="2c057-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="2c057-219">将扩展方法添加到 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="2c057-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="2c057-220">在任何 URL 上向应用发出不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="2c057-221">消除自签名证书不受信任的浏览器安全警告，或创建例外以信任证书。</span><span class="sxs-lookup"><span data-stu-id="2c057-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="2c057-222">使用 `AddRedirectToHttps(301, 5001)` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="2c057-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="2c057-224">使用 `AddRedirectToHttpsPermanent` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="2c057-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="2c057-226">URL 重写</span><span class="sxs-lookup"><span data-stu-id="2c057-226">URL rewrite</span></span>

<span data-ttu-id="2c057-227">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 创建重写 URL 的规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="2c057-228">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2c057-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="2c057-229">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="2c057-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="2c057-230">第三个参数 `skipRemainingRules: {true|false}` 指示如果当前规则适用，中间件是否要跳过其他重写规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="2c057-231">原始请求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="2c057-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="2c057-233">表达式开头的脱字号 (`^`) 意味着匹配从 URL 路径的开头处开始。</span><span class="sxs-lookup"><span data-stu-id="2c057-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="2c057-234">在前面的重定向规则 `redirect-rule/(.*)` 的示例中，正则表达式的开头没有脱字号 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="2c057-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="2c057-235">因此，路径中 `redirect-rule/` 之前的任何字符都可能成功匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="2c057-236">路径</span><span class="sxs-lookup"><span data-stu-id="2c057-236">Path</span></span>                               | <span data-ttu-id="2c057-237">匹配</span><span class="sxs-lookup"><span data-stu-id="2c057-237">Match</span></span> |
| ---
<span data-ttu-id="2c057-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-239">'Blazor'</span></span>
- <span data-ttu-id="2c057-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-240">'Identity'</span></span>
- <span data-ttu-id="2c057-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-242">'Razor'</span></span>
- <span data-ttu-id="2c057-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-245">'Blazor'</span></span>
- <span data-ttu-id="2c057-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-246">'Identity'</span></span>
- <span data-ttu-id="2c057-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-248">'Razor'</span></span>
- <span data-ttu-id="2c057-249">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-251">'Blazor'</span></span>
- <span data-ttu-id="2c057-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-252">'Identity'</span></span>
- <span data-ttu-id="2c057-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-254">'Razor'</span></span>
- <span data-ttu-id="2c057-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-257">'Blazor'</span></span>
- <span data-ttu-id="2c057-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-258">'Identity'</span></span>
- <span data-ttu-id="2c057-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-260">'Razor'</span></span>
- <span data-ttu-id="2c057-261">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-263">'Blazor'</span></span>
- <span data-ttu-id="2c057-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-264">'Identity'</span></span>
- <span data-ttu-id="2c057-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-266">'Razor'</span></span>
- <span data-ttu-id="2c057-267">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-269">'Blazor'</span></span>
- <span data-ttu-id="2c057-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-270">'Identity'</span></span>
- <span data-ttu-id="2c057-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-272">'Razor'</span></span>
- <span data-ttu-id="2c057-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-275">'Blazor'</span></span>
- <span data-ttu-id="2c057-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-276">'Identity'</span></span>
- <span data-ttu-id="2c057-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-278">'Razor'</span></span>
- <span data-ttu-id="2c057-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-281">'Blazor'</span></span>
- <span data-ttu-id="2c057-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-282">'Identity'</span></span>
- <span data-ttu-id="2c057-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-284">'Razor'</span></span>
- <span data-ttu-id="2c057-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-287">'Blazor'</span></span>
- <span data-ttu-id="2c057-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-288">'Identity'</span></span>
- <span data-ttu-id="2c057-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-290">'Razor'</span></span>
- <span data-ttu-id="2c057-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-293">'Blazor'</span></span>
- <span data-ttu-id="2c057-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-294">'Identity'</span></span>
- <span data-ttu-id="2c057-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-296">'Razor'</span></span>
- <span data-ttu-id="2c057-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-299">'Blazor'</span></span>
- <span data-ttu-id="2c057-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-300">'Identity'</span></span>
- <span data-ttu-id="2c057-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-302">'Razor'</span></span>
- <span data-ttu-id="2c057-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-305">'Blazor'</span></span>
- <span data-ttu-id="2c057-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-306">'Identity'</span></span>
- <span data-ttu-id="2c057-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-308">'Razor'</span></span>
- <span data-ttu-id="2c057-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-311">'Blazor'</span></span>
- <span data-ttu-id="2c057-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-312">'Identity'</span></span>
- <span data-ttu-id="2c057-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-314">'Razor'</span></span>
- <span data-ttu-id="2c057-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-317">'Blazor'</span></span>
- <span data-ttu-id="2c057-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-318">'Identity'</span></span>
- <span data-ttu-id="2c057-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-320">'Razor'</span></span>
- <span data-ttu-id="2c057-321">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-323">'Blazor'</span></span>
- <span data-ttu-id="2c057-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-324">'Identity'</span></span>
- <span data-ttu-id="2c057-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-326">'Razor'</span></span>
- <span data-ttu-id="2c057-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-327">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | 是   | | `/my-cool-redirect-rule/1234/5678` | 是   | | `/anotherredirect-rule/1234/5678`  | 是   |</span><span class="sxs-lookup"><span data-stu-id="2c057-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="2c057-329">重写规则 `^rewrite-rule/(\d+)/(\d+)` 只能与以 `rewrite-rule/` 开头的路径匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="2c057-330">注意下表中的匹配差异。</span><span class="sxs-lookup"><span data-stu-id="2c057-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="2c057-331">路径</span><span class="sxs-lookup"><span data-stu-id="2c057-331">Path</span></span>                              | <span data-ttu-id="2c057-332">匹配</span><span class="sxs-lookup"><span data-stu-id="2c057-332">Match</span></span> |
| ---
<span data-ttu-id="2c057-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-334">'Blazor'</span></span>
- <span data-ttu-id="2c057-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-335">'Identity'</span></span>
- <span data-ttu-id="2c057-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-337">'Razor'</span></span>
- <span data-ttu-id="2c057-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-340">'Blazor'</span></span>
- <span data-ttu-id="2c057-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-341">'Identity'</span></span>
- <span data-ttu-id="2c057-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-343">'Razor'</span></span>
- <span data-ttu-id="2c057-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-346">'Blazor'</span></span>
- <span data-ttu-id="2c057-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-347">'Identity'</span></span>
- <span data-ttu-id="2c057-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-349">'Razor'</span></span>
- <span data-ttu-id="2c057-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-352">'Blazor'</span></span>
- <span data-ttu-id="2c057-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-353">'Identity'</span></span>
- <span data-ttu-id="2c057-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-355">'Razor'</span></span>
- <span data-ttu-id="2c057-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-358">'Blazor'</span></span>
- <span data-ttu-id="2c057-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-359">'Identity'</span></span>
- <span data-ttu-id="2c057-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-361">'Razor'</span></span>
- <span data-ttu-id="2c057-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-364">'Blazor'</span></span>
- <span data-ttu-id="2c057-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-365">'Identity'</span></span>
- <span data-ttu-id="2c057-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-367">'Razor'</span></span>
- <span data-ttu-id="2c057-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-370">'Blazor'</span></span>
- <span data-ttu-id="2c057-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-371">'Identity'</span></span>
- <span data-ttu-id="2c057-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-373">'Razor'</span></span>
- <span data-ttu-id="2c057-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-376">'Blazor'</span></span>
- <span data-ttu-id="2c057-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-377">'Identity'</span></span>
- <span data-ttu-id="2c057-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-379">'Razor'</span></span>
- <span data-ttu-id="2c057-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-382">'Blazor'</span></span>
- <span data-ttu-id="2c057-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-383">'Identity'</span></span>
- <span data-ttu-id="2c057-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-385">'Razor'</span></span>
- <span data-ttu-id="2c057-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-388">'Blazor'</span></span>
- <span data-ttu-id="2c057-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-389">'Identity'</span></span>
- <span data-ttu-id="2c057-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-391">'Razor'</span></span>
- <span data-ttu-id="2c057-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-394">'Blazor'</span></span>
- <span data-ttu-id="2c057-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-395">'Identity'</span></span>
- <span data-ttu-id="2c057-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-397">'Razor'</span></span>
- <span data-ttu-id="2c057-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-400">'Blazor'</span></span>
- <span data-ttu-id="2c057-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-401">'Identity'</span></span>
- <span data-ttu-id="2c057-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-403">'Razor'</span></span>
- <span data-ttu-id="2c057-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-406">'Blazor'</span></span>
- <span data-ttu-id="2c057-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-407">'Identity'</span></span>
- <span data-ttu-id="2c057-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-409">'Razor'</span></span>
- <span data-ttu-id="2c057-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-412">'Blazor'</span></span>
- <span data-ttu-id="2c057-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-413">'Identity'</span></span>
- <span data-ttu-id="2c057-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-415">'Razor'</span></span>
- <span data-ttu-id="2c057-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-416">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | 是   | | `/my-cool-rewrite-rule/1234/5678` | 否    | | `/anotherrewrite-rule/1234/5678`  | 否    |</span><span class="sxs-lookup"><span data-stu-id="2c057-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="2c057-418">在表达式的 `^rewrite-rule/` 部分之后，有两个捕获组 `(\d+)/(\d+)`。</span><span class="sxs-lookup"><span data-stu-id="2c057-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="2c057-419">`\d` 表示与数字匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="2c057-420">加号 (`+`) 表示与前面的一个或多个字符匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="2c057-421">因此，URL 必须包含数字加正斜杠加另一个数字的形式。</span><span class="sxs-lookup"><span data-stu-id="2c057-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="2c057-422">这些捕获组以 `$1` 和 `$2` 的形式注入重写 URL 中。</span><span class="sxs-lookup"><span data-stu-id="2c057-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="2c057-423">重写规则替换字符串将捕获组放入查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="2c057-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="2c057-424">重写 `/rewrite-rule/1234/5678` 的请求路径，获取 `/rewritten?var1=1234&var2=5678` 处的资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="2c057-425">如果原始请求中存在查询字符串，则重写 URL 时会保留此字符串。</span><span class="sxs-lookup"><span data-stu-id="2c057-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="2c057-426">无需往返服务器来获取资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="2c057-427">如果资源存在，系统会提取资源并以“200（正常）”状态代码返回给客户端。</span><span class="sxs-lookup"><span data-stu-id="2c057-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="2c057-428">因为客户端不会被重定向，所以浏览器地址栏中的 URL 不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="2c057-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="2c057-429">客户端无法检测到服务器上发生的 URL 重写操作。</span><span class="sxs-lookup"><span data-stu-id="2c057-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2c057-430">尽可能使用 `skipRemainingRules: true`，因为匹配规则在计算上很昂贵并且增加了应用响应时间。</span><span class="sxs-lookup"><span data-stu-id="2c057-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="2c057-431">对于最快的应用响应：</span><span class="sxs-lookup"><span data-stu-id="2c057-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="2c057-432">按照从最频繁匹配的规则到最不频繁匹配的规则排列重写规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="2c057-433">如果出现匹配项且无需处理任何其他规则，则跳过剩余规则的处理。</span><span class="sxs-lookup"><span data-stu-id="2c057-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="2c057-434">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="2c057-434">Apache mod_rewrite</span></span>

<span data-ttu-id="2c057-435">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 应用 Apache mod_rewrite 规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="2c057-436">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="2c057-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="2c057-437">有关 mod_rewrite 规则的详细信息和示例，请参阅 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="2c057-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="2c057-438"><xref:System.IO.StreamReader> 用于读取 ApacheModRewrite.txt 规则文件中的规则：</span><span class="sxs-lookup"><span data-stu-id="2c057-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="2c057-439">示例应用将请求从 `/apache-mod-rules-redirect/(.\*)` 重定向到 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="2c057-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="2c057-440">响应状态代码为“302 (已找到)”。</span><span class="sxs-lookup"><span data-stu-id="2c057-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="2c057-441">原始请求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="2c057-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="2c057-443">中间件支持下列 Apache mod_rewrite 服务器变量：</span><span class="sxs-lookup"><span data-stu-id="2c057-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="2c057-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="2c057-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="2c057-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="2c057-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="2c057-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="2c057-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="2c057-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="2c057-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="2c057-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="2c057-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="2c057-449">HTTP_HOST</span></span>
* <span data-ttu-id="2c057-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="2c057-450">HTTP_REFERER</span></span>
* <span data-ttu-id="2c057-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="2c057-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="2c057-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2c057-452">HTTPS</span></span>
* <span data-ttu-id="2c057-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="2c057-453">IPV6</span></span>
* <span data-ttu-id="2c057-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="2c057-454">QUERY_STRING</span></span>
* <span data-ttu-id="2c057-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="2c057-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="2c057-456">REMOTE_PORT</span></span>
* <span data-ttu-id="2c057-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="2c057-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="2c057-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="2c057-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="2c057-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="2c057-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="2c057-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="2c057-460">REQUEST_URI</span></span>
* <span data-ttu-id="2c057-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="2c057-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="2c057-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-462">SERVER_ADDR</span></span>
* <span data-ttu-id="2c057-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="2c057-463">SERVER_PORT</span></span>
* <span data-ttu-id="2c057-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="2c057-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="2c057-465">TIME</span><span class="sxs-lookup"><span data-stu-id="2c057-465">TIME</span></span>
* <span data-ttu-id="2c057-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="2c057-466">TIME_DAY</span></span>
* <span data-ttu-id="2c057-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="2c057-467">TIME_HOUR</span></span>
* <span data-ttu-id="2c057-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="2c057-468">TIME_MIN</span></span>
* <span data-ttu-id="2c057-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="2c057-469">TIME_MON</span></span>
* <span data-ttu-id="2c057-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="2c057-470">TIME_SEC</span></span>
* <span data-ttu-id="2c057-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="2c057-471">TIME_WDAY</span></span>
* <span data-ttu-id="2c057-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="2c057-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="2c057-473">IIS URL 重写模块规则</span><span class="sxs-lookup"><span data-stu-id="2c057-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="2c057-474">若要使用适用于 IIS URL 重写模块的同一规则集，使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="2c057-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="2c057-475">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="2c057-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="2c057-476">当在 Windows Server IIS 上运行时，请勿指示中间件使用应用的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2c057-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="2c057-477">使用 IIS 时，应将这些规则存储在应用的 web.config 文件之外，以避免与 IIS 重写模块发生冲突。</span><span class="sxs-lookup"><span data-stu-id="2c057-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="2c057-478">有关 IIS URL 重写模块规则的详细信息和示例，请参阅 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)（使用 URL 重写模块 2.0）和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）。</span><span class="sxs-lookup"><span data-stu-id="2c057-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="2c057-479"><xref:System.IO.StreamReader> 用于读取 IISUrlRewrite.xml 规则文件中的规则：</span><span class="sxs-lookup"><span data-stu-id="2c057-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="2c057-480">示例应用将请求从 `/iis-rules-rewrite/(.*)` 重写为 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="2c057-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="2c057-481">以“200 (正常)”状态代码作为响应发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="2c057-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="2c057-482">原始请求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="2c057-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="2c057-484">如果有配置了服务器级别规则（可对应用产生不利影响）的活动 IIS 重写模块，则可禁用应用的 IIS 重写模块。</span><span class="sxs-lookup"><span data-stu-id="2c057-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="2c057-485">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="2c057-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="2c057-486">不支持的功能</span><span class="sxs-lookup"><span data-stu-id="2c057-486">Unsupported features</span></span>

<span data-ttu-id="2c057-487">与 ASP.NET Core 2.x 一同发布的中间件不支持以下 IIS URL 重写模块功能：</span><span class="sxs-lookup"><span data-stu-id="2c057-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="2c057-488">出站规则</span><span class="sxs-lookup"><span data-stu-id="2c057-488">Outbound Rules</span></span>
* <span data-ttu-id="2c057-489">自定义服务器变量</span><span class="sxs-lookup"><span data-stu-id="2c057-489">Custom Server Variables</span></span>
* <span data-ttu-id="2c057-490">通配符</span><span class="sxs-lookup"><span data-stu-id="2c057-490">Wildcards</span></span>
* <span data-ttu-id="2c057-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="2c057-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="2c057-492">受支持的服务器变量</span><span class="sxs-lookup"><span data-stu-id="2c057-492">Supported server variables</span></span>

<span data-ttu-id="2c057-493">中间件支持下列 IIS URL 重写模块服务器变量：</span><span class="sxs-lookup"><span data-stu-id="2c057-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="2c057-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="2c057-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="2c057-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="2c057-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="2c057-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="2c057-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="2c057-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="2c057-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="2c057-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="2c057-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="2c057-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="2c057-499">HTTP_HOST</span></span>
* <span data-ttu-id="2c057-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="2c057-500">HTTP_REFERER</span></span>
* <span data-ttu-id="2c057-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="2c057-501">HTTP_URL</span></span>
* <span data-ttu-id="2c057-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="2c057-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="2c057-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2c057-503">HTTPS</span></span>
* <span data-ttu-id="2c057-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="2c057-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="2c057-505">QUERY_STRING</span></span>
* <span data-ttu-id="2c057-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="2c057-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="2c057-507">REMOTE_PORT</span></span>
* <span data-ttu-id="2c057-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="2c057-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="2c057-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="2c057-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="2c057-510">也可通过 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 获取 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="2c057-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="2c057-511">这种方法可为重写规则文件的位置提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="2c057-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="2c057-512">请确保将重写规则文件部署到所提供路径的服务器中。</span><span class="sxs-lookup"><span data-stu-id="2c057-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="2c057-513">基于方法的规则</span><span class="sxs-lookup"><span data-stu-id="2c057-513">Method-based rule</span></span>

<span data-ttu-id="2c057-514">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在方法中实现自己的规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="2c057-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="2c057-515">`Add` 公开 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，这使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用于方法中。</span><span class="sxs-lookup"><span data-stu-id="2c057-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="2c057-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) 决定如何处理其他管道进程。</span><span class="sxs-lookup"><span data-stu-id="2c057-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="2c057-517">将值设置为下表中的 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 字段之一。</span><span class="sxs-lookup"><span data-stu-id="2c057-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="2c057-518">操作</span><span class="sxs-lookup"><span data-stu-id="2c057-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="2c057-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-520">'Blazor'</span></span>
- <span data-ttu-id="2c057-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-521">'Identity'</span></span>
- <span data-ttu-id="2c057-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-523">'Razor'</span></span>
- <span data-ttu-id="2c057-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-526">'Blazor'</span></span>
- <span data-ttu-id="2c057-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-527">'Identity'</span></span>
- <span data-ttu-id="2c057-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-529">'Razor'</span></span>
- <span data-ttu-id="2c057-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-532">'Blazor'</span></span>
- <span data-ttu-id="2c057-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-533">'Identity'</span></span>
- <span data-ttu-id="2c057-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-535">'Razor'</span></span>
- <span data-ttu-id="2c057-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-538">'Blazor'</span></span>
- <span data-ttu-id="2c057-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-539">'Identity'</span></span>
- <span data-ttu-id="2c057-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-541">'Razor'</span></span>
- <span data-ttu-id="2c057-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-544">'Blazor'</span></span>
- <span data-ttu-id="2c057-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-545">'Identity'</span></span>
- <span data-ttu-id="2c057-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-547">'Razor'</span></span>
- <span data-ttu-id="2c057-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-550">'Blazor'</span></span>
- <span data-ttu-id="2c057-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-551">'Identity'</span></span>
- <span data-ttu-id="2c057-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-553">'Razor'</span></span>
- <span data-ttu-id="2c057-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-556">'Blazor'</span></span>
- <span data-ttu-id="2c057-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-557">'Identity'</span></span>
- <span data-ttu-id="2c057-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-559">'Razor'</span></span>
- <span data-ttu-id="2c057-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-562">'Blazor'</span></span>
- <span data-ttu-id="2c057-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-563">'Identity'</span></span>
- <span data-ttu-id="2c057-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-565">'Razor'</span></span>
- <span data-ttu-id="2c057-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-568">'Blazor'</span></span>
- <span data-ttu-id="2c057-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-569">'Identity'</span></span>
- <span data-ttu-id="2c057-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-571">'Razor'</span></span>
- <span data-ttu-id="2c057-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-574">'Blazor'</span></span>
- <span data-ttu-id="2c057-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-575">'Identity'</span></span>
- <span data-ttu-id="2c057-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-577">'Razor'</span></span>
- <span data-ttu-id="2c057-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-580">'Blazor'</span></span>
- <span data-ttu-id="2c057-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-581">'Identity'</span></span>
- <span data-ttu-id="2c057-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-583">'Razor'</span></span>
- <span data-ttu-id="2c057-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-586">'Blazor'</span></span>
- <span data-ttu-id="2c057-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-587">'Identity'</span></span>
- <span data-ttu-id="2c057-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-589">'Razor'</span></span>
- <span data-ttu-id="2c057-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-592">'Blazor'</span></span>
- <span data-ttu-id="2c057-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-593">'Identity'</span></span>
- <span data-ttu-id="2c057-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-595">'Razor'</span></span>
- <span data-ttu-id="2c057-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-598">'Blazor'</span></span>
- <span data-ttu-id="2c057-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-599">'Identity'</span></span>
- <span data-ttu-id="2c057-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-601">'Razor'</span></span>
- <span data-ttu-id="2c057-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-604">'Blazor'</span></span>
- <span data-ttu-id="2c057-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-605">'Identity'</span></span>
- <span data-ttu-id="2c057-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-607">'Razor'</span></span>
- <span data-ttu-id="2c057-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-610">'Blazor'</span></span>
- <span data-ttu-id="2c057-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-611">'Identity'</span></span>
- <span data-ttu-id="2c057-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-613">'Razor'</span></span>
- <span data-ttu-id="2c057-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-614">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-616">'Blazor'</span></span>
- <span data-ttu-id="2c057-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-617">'Identity'</span></span>
- <span data-ttu-id="2c057-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-619">'Razor'</span></span>
- <span data-ttu-id="2c057-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-622">'Blazor'</span></span>
- <span data-ttu-id="2c057-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-623">'Identity'</span></span>
- <span data-ttu-id="2c057-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-625">'Razor'</span></span>
- <span data-ttu-id="2c057-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-628">'Blazor'</span></span>
- <span data-ttu-id="2c057-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-629">'Identity'</span></span>
- <span data-ttu-id="2c057-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-631">'Razor'</span></span>
- <span data-ttu-id="2c057-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-634">'Blazor'</span></span>
- <span data-ttu-id="2c057-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-635">'Identity'</span></span>
- <span data-ttu-id="2c057-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-637">'Razor'</span></span>
- <span data-ttu-id="2c057-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-640">'Blazor'</span></span>
- <span data-ttu-id="2c057-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-641">'Identity'</span></span>
- <span data-ttu-id="2c057-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-643">'Razor'</span></span>
- <span data-ttu-id="2c057-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-646">'Blazor'</span></span>
- <span data-ttu-id="2c057-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-647">'Identity'</span></span>
- <span data-ttu-id="2c057-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-649">'Razor'</span></span>
- <span data-ttu-id="2c057-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-652">'Blazor'</span></span>
- <span data-ttu-id="2c057-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-653">'Identity'</span></span>
- <span data-ttu-id="2c057-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-655">'Razor'</span></span>
- <span data-ttu-id="2c057-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-658">'Blazor'</span></span>
- <span data-ttu-id="2c057-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-659">'Identity'</span></span>
- <span data-ttu-id="2c057-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-661">'Razor'</span></span>
- <span data-ttu-id="2c057-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-664">'Blazor'</span></span>
- <span data-ttu-id="2c057-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-665">'Identity'</span></span>
- <span data-ttu-id="2c057-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-667">'Razor'</span></span>
- <span data-ttu-id="2c057-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-670">'Blazor'</span></span>
- <span data-ttu-id="2c057-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-671">'Identity'</span></span>
- <span data-ttu-id="2c057-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-673">'Razor'</span></span>
- <span data-ttu-id="2c057-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-676">'Blazor'</span></span>
- <span data-ttu-id="2c057-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-677">'Identity'</span></span>
- <span data-ttu-id="2c057-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-679">'Razor'</span></span>
- <span data-ttu-id="2c057-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-682">'Blazor'</span></span>
- <span data-ttu-id="2c057-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-683">'Identity'</span></span>
- <span data-ttu-id="2c057-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-685">'Razor'</span></span>
- <span data-ttu-id="2c057-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-688">'Blazor'</span></span>
- <span data-ttu-id="2c057-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-689">'Identity'</span></span>
- <span data-ttu-id="2c057-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-691">'Razor'</span></span>
- <span data-ttu-id="2c057-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-694">'Blazor'</span></span>
- <span data-ttu-id="2c057-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-695">'Identity'</span></span>
- <span data-ttu-id="2c057-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-697">'Razor'</span></span>
- <span data-ttu-id="2c057-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-700">'Blazor'</span></span>
- <span data-ttu-id="2c057-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-701">'Identity'</span></span>
- <span data-ttu-id="2c057-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-703">'Razor'</span></span>
- <span data-ttu-id="2c057-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-706">'Blazor'</span></span>
- <span data-ttu-id="2c057-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-707">'Identity'</span></span>
- <span data-ttu-id="2c057-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-709">'Razor'</span></span>
- <span data-ttu-id="2c057-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-712">'Blazor'</span></span>
- <span data-ttu-id="2c057-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-713">'Identity'</span></span>
- <span data-ttu-id="2c057-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-715">'Razor'</span></span>
- <span data-ttu-id="2c057-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-718">'Blazor'</span></span>
- <span data-ttu-id="2c057-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-719">'Identity'</span></span>
- <span data-ttu-id="2c057-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-721">'Razor'</span></span>
- <span data-ttu-id="2c057-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-724">'Blazor'</span></span>
- <span data-ttu-id="2c057-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-725">'Identity'</span></span>
- <span data-ttu-id="2c057-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-727">'Razor'</span></span>
- <span data-ttu-id="2c057-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-730">'Blazor'</span></span>
- <span data-ttu-id="2c057-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-731">'Identity'</span></span>
- <span data-ttu-id="2c057-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-733">'Razor'</span></span>
- <span data-ttu-id="2c057-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-736">'Blazor'</span></span>
- <span data-ttu-id="2c057-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-737">'Identity'</span></span>
- <span data-ttu-id="2c057-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-739">'Razor'</span></span>
- <span data-ttu-id="2c057-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-742">'Blazor'</span></span>
- <span data-ttu-id="2c057-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-743">'Identity'</span></span>
- <span data-ttu-id="2c057-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-745">'Razor'</span></span>
- <span data-ttu-id="2c057-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-748">'Blazor'</span></span>
- <span data-ttu-id="2c057-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-749">'Identity'</span></span>
- <span data-ttu-id="2c057-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-751">'Razor'</span></span>
- <span data-ttu-id="2c057-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-754">'Blazor'</span></span>
- <span data-ttu-id="2c057-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-755">'Identity'</span></span>
- <span data-ttu-id="2c057-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-757">'Razor'</span></span>
- <span data-ttu-id="2c057-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-760">'Blazor'</span></span>
- <span data-ttu-id="2c057-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-761">'Identity'</span></span>
- <span data-ttu-id="2c057-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-763">'Razor'</span></span>
- <span data-ttu-id="2c057-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-766">'Blazor'</span></span>
- <span data-ttu-id="2c057-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-767">'Identity'</span></span>
- <span data-ttu-id="2c057-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-769">'Razor'</span></span>
- <span data-ttu-id="2c057-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-772">'Blazor'</span></span>
- <span data-ttu-id="2c057-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-773">'Identity'</span></span>
- <span data-ttu-id="2c057-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-775">'Razor'</span></span>
- <span data-ttu-id="2c057-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-778">'Blazor'</span></span>
- <span data-ttu-id="2c057-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-779">'Identity'</span></span>
- <span data-ttu-id="2c057-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-781">'Razor'</span></span>
- <span data-ttu-id="2c057-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-784">'Blazor'</span></span>
- <span data-ttu-id="2c057-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-785">'Identity'</span></span>
- <span data-ttu-id="2c057-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-787">'Razor'</span></span>
- <span data-ttu-id="2c057-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-790">'Blazor'</span></span>
- <span data-ttu-id="2c057-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-791">'Identity'</span></span>
- <span data-ttu-id="2c057-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-793">'Razor'</span></span>
- <span data-ttu-id="2c057-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-794">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-795">-------------------------------- | | `RuleResult.ContinueRules`（默认）| 继续应用规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="2c057-796">| | `RuleResult.EndResponse`             | 停止应用规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="2c057-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="2c057-797">| | `RuleResult.SkipRemainingRules`      | 停止应用规则并将上下文发送给下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="2c057-798">示例应用演示了如何对以 .xml 结尾的路径的请求进行重定向。</span><span class="sxs-lookup"><span data-stu-id="2c057-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="2c057-799">如果发出针对 `/file.xml` 的请求，请求将重定向到 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="2c057-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="2c057-800">状态代码设置为“301 (永久移动)”。</span><span class="sxs-lookup"><span data-stu-id="2c057-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="2c057-801">当浏览器发出针对 /xmlfiles/file.xml 的新请求后，静态文件中间件会将文件从 wwwroot / xmlfiles 文件夹提供给客户端 。</span><span class="sxs-lookup"><span data-stu-id="2c057-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="2c057-802">对于重定向，请显式设置响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="2c057-803">否则，将会返回“200 (正常)”状态代码，且客户端上不会发生重写。</span><span class="sxs-lookup"><span data-stu-id="2c057-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="2c057-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="2c057-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="2c057-805">此方法还可以重写请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="2c057-806">示例应用演示了如何重写任何文本文件请求的路径以从 wwwroot 文件夹中提供 file.txt 文本文件 。</span><span class="sxs-lookup"><span data-stu-id="2c057-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="2c057-807">静态文件中间件基于更新的请求路径来提供文件：</span><span class="sxs-lookup"><span data-stu-id="2c057-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="2c057-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="2c057-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="2c057-809">基于 IRule 的规则</span><span class="sxs-lookup"><span data-stu-id="2c057-809">IRule-based rule</span></span>

<span data-ttu-id="2c057-810">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在实现 <xref:Microsoft.AspNetCore.Rewrite.IRule> 接口的类中使用规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="2c057-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="2c057-811">与使用基于方法的规则方法相比，`IRule` 提供了更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="2c057-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="2c057-812">实现类可能包含构造函数，可在其中传入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的参数。</span><span class="sxs-lookup"><span data-stu-id="2c057-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="2c057-813">检查示例应用中 `extension` 和 `newPath` 的参数值是否符合多个条件。</span><span class="sxs-lookup"><span data-stu-id="2c057-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="2c057-814">`extension` 须包含一个值，并且该值必须是 .png、.jpg 或 .gif  。</span><span class="sxs-lookup"><span data-stu-id="2c057-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="2c057-815">如果 `newPath` 无效，则会引发 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="2c057-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="2c057-816">如果发出针对 image.png 的请求，请求将重定向到 `/png-images/image.png`。</span><span class="sxs-lookup"><span data-stu-id="2c057-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="2c057-817">如果发出针对 image.png 的请求，请求将重定向到 `/jpg-images/image.jpg`。</span><span class="sxs-lookup"><span data-stu-id="2c057-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="2c057-818">状态代码设置为“301 (永久移动)”，`context.Result` 设置为停止处理规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="2c057-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="2c057-819">原始请求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="2c057-819">Original Request: `/image.png`</span></span>

![开发人员工具正跟踪 image.png 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="2c057-821">原始请求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="2c057-821">Original Request: `/image.jpg`</span></span>

![开发人员工具正跟踪 image.jpg 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="2c057-823">正则表达式示例</span><span class="sxs-lookup"><span data-stu-id="2c057-823">Regex examples</span></span>

| <span data-ttu-id="2c057-824">目标</span><span class="sxs-lookup"><span data-stu-id="2c057-824">Goal</span></span> | <span data-ttu-id="2c057-825">正则表达式字符串和</span><span class="sxs-lookup"><span data-stu-id="2c057-825">Regex String &</span></span><br><span data-ttu-id="2c057-826">匹配示例</span><span class="sxs-lookup"><span data-stu-id="2c057-826">Match Example</span></span> | <span data-ttu-id="2c057-827">替换字符串和</span><span class="sxs-lookup"><span data-stu-id="2c057-827">Replacement String &</span></span><br><span data-ttu-id="2c057-828">输出示例</span><span class="sxs-lookup"><span data-stu-id="2c057-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="2c057-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-830">'Blazor'</span></span>
- <span data-ttu-id="2c057-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-831">'Identity'</span></span>
- <span data-ttu-id="2c057-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-833">'Razor'</span></span>
- <span data-ttu-id="2c057-834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-836">'Blazor'</span></span>
- <span data-ttu-id="2c057-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-837">'Identity'</span></span>
- <span data-ttu-id="2c057-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-839">'Razor'</span></span>
- <span data-ttu-id="2c057-840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-842">'Blazor'</span></span>
- <span data-ttu-id="2c057-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-843">'Identity'</span></span>
- <span data-ttu-id="2c057-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-845">'Razor'</span></span>
- <span data-ttu-id="2c057-846">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-848">'Blazor'</span></span>
- <span data-ttu-id="2c057-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-849">'Identity'</span></span>
- <span data-ttu-id="2c057-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-851">'Razor'</span></span>
- <span data-ttu-id="2c057-852">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-854">'Blazor'</span></span>
- <span data-ttu-id="2c057-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-855">'Identity'</span></span>
- <span data-ttu-id="2c057-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-857">'Razor'</span></span>
- <span data-ttu-id="2c057-858">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-860">'Blazor'</span></span>
- <span data-ttu-id="2c057-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-861">'Identity'</span></span>
- <span data-ttu-id="2c057-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-863">'Razor'</span></span>
- <span data-ttu-id="2c057-864">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-866">'Blazor'</span></span>
- <span data-ttu-id="2c057-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-867">'Identity'</span></span>
- <span data-ttu-id="2c057-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-869">'Razor'</span></span>
- <span data-ttu-id="2c057-870">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-872">'Blazor'</span></span>
- <span data-ttu-id="2c057-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-873">'Identity'</span></span>
- <span data-ttu-id="2c057-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-875">'Razor'</span></span>
- <span data-ttu-id="2c057-876">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-878">'Blazor'</span></span>
- <span data-ttu-id="2c057-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-879">'Identity'</span></span>
- <span data-ttu-id="2c057-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-881">'Razor'</span></span>
- <span data-ttu-id="2c057-882">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-884">'Blazor'</span></span>
- <span data-ttu-id="2c057-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-885">'Identity'</span></span>
- <span data-ttu-id="2c057-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-887">'Razor'</span></span>
- <span data-ttu-id="2c057-888">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-890">'Blazor'</span></span>
- <span data-ttu-id="2c057-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-891">'Identity'</span></span>
- <span data-ttu-id="2c057-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-893">'Razor'</span></span>
- <span data-ttu-id="2c057-894">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-896">'Blazor'</span></span>
- <span data-ttu-id="2c057-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-897">'Identity'</span></span>
- <span data-ttu-id="2c057-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-899">'Razor'</span></span>
- <span data-ttu-id="2c057-900">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-902">'Blazor'</span></span>
- <span data-ttu-id="2c057-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-903">'Identity'</span></span>
- <span data-ttu-id="2c057-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-905">'Razor'</span></span>
- <span data-ttu-id="2c057-906">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-906">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-908">'Blazor'</span></span>
- <span data-ttu-id="2c057-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-909">'Identity'</span></span>
- <span data-ttu-id="2c057-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-911">'Razor'</span></span>
- <span data-ttu-id="2c057-912">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-914">'Blazor'</span></span>
- <span data-ttu-id="2c057-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-915">'Identity'</span></span>
- <span data-ttu-id="2c057-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-917">'Razor'</span></span>
- <span data-ttu-id="2c057-918">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-920">'Blazor'</span></span>
- <span data-ttu-id="2c057-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-921">'Identity'</span></span>
- <span data-ttu-id="2c057-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-923">'Razor'</span></span>
- <span data-ttu-id="2c057-924">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-926">'Blazor'</span></span>
- <span data-ttu-id="2c057-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-927">'Identity'</span></span>
- <span data-ttu-id="2c057-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-929">'Razor'</span></span>
- <span data-ttu-id="2c057-930">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-932">'Blazor'</span></span>
- <span data-ttu-id="2c057-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-933">'Identity'</span></span>
- <span data-ttu-id="2c057-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-935">'Razor'</span></span>
- <span data-ttu-id="2c057-936">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-938">'Blazor'</span></span>
- <span data-ttu-id="2c057-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-939">'Identity'</span></span>
- <span data-ttu-id="2c057-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-941">'Razor'</span></span>
- <span data-ttu-id="2c057-942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-944">'Blazor'</span></span>
- <span data-ttu-id="2c057-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-945">'Identity'</span></span>
- <span data-ttu-id="2c057-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-947">'Razor'</span></span>
- <span data-ttu-id="2c057-948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-950">'Blazor'</span></span>
- <span data-ttu-id="2c057-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-951">'Identity'</span></span>
- <span data-ttu-id="2c057-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-953">'Razor'</span></span>
- <span data-ttu-id="2c057-954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-956">'Blazor'</span></span>
- <span data-ttu-id="2c057-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-957">'Identity'</span></span>
- <span data-ttu-id="2c057-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-959">'Razor'</span></span>
- <span data-ttu-id="2c057-960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-962">'Blazor'</span></span>
- <span data-ttu-id="2c057-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-963">'Identity'</span></span>
- <span data-ttu-id="2c057-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-965">'Razor'</span></span>
- <span data-ttu-id="2c057-966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-968">'Blazor'</span></span>
- <span data-ttu-id="2c057-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-969">'Identity'</span></span>
- <span data-ttu-id="2c057-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-971">'Razor'</span></span>
- <span data-ttu-id="2c057-972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-974">'Blazor'</span></span>
- <span data-ttu-id="2c057-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-975">'Identity'</span></span>
- <span data-ttu-id="2c057-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-977">'Razor'</span></span>
- <span data-ttu-id="2c057-978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-980">'Blazor'</span></span>
- <span data-ttu-id="2c057-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-981">'Identity'</span></span>
- <span data-ttu-id="2c057-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-983">'Razor'</span></span>
- <span data-ttu-id="2c057-984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-986">'Blazor'</span></span>
- <span data-ttu-id="2c057-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-987">'Identity'</span></span>
- <span data-ttu-id="2c057-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-989">'Razor'</span></span>
- <span data-ttu-id="2c057-990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-992">'Blazor'</span></span>
- <span data-ttu-id="2c057-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-993">'Identity'</span></span>
- <span data-ttu-id="2c057-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-995">'Razor'</span></span>
- <span data-ttu-id="2c057-996">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-998">'Blazor'</span></span>
- <span data-ttu-id="2c057-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-999">'Identity'</span></span>
- <span data-ttu-id="2c057-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1001">'Razor'</span></span>
- <span data-ttu-id="2c057-1002">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1004">'Blazor'</span></span>
- <span data-ttu-id="2c057-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1005">'Identity'</span></span>
- <span data-ttu-id="2c057-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1007">'Razor'</span></span>
- <span data-ttu-id="2c057-1008">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-1009">------------------- | | 将路径重写为查询字符串 | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="2c057-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="2c057-1010">`/path?var1=abc&var2=123` | | 去掉尾部斜杠 | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="2c057-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="2c057-1011">`/path` | | 强制添加尾部斜杠 | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="2c057-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="2c057-1012">`/path/` | | 避免重写特定请求 | `^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="2c057-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="2c057-1013">正确：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="2c057-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="2c057-1014">否：`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="2c057-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="2c057-1015">`/resource.axd` | | 重新排列 URL 段 | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="2c057-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="2c057-1016">`path/3/2/1` | | 替换 URL 段 | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="2c057-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2c057-1017">本文档介绍 URL 重写并说明如何在 ASP.NET Core 应用中使用 URL 重写中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="2c057-1018">URL 重写是根据一个或多个预定义规则修改请求 URL 的行为。</span><span class="sxs-lookup"><span data-stu-id="2c057-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="2c057-1019">URL 重写会在资源位置和地址之间创建一个抽象，使位置和地址不紧密相连。</span><span class="sxs-lookup"><span data-stu-id="2c057-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="2c057-1020">在以下几种方案中，URL 重写很有价值：</span><span class="sxs-lookup"><span data-stu-id="2c057-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="2c057-1021">暂时或永久移动或替换服务器资源，并维护这些资源的稳定定位符。</span><span class="sxs-lookup"><span data-stu-id="2c057-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="2c057-1022">拆分在不同应用或同一应用的不同区域中处理的请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="2c057-1023">删除、添加或重新组织传入请求上的 URL 段。</span><span class="sxs-lookup"><span data-stu-id="2c057-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="2c057-1024">优化搜索引擎优化 (SEO) 的公共 URL。</span><span class="sxs-lookup"><span data-stu-id="2c057-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="2c057-1025">允许使用友好的公共 URL 来帮助访问者预测请求资源后返回的内容。</span><span class="sxs-lookup"><span data-stu-id="2c057-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="2c057-1026">将不安全请求重定向到安全终结点。</span><span class="sxs-lookup"><span data-stu-id="2c057-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="2c057-1027">防止热链接，外部站点会通过热链接将其他站点的资产链接到其自己的内容，从而利用托管在其他站点上的静态资产。</span><span class="sxs-lookup"><span data-stu-id="2c057-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="2c057-1028">URL 重写可能会降低应用的性能。</span><span class="sxs-lookup"><span data-stu-id="2c057-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="2c057-1029">如果可行，应限制规则的数量和复杂度。</span><span class="sxs-lookup"><span data-stu-id="2c057-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="2c057-1030">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2c057-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="2c057-1031">URL 重定向和 URL 重写</span><span class="sxs-lookup"><span data-stu-id="2c057-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="2c057-1032">URL 重定向和 URL 重写之间的用词差异很细微，但这对于向客户端提供资源具有重要意义 。</span><span class="sxs-lookup"><span data-stu-id="2c057-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="2c057-1033">ASP.NET Core 的 URL 重写中间件能够满足两者的需求。</span><span class="sxs-lookup"><span data-stu-id="2c057-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="2c057-1034">URL 重定向涉及客户端操作，指示客户端访问与客户端最初请求地址不同的资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="2c057-1035">这需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="2c057-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="2c057-1036">客户端对资源发出新请求时，返回客户端的重定向 URL 会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="2c057-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="2c057-1037">如果 `/resource` 被重定向到 `/different-resource`，则服务器作出响应，指示客户端应在 `/different-resource` 获取资源，所提供的状态代码指示重定向是临时的还是永久的。</span><span class="sxs-lookup"><span data-stu-id="2c057-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 服务终结点已暂时从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="2c057-1043">将请求重定向到不同 URL 时，通过使用响应指定状态代码来指示重定向是永久还是临时：</span><span class="sxs-lookup"><span data-stu-id="2c057-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="2c057-1044">如果资源有一个新的永久性 URL，并且你希望指示客户端所有将来的资源请求都使用新 URL，则使用“301 (永久移动)”状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="2c057-1045">收到 301 状态代码时，客户端可能会缓存响应并重用这段代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="2c057-1046">“302 (找到)”状态代码用于后列情况：重定向操作是临时的或通常会发生变化。</span><span class="sxs-lookup"><span data-stu-id="2c057-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="2c057-1047">302 状态代码向客户端指示不存储 URL 并在将来使用。</span><span class="sxs-lookup"><span data-stu-id="2c057-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="2c057-1048">有关状态代码的详细信息，请参阅 [RFC 2616：状态代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="2c057-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="2c057-1049">URL 重写是服务器端操作，它从与客户端请求的资源地址不同的资源地址提供资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="2c057-1050">重写 URL 不需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="2c057-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="2c057-1051">重写的 URL 不会返回客户端，也不会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="2c057-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="2c057-1052">如果 `/resource` 重写到 `/different-resource`，服务器会在内部提取并返回 `/different-resource` 处的资源 。</span><span class="sxs-lookup"><span data-stu-id="2c057-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="2c057-1053">尽管客户端可能能够检索已重写 URL 处的资源，但是，客户端发出请求并收到响应时，并不知道已重写 URL 处存在的资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 服务终结点已从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="2c057-1058">URL 重写示例应用</span><span class="sxs-lookup"><span data-stu-id="2c057-1058">URL rewriting sample app</span></span>

<span data-ttu-id="2c057-1059">可使用[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)了解 URL 重写中间件的功能。</span><span class="sxs-lookup"><span data-stu-id="2c057-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="2c057-1060">该应用程序应用重定向和重写规则，并显示多个方案的重定向或重写的 URL。</span><span class="sxs-lookup"><span data-stu-id="2c057-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="2c057-1061">何时使用 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="2c057-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="2c057-1062">如果无法使用以下方法，请使用 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="2c057-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="2c057-1063">在 Windows Server 上使用带 IIS 的 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="2c057-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="2c057-1064">在 Apache 服务器上使用 Apache mod_rewrite 模块</span><span class="sxs-lookup"><span data-stu-id="2c057-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="2c057-1065">Nginx 上的 URL 重写</span><span class="sxs-lookup"><span data-stu-id="2c057-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="2c057-1066">此外，如果应用程序在 [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（旧称 WebListener）上托管，请使用中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="2c057-1067">使用 IIS、Apache 和 Nginx 中的基于服务器的 URL 重写技术的主要原因：</span><span class="sxs-lookup"><span data-stu-id="2c057-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="2c057-1068">中间件不支持这些模块的完整功能。</span><span class="sxs-lookup"><span data-stu-id="2c057-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="2c057-1069">服务器模块的一些功能不适用于 ASP.NET Core 项目，例如 IIS 重写模块的 `IsFile` 和 `IsDirectory` 约束。</span><span class="sxs-lookup"><span data-stu-id="2c057-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="2c057-1070">在这些情况下，请改为使用中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="2c057-1071">中间件性能与模块性能不匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="2c057-1072">基准测试是确定哪种方法会最大程度降低性能或降低的性能是否可忽略不计的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="2c057-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="2c057-1073">Package</span><span class="sxs-lookup"><span data-stu-id="2c057-1073">Package</span></span>

<span data-ttu-id="2c057-1074">要在项目中包含中间件，请在项目文件中添加对 [Microsoft.AspNetCore.App 元数据包](xref:fundamentals/metapackage-app)的包引用，该文件包含 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 包。</span><span class="sxs-lookup"><span data-stu-id="2c057-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="2c057-1075">不使用 `Microsoft.AspNetCore.App` 元包时，向 `Microsoft.AspNetCore.Rewrite` 包添加项目引用。</span><span class="sxs-lookup"><span data-stu-id="2c057-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="2c057-1076">扩展和选项</span><span class="sxs-lookup"><span data-stu-id="2c057-1076">Extension and options</span></span>

<span data-ttu-id="2c057-1077">通过使用扩展方法为每条重写规则创建 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 类的实例，建立 URL 重写和重写定向规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="2c057-1078">按所需的处理顺序链接多个规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="2c057-1079">使用 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 将 `RewriteOptions` 添加到请求管道时，它会被传递到 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="2c057-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="2c057-1080">将非 www 重定向到 www</span><span class="sxs-lookup"><span data-stu-id="2c057-1080">Redirect non-www to www</span></span>

<span data-ttu-id="2c057-1081">三个选项允许应用将非 `www` 重新定向到 `www`：</span><span class="sxs-lookup"><span data-stu-id="2c057-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="2c057-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>：如果请求是非 `www`，则将请求永久重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="2c057-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="2c057-1083">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="2c057-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="2c057-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>：如果传入请求是非 `www`，则将请求重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="2c057-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="2c057-1085">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="2c057-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="2c057-1086">重载允许提供响应状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="2c057-1087">使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 类的字段实现状态代码分配。</span><span class="sxs-lookup"><span data-stu-id="2c057-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="2c057-1088">URL 重定向</span><span class="sxs-lookup"><span data-stu-id="2c057-1088">URL redirect</span></span>

<span data-ttu-id="2c057-1089">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 将请求重定向。</span><span class="sxs-lookup"><span data-stu-id="2c057-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="2c057-1090">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2c057-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="2c057-1091">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="2c057-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="2c057-1092">第三个参数（如有）指定状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="2c057-1093">如不指定状态代码，则状态代码默认为“302 (已找到)”，指示资源暂时移动或替换。</span><span class="sxs-lookup"><span data-stu-id="2c057-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="2c057-1094">在启用了开发人员工具的浏览器中，向路径为 `/redirect-rule/1234/5678` 的示例应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="2c057-1095">正则表达式匹配 `redirect-rule/(.*)` 上的请求路径，且该路径会被 `/redirected/1234/5678` 替代。</span><span class="sxs-lookup"><span data-stu-id="2c057-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="2c057-1096">重定向 URL 以“302 (已找到)”状态代码发回客户端。</span><span class="sxs-lookup"><span data-stu-id="2c057-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="2c057-1097">浏览器会在浏览器地址栏中出现的重定向 URL 上发出新请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="2c057-1098">由于示例应用中的规则都不匹配重定向 URL：</span><span class="sxs-lookup"><span data-stu-id="2c057-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="2c057-1099">第二个请求从应用程序收到“200 (正常)”响应。</span><span class="sxs-lookup"><span data-stu-id="2c057-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="2c057-1100">响应正文显示了重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="2c057-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="2c057-1101">重定向 URL 时，系统将向服务器进行一次往返。</span><span class="sxs-lookup"><span data-stu-id="2c057-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="2c057-1102">建立重定向规则时务必小心。</span><span class="sxs-lookup"><span data-stu-id="2c057-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="2c057-1103">系统会根据应用的每个请求（包括重定向后的请求）对重定向规则进行评估。</span><span class="sxs-lookup"><span data-stu-id="2c057-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="2c057-1104">很容易便会意外创建无限重定向循环。</span><span class="sxs-lookup"><span data-stu-id="2c057-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="2c057-1105">原始请求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="2c057-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="2c057-1107">括号内的表达式部分称为“捕获组”。</span><span class="sxs-lookup"><span data-stu-id="2c057-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="2c057-1108">表达式的点 (`.`) 表示匹配任何字符。</span><span class="sxs-lookup"><span data-stu-id="2c057-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="2c057-1109">星号 (`*`) 表示零次或多次匹配前面的字符。</span><span class="sxs-lookup"><span data-stu-id="2c057-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="2c057-1110">因此，URL 的最后两个路径段 `1234/5678` 由捕获组 `(.*)` 捕获。</span><span class="sxs-lookup"><span data-stu-id="2c057-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="2c057-1111">在请求 URL 中提供的位于 `redirect-rule/` 之后的任何值均由此单个捕获组捕获。</span><span class="sxs-lookup"><span data-stu-id="2c057-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="2c057-1112">在替换字符串中，将捕获组注入带有美元符号 (`$`)、后跟捕获序列号的字符串中。</span><span class="sxs-lookup"><span data-stu-id="2c057-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="2c057-1113">获取的第一个捕获组值为 `$1`，第二个为 `$2`，并且正则表达式中的其他捕获组值将依次继续排列。</span><span class="sxs-lookup"><span data-stu-id="2c057-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="2c057-1114">示例应用的重定向规则正则表达式中只有一个捕获组，因此替换字符串中只有一个注入组，即 `$1`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="2c057-1115">如果应用此规则，URL 将变为 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="2c057-1116">URL 重定向到安全的终结点</span><span class="sxs-lookup"><span data-stu-id="2c057-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="2c057-1117">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 将 HTTP 请求重定向到采用 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="2c057-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="2c057-1118">如不提供状态代码，则中间件默认为“302(已找到)”。</span><span class="sxs-lookup"><span data-stu-id="2c057-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="2c057-1119">如果不提供端口：</span><span class="sxs-lookup"><span data-stu-id="2c057-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="2c057-1120">中间件默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="2c057-1121">方案更改为 `https`（HTTPS 协议），客户端访问端口 443 上的资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="2c057-1122">下面的示例演示如何将状态代码设置为“301(永久移动)”并将端口更改为 5001。</span><span class="sxs-lookup"><span data-stu-id="2c057-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="2c057-1123">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 将不安全的请求重定向到端口 443 上的采用安全 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="2c057-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="2c057-1124">中间件将状态代码设置为“301 (永久移动)”。</span><span class="sxs-lookup"><span data-stu-id="2c057-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="2c057-1125">当重定向到安全的终结点并且不需要其他重定向规则时，建议使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="2c057-1126">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl#require-https)主题。</span><span class="sxs-lookup"><span data-stu-id="2c057-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="2c057-1127">示例应用能够演示如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="2c057-1128">将扩展方法添加到 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="2c057-1129">在任何 URL 上向应用发出不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="2c057-1130">消除自签名证书不受信任的浏览器安全警告，或创建例外以信任证书。</span><span class="sxs-lookup"><span data-stu-id="2c057-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="2c057-1131">使用 `AddRedirectToHttps(301, 5001)` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="2c057-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="2c057-1133">使用 `AddRedirectToHttpsPermanent` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="2c057-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="2c057-1135">URL 重写</span><span class="sxs-lookup"><span data-stu-id="2c057-1135">URL rewrite</span></span>

<span data-ttu-id="2c057-1136">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 创建重写 URL 的规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="2c057-1137">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="2c057-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="2c057-1138">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="2c057-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="2c057-1139">第三个参数 `skipRemainingRules: {true|false}` 指示如果当前规则适用，中间件是否要跳过其他重写规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="2c057-1140">原始请求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="2c057-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="2c057-1142">表达式开头的脱字号 (`^`) 意味着匹配从 URL 路径的开头处开始。</span><span class="sxs-lookup"><span data-stu-id="2c057-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="2c057-1143">在前面的重定向规则 `redirect-rule/(.*)` 的示例中，正则表达式的开头没有脱字号 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="2c057-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="2c057-1144">因此，路径中 `redirect-rule/` 之前的任何字符都可能成功匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="2c057-1145">路径</span><span class="sxs-lookup"><span data-stu-id="2c057-1145">Path</span></span>                               | <span data-ttu-id="2c057-1146">匹配</span><span class="sxs-lookup"><span data-stu-id="2c057-1146">Match</span></span> |
| ---
<span data-ttu-id="2c057-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1148">'Blazor'</span></span>
- <span data-ttu-id="2c057-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1149">'Identity'</span></span>
- <span data-ttu-id="2c057-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1151">'Razor'</span></span>
- <span data-ttu-id="2c057-1152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1154">'Blazor'</span></span>
- <span data-ttu-id="2c057-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1155">'Identity'</span></span>
- <span data-ttu-id="2c057-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1157">'Razor'</span></span>
- <span data-ttu-id="2c057-1158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1160">'Blazor'</span></span>
- <span data-ttu-id="2c057-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1161">'Identity'</span></span>
- <span data-ttu-id="2c057-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1163">'Razor'</span></span>
- <span data-ttu-id="2c057-1164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1166">'Blazor'</span></span>
- <span data-ttu-id="2c057-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1167">'Identity'</span></span>
- <span data-ttu-id="2c057-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1169">'Razor'</span></span>
- <span data-ttu-id="2c057-1170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1172">'Blazor'</span></span>
- <span data-ttu-id="2c057-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1173">'Identity'</span></span>
- <span data-ttu-id="2c057-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1175">'Razor'</span></span>
- <span data-ttu-id="2c057-1176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1178">'Blazor'</span></span>
- <span data-ttu-id="2c057-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1179">'Identity'</span></span>
- <span data-ttu-id="2c057-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1181">'Razor'</span></span>
- <span data-ttu-id="2c057-1182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1184">'Blazor'</span></span>
- <span data-ttu-id="2c057-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1185">'Identity'</span></span>
- <span data-ttu-id="2c057-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1187">'Razor'</span></span>
- <span data-ttu-id="2c057-1188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1190">'Blazor'</span></span>
- <span data-ttu-id="2c057-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1191">'Identity'</span></span>
- <span data-ttu-id="2c057-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1193">'Razor'</span></span>
- <span data-ttu-id="2c057-1194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1196">'Blazor'</span></span>
- <span data-ttu-id="2c057-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1197">'Identity'</span></span>
- <span data-ttu-id="2c057-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1199">'Razor'</span></span>
- <span data-ttu-id="2c057-1200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1202">'Blazor'</span></span>
- <span data-ttu-id="2c057-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1203">'Identity'</span></span>
- <span data-ttu-id="2c057-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1205">'Razor'</span></span>
- <span data-ttu-id="2c057-1206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1208">'Blazor'</span></span>
- <span data-ttu-id="2c057-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1209">'Identity'</span></span>
- <span data-ttu-id="2c057-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1211">'Razor'</span></span>
- <span data-ttu-id="2c057-1212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1214">'Blazor'</span></span>
- <span data-ttu-id="2c057-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1215">'Identity'</span></span>
- <span data-ttu-id="2c057-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1217">'Razor'</span></span>
- <span data-ttu-id="2c057-1218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1220">'Blazor'</span></span>
- <span data-ttu-id="2c057-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1221">'Identity'</span></span>
- <span data-ttu-id="2c057-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1223">'Razor'</span></span>
- <span data-ttu-id="2c057-1224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1226">'Blazor'</span></span>
- <span data-ttu-id="2c057-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1227">'Identity'</span></span>
- <span data-ttu-id="2c057-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1229">'Razor'</span></span>
- <span data-ttu-id="2c057-1230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1232">'Blazor'</span></span>
- <span data-ttu-id="2c057-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1233">'Identity'</span></span>
- <span data-ttu-id="2c057-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1235">'Razor'</span></span>
- <span data-ttu-id="2c057-1236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | 是   | | `/my-cool-redirect-rule/1234/5678` | 是   | | `/anotherredirect-rule/1234/5678`  | 是   |</span><span class="sxs-lookup"><span data-stu-id="2c057-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="2c057-1238">重写规则 `^rewrite-rule/(\d+)/(\d+)` 只能与以 `rewrite-rule/` 开头的路径匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="2c057-1239">注意下表中的匹配差异。</span><span class="sxs-lookup"><span data-stu-id="2c057-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="2c057-1240">路径</span><span class="sxs-lookup"><span data-stu-id="2c057-1240">Path</span></span>                              | <span data-ttu-id="2c057-1241">匹配</span><span class="sxs-lookup"><span data-stu-id="2c057-1241">Match</span></span> |
| ---
<span data-ttu-id="2c057-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1243">'Blazor'</span></span>
- <span data-ttu-id="2c057-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1244">'Identity'</span></span>
- <span data-ttu-id="2c057-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1246">'Razor'</span></span>
- <span data-ttu-id="2c057-1247">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1249">'Blazor'</span></span>
- <span data-ttu-id="2c057-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1250">'Identity'</span></span>
- <span data-ttu-id="2c057-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1252">'Razor'</span></span>
- <span data-ttu-id="2c057-1253">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1255">'Blazor'</span></span>
- <span data-ttu-id="2c057-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1256">'Identity'</span></span>
- <span data-ttu-id="2c057-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1258">'Razor'</span></span>
- <span data-ttu-id="2c057-1259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1261">'Blazor'</span></span>
- <span data-ttu-id="2c057-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1262">'Identity'</span></span>
- <span data-ttu-id="2c057-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1264">'Razor'</span></span>
- <span data-ttu-id="2c057-1265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1267">'Blazor'</span></span>
- <span data-ttu-id="2c057-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1268">'Identity'</span></span>
- <span data-ttu-id="2c057-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1270">'Razor'</span></span>
- <span data-ttu-id="2c057-1271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1273">'Blazor'</span></span>
- <span data-ttu-id="2c057-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1274">'Identity'</span></span>
- <span data-ttu-id="2c057-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1276">'Razor'</span></span>
- <span data-ttu-id="2c057-1277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1279">'Blazor'</span></span>
- <span data-ttu-id="2c057-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1280">'Identity'</span></span>
- <span data-ttu-id="2c057-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1282">'Razor'</span></span>
- <span data-ttu-id="2c057-1283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1285">'Blazor'</span></span>
- <span data-ttu-id="2c057-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1286">'Identity'</span></span>
- <span data-ttu-id="2c057-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1288">'Razor'</span></span>
- <span data-ttu-id="2c057-1289">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1291">'Blazor'</span></span>
- <span data-ttu-id="2c057-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1292">'Identity'</span></span>
- <span data-ttu-id="2c057-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1294">'Razor'</span></span>
- <span data-ttu-id="2c057-1295">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1297">'Blazor'</span></span>
- <span data-ttu-id="2c057-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1298">'Identity'</span></span>
- <span data-ttu-id="2c057-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1300">'Razor'</span></span>
- <span data-ttu-id="2c057-1301">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1303">'Blazor'</span></span>
- <span data-ttu-id="2c057-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1304">'Identity'</span></span>
- <span data-ttu-id="2c057-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1306">'Razor'</span></span>
- <span data-ttu-id="2c057-1307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1309">'Blazor'</span></span>
- <span data-ttu-id="2c057-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1310">'Identity'</span></span>
- <span data-ttu-id="2c057-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1312">'Razor'</span></span>
- <span data-ttu-id="2c057-1313">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1315">'Blazor'</span></span>
- <span data-ttu-id="2c057-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1316">'Identity'</span></span>
- <span data-ttu-id="2c057-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1318">'Razor'</span></span>
- <span data-ttu-id="2c057-1319">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1321">'Blazor'</span></span>
- <span data-ttu-id="2c057-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1322">'Identity'</span></span>
- <span data-ttu-id="2c057-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1324">'Razor'</span></span>
- <span data-ttu-id="2c057-1325">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | 是   | | `/my-cool-rewrite-rule/1234/5678` | 否    | | `/anotherrewrite-rule/1234/5678`  | 否    |</span><span class="sxs-lookup"><span data-stu-id="2c057-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="2c057-1327">在表达式的 `^rewrite-rule/` 部分之后，有两个捕获组 `(\d+)/(\d+)`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="2c057-1328">`\d` 表示与数字匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="2c057-1329">加号 (`+`) 表示与前面的一个或多个字符匹配。</span><span class="sxs-lookup"><span data-stu-id="2c057-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="2c057-1330">因此，URL 必须包含数字加正斜杠加另一个数字的形式。</span><span class="sxs-lookup"><span data-stu-id="2c057-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="2c057-1331">这些捕获组以 `$1` 和 `$2` 的形式注入重写 URL 中。</span><span class="sxs-lookup"><span data-stu-id="2c057-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="2c057-1332">重写规则替换字符串将捕获组放入查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="2c057-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="2c057-1333">重写 `/rewrite-rule/1234/5678` 的请求路径，获取 `/rewritten?var1=1234&var2=5678` 处的资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="2c057-1334">如果原始请求中存在查询字符串，则重写 URL 时会保留此字符串。</span><span class="sxs-lookup"><span data-stu-id="2c057-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="2c057-1335">无需往返服务器来获取资源。</span><span class="sxs-lookup"><span data-stu-id="2c057-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="2c057-1336">如果资源存在，系统会提取资源并以“200（正常）”状态代码返回给客户端。</span><span class="sxs-lookup"><span data-stu-id="2c057-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="2c057-1337">因为客户端不会被重定向，所以浏览器地址栏中的 URL 不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="2c057-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="2c057-1338">客户端无法检测到服务器上发生的 URL 重写操作。</span><span class="sxs-lookup"><span data-stu-id="2c057-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2c057-1339">尽可能使用 `skipRemainingRules: true`，因为匹配规则在计算上很昂贵并且增加了应用响应时间。</span><span class="sxs-lookup"><span data-stu-id="2c057-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="2c057-1340">对于最快的应用响应：</span><span class="sxs-lookup"><span data-stu-id="2c057-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="2c057-1341">按照从最频繁匹配的规则到最不频繁匹配的规则排列重写规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="2c057-1342">如果出现匹配项且无需处理任何其他规则，则跳过剩余规则的处理。</span><span class="sxs-lookup"><span data-stu-id="2c057-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="2c057-1343">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="2c057-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="2c057-1344">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 应用 Apache mod_rewrite 规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="2c057-1345">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="2c057-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="2c057-1346">有关 mod_rewrite 规则的详细信息和示例，请参阅 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="2c057-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="2c057-1347"><xref:System.IO.StreamReader> 用于读取 ApacheModRewrite.txt 规则文件中的规则：</span><span class="sxs-lookup"><span data-stu-id="2c057-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="2c057-1348">示例应用将请求从 `/apache-mod-rules-redirect/(.\*)` 重定向到 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="2c057-1349">响应状态代码为“302 (已找到)”。</span><span class="sxs-lookup"><span data-stu-id="2c057-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="2c057-1350">原始请求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="2c057-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="2c057-1352">中间件支持下列 Apache mod_rewrite 服务器变量：</span><span class="sxs-lookup"><span data-stu-id="2c057-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="2c057-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="2c057-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="2c057-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="2c057-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="2c057-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="2c057-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="2c057-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="2c057-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="2c057-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="2c057-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="2c057-1358">HTTP_HOST</span></span>
* <span data-ttu-id="2c057-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="2c057-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="2c057-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="2c057-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="2c057-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2c057-1361">HTTPS</span></span>
* <span data-ttu-id="2c057-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="2c057-1362">IPV6</span></span>
* <span data-ttu-id="2c057-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="2c057-1363">QUERY_STRING</span></span>
* <span data-ttu-id="2c057-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="2c057-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="2c057-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="2c057-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="2c057-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="2c057-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="2c057-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="2c057-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="2c057-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="2c057-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="2c057-1369">REQUEST_URI</span></span>
* <span data-ttu-id="2c057-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="2c057-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="2c057-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="2c057-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="2c057-1372">SERVER_PORT</span></span>
* <span data-ttu-id="2c057-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="2c057-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="2c057-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="2c057-1374">TIME</span></span>
* <span data-ttu-id="2c057-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="2c057-1375">TIME_DAY</span></span>
* <span data-ttu-id="2c057-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="2c057-1376">TIME_HOUR</span></span>
* <span data-ttu-id="2c057-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="2c057-1377">TIME_MIN</span></span>
* <span data-ttu-id="2c057-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="2c057-1378">TIME_MON</span></span>
* <span data-ttu-id="2c057-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="2c057-1379">TIME_SEC</span></span>
* <span data-ttu-id="2c057-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="2c057-1380">TIME_WDAY</span></span>
* <span data-ttu-id="2c057-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="2c057-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="2c057-1382">IIS URL 重写模块规则</span><span class="sxs-lookup"><span data-stu-id="2c057-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="2c057-1383">若要使用适用于 IIS URL 重写模块的同一规则集，使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="2c057-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="2c057-1384">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="2c057-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="2c057-1385">当在 Windows Server IIS 上运行时，请勿指示中间件使用应用的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2c057-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="2c057-1386">使用 IIS 时，应将这些规则存储在应用的 web.config 文件之外，以避免与 IIS 重写模块发生冲突。</span><span class="sxs-lookup"><span data-stu-id="2c057-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="2c057-1387">有关 IIS URL 重写模块规则的详细信息和示例，请参阅 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)（使用 URL 重写模块 2.0）和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）。</span><span class="sxs-lookup"><span data-stu-id="2c057-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="2c057-1388"><xref:System.IO.StreamReader> 用于读取 IISUrlRewrite.xml 规则文件中的规则：</span><span class="sxs-lookup"><span data-stu-id="2c057-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="2c057-1389">示例应用将请求从 `/iis-rules-rewrite/(.*)` 重写为 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="2c057-1390">以“200 (正常)”状态代码作为响应发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="2c057-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="2c057-1391">原始请求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="2c057-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="2c057-1393">如果有配置了服务器级别规则（可对应用产生不利影响）的活动 IIS 重写模块，则可禁用应用的 IIS 重写模块。</span><span class="sxs-lookup"><span data-stu-id="2c057-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="2c057-1394">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="2c057-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="2c057-1395">不支持的功能</span><span class="sxs-lookup"><span data-stu-id="2c057-1395">Unsupported features</span></span>

<span data-ttu-id="2c057-1396">与 ASP.NET Core 2.x 一同发布的中间件不支持以下 IIS URL 重写模块功能：</span><span class="sxs-lookup"><span data-stu-id="2c057-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="2c057-1397">出站规则</span><span class="sxs-lookup"><span data-stu-id="2c057-1397">Outbound Rules</span></span>
* <span data-ttu-id="2c057-1398">自定义服务器变量</span><span class="sxs-lookup"><span data-stu-id="2c057-1398">Custom Server Variables</span></span>
* <span data-ttu-id="2c057-1399">通配符</span><span class="sxs-lookup"><span data-stu-id="2c057-1399">Wildcards</span></span>
* <span data-ttu-id="2c057-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="2c057-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="2c057-1401">受支持的服务器变量</span><span class="sxs-lookup"><span data-stu-id="2c057-1401">Supported server variables</span></span>

<span data-ttu-id="2c057-1402">中间件支持下列 IIS URL 重写模块服务器变量：</span><span class="sxs-lookup"><span data-stu-id="2c057-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="2c057-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="2c057-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="2c057-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="2c057-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="2c057-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="2c057-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="2c057-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="2c057-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="2c057-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="2c057-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="2c057-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="2c057-1408">HTTP_HOST</span></span>
* <span data-ttu-id="2c057-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="2c057-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="2c057-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="2c057-1410">HTTP_URL</span></span>
* <span data-ttu-id="2c057-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="2c057-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="2c057-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2c057-1412">HTTPS</span></span>
* <span data-ttu-id="2c057-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="2c057-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="2c057-1414">QUERY_STRING</span></span>
* <span data-ttu-id="2c057-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="2c057-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="2c057-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="2c057-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="2c057-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="2c057-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="2c057-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="2c057-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="2c057-1419">也可通过 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 获取 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="2c057-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="2c057-1420">这种方法可为重写规则文件的位置提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="2c057-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="2c057-1421">请确保将重写规则文件部署到所提供路径的服务器中。</span><span class="sxs-lookup"><span data-stu-id="2c057-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="2c057-1422">基于方法的规则</span><span class="sxs-lookup"><span data-stu-id="2c057-1422">Method-based rule</span></span>

<span data-ttu-id="2c057-1423">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在方法中实现自己的规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="2c057-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="2c057-1424">`Add` 公开 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，这使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用于方法中。</span><span class="sxs-lookup"><span data-stu-id="2c057-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="2c057-1425">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) 决定如何处理其他管道进程。</span><span class="sxs-lookup"><span data-stu-id="2c057-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="2c057-1426">将值设置为下表中的 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 字段之一。</span><span class="sxs-lookup"><span data-stu-id="2c057-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="2c057-1427">操作</span><span class="sxs-lookup"><span data-stu-id="2c057-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="2c057-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1429">'Blazor'</span></span>
- <span data-ttu-id="2c057-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1430">'Identity'</span></span>
- <span data-ttu-id="2c057-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1432">'Razor'</span></span>
- <span data-ttu-id="2c057-1433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1435">'Blazor'</span></span>
- <span data-ttu-id="2c057-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1436">'Identity'</span></span>
- <span data-ttu-id="2c057-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1438">'Razor'</span></span>
- <span data-ttu-id="2c057-1439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1441">'Blazor'</span></span>
- <span data-ttu-id="2c057-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1442">'Identity'</span></span>
- <span data-ttu-id="2c057-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1444">'Razor'</span></span>
- <span data-ttu-id="2c057-1445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1447">'Blazor'</span></span>
- <span data-ttu-id="2c057-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1448">'Identity'</span></span>
- <span data-ttu-id="2c057-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1450">'Razor'</span></span>
- <span data-ttu-id="2c057-1451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1453">'Blazor'</span></span>
- <span data-ttu-id="2c057-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1454">'Identity'</span></span>
- <span data-ttu-id="2c057-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1456">'Razor'</span></span>
- <span data-ttu-id="2c057-1457">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1459">'Blazor'</span></span>
- <span data-ttu-id="2c057-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1460">'Identity'</span></span>
- <span data-ttu-id="2c057-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1462">'Razor'</span></span>
- <span data-ttu-id="2c057-1463">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1465">'Blazor'</span></span>
- <span data-ttu-id="2c057-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1466">'Identity'</span></span>
- <span data-ttu-id="2c057-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1468">'Razor'</span></span>
- <span data-ttu-id="2c057-1469">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1471">'Blazor'</span></span>
- <span data-ttu-id="2c057-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1472">'Identity'</span></span>
- <span data-ttu-id="2c057-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1474">'Razor'</span></span>
- <span data-ttu-id="2c057-1475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1477">'Blazor'</span></span>
- <span data-ttu-id="2c057-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1478">'Identity'</span></span>
- <span data-ttu-id="2c057-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1480">'Razor'</span></span>
- <span data-ttu-id="2c057-1481">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1483">'Blazor'</span></span>
- <span data-ttu-id="2c057-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1484">'Identity'</span></span>
- <span data-ttu-id="2c057-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1486">'Razor'</span></span>
- <span data-ttu-id="2c057-1487">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1489">'Blazor'</span></span>
- <span data-ttu-id="2c057-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1490">'Identity'</span></span>
- <span data-ttu-id="2c057-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1492">'Razor'</span></span>
- <span data-ttu-id="2c057-1493">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1495">'Blazor'</span></span>
- <span data-ttu-id="2c057-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1496">'Identity'</span></span>
- <span data-ttu-id="2c057-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1498">'Razor'</span></span>
- <span data-ttu-id="2c057-1499">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1501">'Blazor'</span></span>
- <span data-ttu-id="2c057-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1502">'Identity'</span></span>
- <span data-ttu-id="2c057-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1504">'Razor'</span></span>
- <span data-ttu-id="2c057-1505">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1507">'Blazor'</span></span>
- <span data-ttu-id="2c057-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1508">'Identity'</span></span>
- <span data-ttu-id="2c057-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1510">'Razor'</span></span>
- <span data-ttu-id="2c057-1511">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1513">'Blazor'</span></span>
- <span data-ttu-id="2c057-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1514">'Identity'</span></span>
- <span data-ttu-id="2c057-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1516">'Razor'</span></span>
- <span data-ttu-id="2c057-1517">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1519">'Blazor'</span></span>
- <span data-ttu-id="2c057-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1520">'Identity'</span></span>
- <span data-ttu-id="2c057-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1522">'Razor'</span></span>
- <span data-ttu-id="2c057-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1525">'Blazor'</span></span>
- <span data-ttu-id="2c057-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1526">'Identity'</span></span>
- <span data-ttu-id="2c057-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1528">'Razor'</span></span>
- <span data-ttu-id="2c057-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1531">'Blazor'</span></span>
- <span data-ttu-id="2c057-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1532">'Identity'</span></span>
- <span data-ttu-id="2c057-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1534">'Razor'</span></span>
- <span data-ttu-id="2c057-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1537">'Blazor'</span></span>
- <span data-ttu-id="2c057-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1538">'Identity'</span></span>
- <span data-ttu-id="2c057-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1540">'Razor'</span></span>
- <span data-ttu-id="2c057-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1543">'Blazor'</span></span>
- <span data-ttu-id="2c057-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1544">'Identity'</span></span>
- <span data-ttu-id="2c057-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1546">'Razor'</span></span>
- <span data-ttu-id="2c057-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1549">'Blazor'</span></span>
- <span data-ttu-id="2c057-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1550">'Identity'</span></span>
- <span data-ttu-id="2c057-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1552">'Razor'</span></span>
- <span data-ttu-id="2c057-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1555">'Blazor'</span></span>
- <span data-ttu-id="2c057-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1556">'Identity'</span></span>
- <span data-ttu-id="2c057-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1558">'Razor'</span></span>
- <span data-ttu-id="2c057-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1561">'Blazor'</span></span>
- <span data-ttu-id="2c057-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1562">'Identity'</span></span>
- <span data-ttu-id="2c057-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1564">'Razor'</span></span>
- <span data-ttu-id="2c057-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1567">'Blazor'</span></span>
- <span data-ttu-id="2c057-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1568">'Identity'</span></span>
- <span data-ttu-id="2c057-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1570">'Razor'</span></span>
- <span data-ttu-id="2c057-1571">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1573">'Blazor'</span></span>
- <span data-ttu-id="2c057-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1574">'Identity'</span></span>
- <span data-ttu-id="2c057-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1576">'Razor'</span></span>
- <span data-ttu-id="2c057-1577">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1579">'Blazor'</span></span>
- <span data-ttu-id="2c057-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1580">'Identity'</span></span>
- <span data-ttu-id="2c057-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1582">'Razor'</span></span>
- <span data-ttu-id="2c057-1583">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1585">'Blazor'</span></span>
- <span data-ttu-id="2c057-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1586">'Identity'</span></span>
- <span data-ttu-id="2c057-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1588">'Razor'</span></span>
- <span data-ttu-id="2c057-1589">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1591">'Blazor'</span></span>
- <span data-ttu-id="2c057-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1592">'Identity'</span></span>
- <span data-ttu-id="2c057-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1594">'Razor'</span></span>
- <span data-ttu-id="2c057-1595">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1597">'Blazor'</span></span>
- <span data-ttu-id="2c057-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1598">'Identity'</span></span>
- <span data-ttu-id="2c057-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1600">'Razor'</span></span>
- <span data-ttu-id="2c057-1601">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1603">'Blazor'</span></span>
- <span data-ttu-id="2c057-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1604">'Identity'</span></span>
- <span data-ttu-id="2c057-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1606">'Razor'</span></span>
- <span data-ttu-id="2c057-1607">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1609">'Blazor'</span></span>
- <span data-ttu-id="2c057-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1610">'Identity'</span></span>
- <span data-ttu-id="2c057-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1612">'Razor'</span></span>
- <span data-ttu-id="2c057-1613">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1615">'Blazor'</span></span>
- <span data-ttu-id="2c057-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1616">'Identity'</span></span>
- <span data-ttu-id="2c057-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1618">'Razor'</span></span>
- <span data-ttu-id="2c057-1619">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1621">'Blazor'</span></span>
- <span data-ttu-id="2c057-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1622">'Identity'</span></span>
- <span data-ttu-id="2c057-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1624">'Razor'</span></span>
- <span data-ttu-id="2c057-1625">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1627">'Blazor'</span></span>
- <span data-ttu-id="2c057-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1628">'Identity'</span></span>
- <span data-ttu-id="2c057-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1630">'Razor'</span></span>
- <span data-ttu-id="2c057-1631">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1633">'Blazor'</span></span>
- <span data-ttu-id="2c057-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1634">'Identity'</span></span>
- <span data-ttu-id="2c057-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1636">'Razor'</span></span>
- <span data-ttu-id="2c057-1637">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1639">'Blazor'</span></span>
- <span data-ttu-id="2c057-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1640">'Identity'</span></span>
- <span data-ttu-id="2c057-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1642">'Razor'</span></span>
- <span data-ttu-id="2c057-1643">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1645">'Blazor'</span></span>
- <span data-ttu-id="2c057-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1646">'Identity'</span></span>
- <span data-ttu-id="2c057-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1648">'Razor'</span></span>
- <span data-ttu-id="2c057-1649">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1651">'Blazor'</span></span>
- <span data-ttu-id="2c057-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1652">'Identity'</span></span>
- <span data-ttu-id="2c057-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1654">'Razor'</span></span>
- <span data-ttu-id="2c057-1655">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1657">'Blazor'</span></span>
- <span data-ttu-id="2c057-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1658">'Identity'</span></span>
- <span data-ttu-id="2c057-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1660">'Razor'</span></span>
- <span data-ttu-id="2c057-1661">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1663">'Blazor'</span></span>
- <span data-ttu-id="2c057-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1664">'Identity'</span></span>
- <span data-ttu-id="2c057-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1666">'Razor'</span></span>
- <span data-ttu-id="2c057-1667">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1669">'Blazor'</span></span>
- <span data-ttu-id="2c057-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1670">'Identity'</span></span>
- <span data-ttu-id="2c057-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1672">'Razor'</span></span>
- <span data-ttu-id="2c057-1673">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1675">'Blazor'</span></span>
- <span data-ttu-id="2c057-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1676">'Identity'</span></span>
- <span data-ttu-id="2c057-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1678">'Razor'</span></span>
- <span data-ttu-id="2c057-1679">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1681">'Blazor'</span></span>
- <span data-ttu-id="2c057-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1682">'Identity'</span></span>
- <span data-ttu-id="2c057-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1684">'Razor'</span></span>
- <span data-ttu-id="2c057-1685">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1687">'Blazor'</span></span>
- <span data-ttu-id="2c057-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1688">'Identity'</span></span>
- <span data-ttu-id="2c057-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1690">'Razor'</span></span>
- <span data-ttu-id="2c057-1691">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1693">'Blazor'</span></span>
- <span data-ttu-id="2c057-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1694">'Identity'</span></span>
- <span data-ttu-id="2c057-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1696">'Razor'</span></span>
- <span data-ttu-id="2c057-1697">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1699">'Blazor'</span></span>
- <span data-ttu-id="2c057-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1700">'Identity'</span></span>
- <span data-ttu-id="2c057-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1702">'Razor'</span></span>
- <span data-ttu-id="2c057-1703">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-1704">-------------------------------- | | `RuleResult.ContinueRules`（默认）| 继续应用规则。</span><span class="sxs-lookup"><span data-stu-id="2c057-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="2c057-1705">| | `RuleResult.EndResponse`             | 停止应用规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="2c057-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="2c057-1706">| | `RuleResult.SkipRemainingRules`      | 停止应用规则并将上下文发送给下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="2c057-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="2c057-1707">示例应用演示了如何对以 .xml 结尾的路径的请求进行重定向。</span><span class="sxs-lookup"><span data-stu-id="2c057-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="2c057-1708">如果发出针对 `/file.xml` 的请求，请求将重定向到 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="2c057-1709">状态代码设置为“301 (永久移动)”。</span><span class="sxs-lookup"><span data-stu-id="2c057-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="2c057-1710">当浏览器发出针对 /xmlfiles/file.xml 的新请求后，静态文件中间件会将文件从 wwwroot / xmlfiles 文件夹提供给客户端 。</span><span class="sxs-lookup"><span data-stu-id="2c057-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="2c057-1711">对于重定向，请显式设置响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c057-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="2c057-1712">否则，将会返回“200 (正常)”状态代码，且客户端上不会发生重写。</span><span class="sxs-lookup"><span data-stu-id="2c057-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="2c057-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="2c057-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="2c057-1714">此方法还可以重写请求。</span><span class="sxs-lookup"><span data-stu-id="2c057-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="2c057-1715">示例应用演示了如何重写任何文本文件请求的路径以从 wwwroot 文件夹中提供 file.txt 文本文件 。</span><span class="sxs-lookup"><span data-stu-id="2c057-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="2c057-1716">静态文件中间件基于更新的请求路径来提供文件：</span><span class="sxs-lookup"><span data-stu-id="2c057-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="2c057-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="2c057-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="2c057-1718">基于 IRule 的规则</span><span class="sxs-lookup"><span data-stu-id="2c057-1718">IRule-based rule</span></span>

<span data-ttu-id="2c057-1719">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在实现 <xref:Microsoft.AspNetCore.Rewrite.IRule> 接口的类中使用规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="2c057-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="2c057-1720">与使用基于方法的规则方法相比，`IRule` 提供了更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="2c057-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="2c057-1721">实现类可能包含构造函数，可在其中传入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的参数。</span><span class="sxs-lookup"><span data-stu-id="2c057-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="2c057-1722">检查示例应用中 `extension` 和 `newPath` 的参数值是否符合多个条件。</span><span class="sxs-lookup"><span data-stu-id="2c057-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="2c057-1723">`extension` 须包含一个值，并且该值必须是 .png、.jpg 或 .gif  。</span><span class="sxs-lookup"><span data-stu-id="2c057-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="2c057-1724">如果 `newPath` 无效，则会引发 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="2c057-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="2c057-1725">如果发出针对 image.png 的请求，请求将重定向到 `/png-images/image.png`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="2c057-1726">如果发出针对 image.png 的请求，请求将重定向到 `/jpg-images/image.jpg`。</span><span class="sxs-lookup"><span data-stu-id="2c057-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="2c057-1727">状态代码设置为“301 (永久移动)”，`context.Result` 设置为停止处理规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="2c057-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="2c057-1728">原始请求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="2c057-1728">Original Request: `/image.png`</span></span>

![开发人员工具正跟踪 image.png 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="2c057-1730">原始请求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="2c057-1730">Original Request: `/image.jpg`</span></span>

![开发人员工具正跟踪 image.jpg 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="2c057-1732">正则表达式示例</span><span class="sxs-lookup"><span data-stu-id="2c057-1732">Regex examples</span></span>

| <span data-ttu-id="2c057-1733">目标</span><span class="sxs-lookup"><span data-stu-id="2c057-1733">Goal</span></span> | <span data-ttu-id="2c057-1734">正则表达式字符串和</span><span class="sxs-lookup"><span data-stu-id="2c057-1734">Regex String &</span></span><br><span data-ttu-id="2c057-1735">匹配示例</span><span class="sxs-lookup"><span data-stu-id="2c057-1735">Match Example</span></span> | <span data-ttu-id="2c057-1736">替换字符串和</span><span class="sxs-lookup"><span data-stu-id="2c057-1736">Replacement String &</span></span><br><span data-ttu-id="2c057-1737">输出示例</span><span class="sxs-lookup"><span data-stu-id="2c057-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="2c057-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1739">'Blazor'</span></span>
- <span data-ttu-id="2c057-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1740">'Identity'</span></span>
- <span data-ttu-id="2c057-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1742">'Razor'</span></span>
- <span data-ttu-id="2c057-1743">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1745">'Blazor'</span></span>
- <span data-ttu-id="2c057-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1746">'Identity'</span></span>
- <span data-ttu-id="2c057-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1748">'Razor'</span></span>
- <span data-ttu-id="2c057-1749">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1751">'Blazor'</span></span>
- <span data-ttu-id="2c057-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1752">'Identity'</span></span>
- <span data-ttu-id="2c057-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1754">'Razor'</span></span>
- <span data-ttu-id="2c057-1755">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1757">'Blazor'</span></span>
- <span data-ttu-id="2c057-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1758">'Identity'</span></span>
- <span data-ttu-id="2c057-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1760">'Razor'</span></span>
- <span data-ttu-id="2c057-1761">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1763">'Blazor'</span></span>
- <span data-ttu-id="2c057-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1764">'Identity'</span></span>
- <span data-ttu-id="2c057-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1766">'Razor'</span></span>
- <span data-ttu-id="2c057-1767">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1769">'Blazor'</span></span>
- <span data-ttu-id="2c057-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1770">'Identity'</span></span>
- <span data-ttu-id="2c057-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1772">'Razor'</span></span>
- <span data-ttu-id="2c057-1773">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1775">'Blazor'</span></span>
- <span data-ttu-id="2c057-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1776">'Identity'</span></span>
- <span data-ttu-id="2c057-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1778">'Razor'</span></span>
- <span data-ttu-id="2c057-1779">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1781">'Blazor'</span></span>
- <span data-ttu-id="2c057-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1782">'Identity'</span></span>
- <span data-ttu-id="2c057-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1784">'Razor'</span></span>
- <span data-ttu-id="2c057-1785">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1787">'Blazor'</span></span>
- <span data-ttu-id="2c057-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1788">'Identity'</span></span>
- <span data-ttu-id="2c057-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1790">'Razor'</span></span>
- <span data-ttu-id="2c057-1791">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1793">'Blazor'</span></span>
- <span data-ttu-id="2c057-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1794">'Identity'</span></span>
- <span data-ttu-id="2c057-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1796">'Razor'</span></span>
- <span data-ttu-id="2c057-1797">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1799">'Blazor'</span></span>
- <span data-ttu-id="2c057-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1800">'Identity'</span></span>
- <span data-ttu-id="2c057-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1802">'Razor'</span></span>
- <span data-ttu-id="2c057-1803">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1805">'Blazor'</span></span>
- <span data-ttu-id="2c057-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1806">'Identity'</span></span>
- <span data-ttu-id="2c057-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1808">'Razor'</span></span>
- <span data-ttu-id="2c057-1809">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1811">'Blazor'</span></span>
- <span data-ttu-id="2c057-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1812">'Identity'</span></span>
- <span data-ttu-id="2c057-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1814">'Razor'</span></span>
- <span data-ttu-id="2c057-1815">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1817">'Blazor'</span></span>
- <span data-ttu-id="2c057-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1818">'Identity'</span></span>
- <span data-ttu-id="2c057-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1820">'Razor'</span></span>
- <span data-ttu-id="2c057-1821">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1823">'Blazor'</span></span>
- <span data-ttu-id="2c057-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1824">'Identity'</span></span>
- <span data-ttu-id="2c057-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1826">'Razor'</span></span>
- <span data-ttu-id="2c057-1827">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1829">'Blazor'</span></span>
- <span data-ttu-id="2c057-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1830">'Identity'</span></span>
- <span data-ttu-id="2c057-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1832">'Razor'</span></span>
- <span data-ttu-id="2c057-1833">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1835">'Blazor'</span></span>
- <span data-ttu-id="2c057-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1836">'Identity'</span></span>
- <span data-ttu-id="2c057-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1838">'Razor'</span></span>
- <span data-ttu-id="2c057-1839">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1841">'Blazor'</span></span>
- <span data-ttu-id="2c057-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1842">'Identity'</span></span>
- <span data-ttu-id="2c057-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1844">'Razor'</span></span>
- <span data-ttu-id="2c057-1845">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1847">'Blazor'</span></span>
- <span data-ttu-id="2c057-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1848">'Identity'</span></span>
- <span data-ttu-id="2c057-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1850">'Razor'</span></span>
- <span data-ttu-id="2c057-1851">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1853">'Blazor'</span></span>
- <span data-ttu-id="2c057-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1854">'Identity'</span></span>
- <span data-ttu-id="2c057-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1856">'Razor'</span></span>
- <span data-ttu-id="2c057-1857">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1859">'Blazor'</span></span>
- <span data-ttu-id="2c057-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1860">'Identity'</span></span>
- <span data-ttu-id="2c057-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1862">'Razor'</span></span>
- <span data-ttu-id="2c057-1863">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1865">'Blazor'</span></span>
- <span data-ttu-id="2c057-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1866">'Identity'</span></span>
- <span data-ttu-id="2c057-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1868">'Razor'</span></span>
- <span data-ttu-id="2c057-1869">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1871">'Blazor'</span></span>
- <span data-ttu-id="2c057-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1872">'Identity'</span></span>
- <span data-ttu-id="2c057-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1874">'Razor'</span></span>
- <span data-ttu-id="2c057-1875">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1877">'Blazor'</span></span>
- <span data-ttu-id="2c057-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1878">'Identity'</span></span>
- <span data-ttu-id="2c057-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1880">'Razor'</span></span>
- <span data-ttu-id="2c057-1881">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1883">'Blazor'</span></span>
- <span data-ttu-id="2c057-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1884">'Identity'</span></span>
- <span data-ttu-id="2c057-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1886">'Razor'</span></span>
- <span data-ttu-id="2c057-1887">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1889">'Blazor'</span></span>
- <span data-ttu-id="2c057-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1890">'Identity'</span></span>
- <span data-ttu-id="2c057-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1892">'Razor'</span></span>
- <span data-ttu-id="2c057-1893">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1895">'Blazor'</span></span>
- <span data-ttu-id="2c057-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1896">'Identity'</span></span>
- <span data-ttu-id="2c057-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1898">'Razor'</span></span>
- <span data-ttu-id="2c057-1899">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1901">'Blazor'</span></span>
- <span data-ttu-id="2c057-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1902">'Identity'</span></span>
- <span data-ttu-id="2c057-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1904">'Razor'</span></span>
- <span data-ttu-id="2c057-1905">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1907">'Blazor'</span></span>
- <span data-ttu-id="2c057-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1908">'Identity'</span></span>
- <span data-ttu-id="2c057-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1910">'Razor'</span></span>
- <span data-ttu-id="2c057-1911">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2c057-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2c057-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2c057-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1913">'Blazor'</span></span>
- <span data-ttu-id="2c057-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2c057-1914">'Identity'</span></span>
- <span data-ttu-id="2c057-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2c057-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="2c057-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2c057-1916">'Razor'</span></span>
- <span data-ttu-id="2c057-1917">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2c057-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="2c057-1918">------------------- | | 将路径重写为查询字符串 | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="2c057-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="2c057-1919">`/path?var1=abc&var2=123` | | 去掉尾部斜杠 | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="2c057-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="2c057-1920">`/path` | | 强制添加尾部斜杠 | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="2c057-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="2c057-1921">`/path/` | | 避免重写特定请求 | `^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="2c057-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="2c057-1922">正确：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="2c057-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="2c057-1923">否：`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="2c057-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="2c057-1924">`/resource.axd` | | 重新排列 URL 段 | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="2c057-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="2c057-1925">`path/3/2/1` | | 替换 URL 段 | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="2c057-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2c057-1926">其他资源</span><span class="sxs-lookup"><span data-stu-id="2c057-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="2c057-1927">.NET 中的正则表达式</span><span class="sxs-lookup"><span data-stu-id="2c057-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="2c057-1928">正则表达式语言 - 快速参考</span><span class="sxs-lookup"><span data-stu-id="2c057-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="2c057-1929">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="2c057-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="2c057-1930">使用 URL 重写模块 2.0（适用于 IIS）</span><span class="sxs-lookup"><span data-stu-id="2c057-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* <span data-ttu-id="2c057-1931">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）</span><span class="sxs-lookup"><span data-stu-id="2c057-1931">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span></span>
* [<span data-ttu-id="2c057-1932">IIS URL 重写模块论坛</span><span class="sxs-lookup"><span data-stu-id="2c057-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* <span data-ttu-id="2c057-1933">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)（保持简单的 URL 结构）</span><span class="sxs-lookup"><span data-stu-id="2c057-1933">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)</span></span>
* <span data-ttu-id="2c057-1934">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)（10 个 URL 重写提示和技巧）</span><span class="sxs-lookup"><span data-stu-id="2c057-1934">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)</span></span>
* <span data-ttu-id="2c057-1935">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)（用斜杠或不用斜杠）</span><span class="sxs-lookup"><span data-stu-id="2c057-1935">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)</span></span>
