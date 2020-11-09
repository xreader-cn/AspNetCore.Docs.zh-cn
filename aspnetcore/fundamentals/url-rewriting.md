---
title: ASP.NET Core 中的 URL 重写中间件
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用程序中使用 URL 重写中间件进行 URL 重写和重定向。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/url-rewriting
ms.openlocfilehash: e7bd5f4d61661dd23eb0907f896d0d32b7799aac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061296"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="9f643-103">ASP.NET Core 中的 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="9f643-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="9f643-104">作者：[Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="9f643-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9f643-105">本文档介绍 URL 重写并说明如何在 ASP.NET Core 应用中使用 URL 重写中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="9f643-106">URL 重写是根据一个或多个预定义规则修改请求 URL 的行为。</span><span class="sxs-lookup"><span data-stu-id="9f643-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="9f643-107">URL 重写会在资源位置和地址之间创建一个抽象，使位置和地址不紧密相连。</span><span class="sxs-lookup"><span data-stu-id="9f643-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="9f643-108">在以下几种方案中，URL 重写很有价值：</span><span class="sxs-lookup"><span data-stu-id="9f643-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="9f643-109">暂时或永久移动或替换服务器资源，并维护这些资源的稳定定位符。</span><span class="sxs-lookup"><span data-stu-id="9f643-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="9f643-110">拆分在不同应用或同一应用的不同区域中处理的请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="9f643-111">删除、添加或重新组织传入请求上的 URL 段。</span><span class="sxs-lookup"><span data-stu-id="9f643-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="9f643-112">优化搜索引擎优化 (SEO) 的公共 URL。</span><span class="sxs-lookup"><span data-stu-id="9f643-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="9f643-113">允许使用友好的公共 URL 来帮助访问者预测请求资源后返回的内容。</span><span class="sxs-lookup"><span data-stu-id="9f643-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="9f643-114">将不安全请求重定向到安全终结点。</span><span class="sxs-lookup"><span data-stu-id="9f643-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="9f643-115">防止热链接，外部站点会通过热链接将其他站点的资产链接到其自己的内容，从而利用托管在其他站点上的静态资产。</span><span class="sxs-lookup"><span data-stu-id="9f643-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="9f643-116">URL 重写可能会降低应用的性能。</span><span class="sxs-lookup"><span data-stu-id="9f643-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="9f643-117">如果可行，应限制规则的数量和复杂度。</span><span class="sxs-lookup"><span data-stu-id="9f643-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="9f643-118">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9f643-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="9f643-119">URL 重定向和 URL 重写</span><span class="sxs-lookup"><span data-stu-id="9f643-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="9f643-120">URL 重定向和 URL 重写之间的用词差异很细微，但这对于向客户端提供资源具有重要意义 。</span><span class="sxs-lookup"><span data-stu-id="9f643-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="9f643-121">ASP.NET Core 的 URL 重写中间件能够满足两者的需求。</span><span class="sxs-lookup"><span data-stu-id="9f643-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="9f643-122">URL 重定向涉及客户端操作，指示客户端访问与客户端最初请求地址不同的资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="9f643-123">这需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="9f643-123">This requires a round trip to the server.</span></span> <span data-ttu-id="9f643-124">客户端对资源发出新请求时，返回客户端的重定向 URL 会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="9f643-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="9f643-125">如果 `/resource` 被重定向到 `/different-resource`，则服务器作出响应，指示客户端应在 `/different-resource` 获取资源，所提供的状态代码指示重定向是临时的还是永久的。</span><span class="sxs-lookup"><span data-stu-id="9f643-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 服务终结点已暂时从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="9f643-131">将请求重定向到不同 URL 时，通过使用响应指定状态代码来指示重定向是永久还是临时：</span><span class="sxs-lookup"><span data-stu-id="9f643-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="9f643-132">如果资源有一个新的永久性 URL，并且你希望指示客户端所有将来的资源请求都使用新 URL，则使用“301 (永久移动)”状态代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="9f643-133">收到 301 状态代码时，客户端可能会缓存响应并重用这段代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="9f643-134">“302 (找到)”状态代码用于后列情况：重定向操作是临时的或通常会发生变化。</span><span class="sxs-lookup"><span data-stu-id="9f643-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="9f643-135">302 状态代码向客户端指示不存储 URL 并在将来使用。</span><span class="sxs-lookup"><span data-stu-id="9f643-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="9f643-136">有关状态代码的详细信息，请参阅 [RFC 2616：状态代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="9f643-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="9f643-137">URL 重写是服务器端操作，它从与客户端请求的资源地址不同的资源地址提供资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="9f643-138">重写 URL 不需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="9f643-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="9f643-139">重写的 URL 不会返回客户端，也不会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="9f643-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="9f643-140">如果 `/resource` 重写到 `/different-resource`，服务器会在内部提取并返回 `/different-resource` 处的资源 。</span><span class="sxs-lookup"><span data-stu-id="9f643-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="9f643-141">尽管客户端可能能够检索已重写 URL 处的资源，但是，客户端发出请求并收到响应时，并不知道已重写 URL 处存在的资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 服务终结点已从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="9f643-146">URL 重写示例应用</span><span class="sxs-lookup"><span data-stu-id="9f643-146">URL rewriting sample app</span></span>

<span data-ttu-id="9f643-147">可使用[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)了解 URL 重写中间件的功能。</span><span class="sxs-lookup"><span data-stu-id="9f643-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="9f643-148">该应用程序应用重定向和重写规则，并显示多个方案的重定向或重写的 URL。</span><span class="sxs-lookup"><span data-stu-id="9f643-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="9f643-149">何时使用 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="9f643-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="9f643-150">如果无法使用以下方法，请使用 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="9f643-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="9f643-151">在 Windows Server 上使用带 IIS 的 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="9f643-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="9f643-152">在 Apache 服务器上使用 Apache mod_rewrite 模块</span><span class="sxs-lookup"><span data-stu-id="9f643-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="9f643-153">Nginx 上的 URL 重写</span><span class="sxs-lookup"><span data-stu-id="9f643-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="9f643-154">此外，如果应用程序在 [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（旧称 WebListener）上托管，请使用中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="9f643-155">使用 IIS、Apache 和 Nginx 中的基于服务器的 URL 重写技术的主要原因：</span><span class="sxs-lookup"><span data-stu-id="9f643-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="9f643-156">中间件不支持这些模块的完整功能。</span><span class="sxs-lookup"><span data-stu-id="9f643-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="9f643-157">服务器模块的一些功能不适用于 ASP.NET Core 项目，例如 IIS 重写模块的 `IsFile` 和 `IsDirectory` 约束。</span><span class="sxs-lookup"><span data-stu-id="9f643-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="9f643-158">在这些情况下，请改为使用中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="9f643-159">中间件性能与模块性能不匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="9f643-160">基准测试是确定哪种方法会最大程度降低性能或降低的性能是否可忽略不计的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="9f643-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="9f643-161">Package</span><span class="sxs-lookup"><span data-stu-id="9f643-161">Package</span></span>

<span data-ttu-id="9f643-162">URL 重写中间件由 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 包提供，该包隐式包含在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="9f643-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="9f643-163">扩展和选项</span><span class="sxs-lookup"><span data-stu-id="9f643-163">Extension and options</span></span>

<span data-ttu-id="9f643-164">通过使用扩展方法为每条重写规则创建 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 类的实例，建立 URL 重写和重写定向规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="9f643-165">按所需的处理顺序链接多个规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="9f643-166">使用 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 将 `RewriteOptions` 添加到请求管道时，它会被传递到 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="9f643-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="9f643-167">将非 www 重定向到 www</span><span class="sxs-lookup"><span data-stu-id="9f643-167">Redirect non-www to www</span></span>

<span data-ttu-id="9f643-168">三个选项允许应用将非 `www` 重新定向到 `www`：</span><span class="sxs-lookup"><span data-stu-id="9f643-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="9f643-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>：如果请求是非 `www`，则将请求永久重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="9f643-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="9f643-170">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="9f643-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="9f643-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>：如果传入请求是非 `www`，则将请求重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="9f643-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="9f643-172">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="9f643-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="9f643-173">重载允许提供响应状态代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="9f643-174">使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 类的字段实现状态代码分配。</span><span class="sxs-lookup"><span data-stu-id="9f643-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="9f643-175">URL 重定向</span><span class="sxs-lookup"><span data-stu-id="9f643-175">URL redirect</span></span>

<span data-ttu-id="9f643-176">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 将请求重定向。</span><span class="sxs-lookup"><span data-stu-id="9f643-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="9f643-177">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="9f643-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="9f643-178">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="9f643-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="9f643-179">第三个参数（如有）指定状态代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="9f643-180">如不指定状态代码，则状态代码默认为“302 (已找到)”，指示资源暂时移动或替换。</span><span class="sxs-lookup"><span data-stu-id="9f643-180">If you don't specify the status code, the status code defaults to *302 - Found* , which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="9f643-181">在启用了开发人员工具的浏览器中，向路径为 `/redirect-rule/1234/5678` 的示例应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="9f643-182">正则表达式匹配 `redirect-rule/(.*)` 上的请求路径，且该路径会被 `/redirected/1234/5678` 替代。</span><span class="sxs-lookup"><span data-stu-id="9f643-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="9f643-183">重定向 URL 以“302 (已找到)”状态代码发回客户端。</span><span class="sxs-lookup"><span data-stu-id="9f643-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="9f643-184">浏览器会在浏览器地址栏中出现的重定向 URL 上发出新请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="9f643-185">由于示例应用中的规则都不匹配重定向 URL：</span><span class="sxs-lookup"><span data-stu-id="9f643-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="9f643-186">第二个请求从应用程序收到“200 (正常)”响应。</span><span class="sxs-lookup"><span data-stu-id="9f643-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="9f643-187">响应正文显示了重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="9f643-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="9f643-188">重定向 URL 时，系统将向服务器进行一次往返。</span><span class="sxs-lookup"><span data-stu-id="9f643-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="9f643-189">建立重定向规则时务必小心。</span><span class="sxs-lookup"><span data-stu-id="9f643-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="9f643-190">系统会根据应用的每个请求（包括重定向后的请求）对重定向规则进行评估。</span><span class="sxs-lookup"><span data-stu-id="9f643-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="9f643-191">很容易便会意外创建无限重定向循环。</span><span class="sxs-lookup"><span data-stu-id="9f643-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="9f643-192">原始请求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="9f643-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="9f643-194">括号内的表达式部分称为“捕获组”。</span><span class="sxs-lookup"><span data-stu-id="9f643-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="9f643-195">表达式的点 (`.`) 表示匹配任何字符。</span><span class="sxs-lookup"><span data-stu-id="9f643-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="9f643-196">星号 (`*`) 表示零次或多次匹配前面的字符。</span><span class="sxs-lookup"><span data-stu-id="9f643-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="9f643-197">因此，URL 的最后两个路径段 `1234/5678` 由捕获组 `(.*)` 捕获。</span><span class="sxs-lookup"><span data-stu-id="9f643-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="9f643-198">在请求 URL 中提供的位于 `redirect-rule/` 之后的任何值均由此单个捕获组捕获。</span><span class="sxs-lookup"><span data-stu-id="9f643-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="9f643-199">在替换字符串中，将捕获组注入带有美元符号 (`$`)、后跟捕获序列号的字符串中。</span><span class="sxs-lookup"><span data-stu-id="9f643-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="9f643-200">获取的第一个捕获组值为 `$1`，第二个为 `$2`，并且正则表达式中的其他捕获组值将依次继续排列。</span><span class="sxs-lookup"><span data-stu-id="9f643-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="9f643-201">示例应用的重定向规则正则表达式中只有一个捕获组，因此替换字符串中只有一个注入组，即 `$1`。</span><span class="sxs-lookup"><span data-stu-id="9f643-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="9f643-202">如果应用此规则，URL 将变为 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="9f643-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="9f643-203">URL 重定向到安全的终结点</span><span class="sxs-lookup"><span data-stu-id="9f643-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="9f643-204">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 将 HTTP 请求重定向到采用 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="9f643-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="9f643-205">如不提供状态代码，则中间件默认为“302(已找到)”。</span><span class="sxs-lookup"><span data-stu-id="9f643-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="9f643-206">如果不提供端口：</span><span class="sxs-lookup"><span data-stu-id="9f643-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="9f643-207">中间件默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="9f643-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="9f643-208">方案更改为 `https`（HTTPS 协议），客户端访问端口 443 上的资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="9f643-209">下面的示例演示如何将状态代码设置为“301(永久移动)”并将端口更改为 5001。</span><span class="sxs-lookup"><span data-stu-id="9f643-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="9f643-210">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 将不安全的请求重定向到端口 443 上的采用安全 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="9f643-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="9f643-211">中间件将状态代码设置为“301 (永久移动)”。</span><span class="sxs-lookup"><span data-stu-id="9f643-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="9f643-212">当重定向到安全的终结点并且不需要其他重定向规则时，建议使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="9f643-213">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl#require-https)主题。</span><span class="sxs-lookup"><span data-stu-id="9f643-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="9f643-214">示例应用能够演示如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="9f643-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="9f643-215">将扩展方法添加到 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="9f643-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="9f643-216">在任何 URL 上向应用发出不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="9f643-217">消除自签名证书不受信任的浏览器安全警告，或创建例外以信任证书。</span><span class="sxs-lookup"><span data-stu-id="9f643-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="9f643-218">使用 `AddRedirectToHttps(301, 5001)` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="9f643-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="9f643-220">使用 `AddRedirectToHttpsPermanent` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="9f643-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="9f643-222">URL 重写</span><span class="sxs-lookup"><span data-stu-id="9f643-222">URL rewrite</span></span>

<span data-ttu-id="9f643-223">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 创建重写 URL 的规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="9f643-224">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="9f643-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="9f643-225">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="9f643-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="9f643-226">第三个参数 `skipRemainingRules: {true|false}` 指示如果当前规则适用，中间件是否要跳过其他重写规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="9f643-227">原始请求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="9f643-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="9f643-229">表达式开头的脱字号 (`^`) 意味着匹配从 URL 路径的开头处开始。</span><span class="sxs-lookup"><span data-stu-id="9f643-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="9f643-230">在前面的重定向规则 `redirect-rule/(.*)` 的示例中，正则表达式的开头没有脱字号 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="9f643-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="9f643-231">因此，路径中 `redirect-rule/` 之前的任何字符都可能成功匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="9f643-232">路径</span><span class="sxs-lookup"><span data-stu-id="9f643-232">Path</span></span>                               | <span data-ttu-id="9f643-233">匹配</span><span class="sxs-lookup"><span data-stu-id="9f643-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="9f643-234">是</span><span class="sxs-lookup"><span data-stu-id="9f643-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="9f643-235">是</span><span class="sxs-lookup"><span data-stu-id="9f643-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="9f643-236">是</span><span class="sxs-lookup"><span data-stu-id="9f643-236">Yes</span></span>   |

<span data-ttu-id="9f643-237">重写规则 `^rewrite-rule/(\d+)/(\d+)` 只能与以 `rewrite-rule/` 开头的路径匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="9f643-238">注意下表中的匹配差异。</span><span class="sxs-lookup"><span data-stu-id="9f643-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="9f643-239">路径</span><span class="sxs-lookup"><span data-stu-id="9f643-239">Path</span></span>                              | <span data-ttu-id="9f643-240">匹配</span><span class="sxs-lookup"><span data-stu-id="9f643-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="9f643-241">是</span><span class="sxs-lookup"><span data-stu-id="9f643-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="9f643-242">否</span><span class="sxs-lookup"><span data-stu-id="9f643-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="9f643-243">否</span><span class="sxs-lookup"><span data-stu-id="9f643-243">No</span></span>    |

<span data-ttu-id="9f643-244">在表达式的 `^rewrite-rule/` 部分之后，有两个捕获组 `(\d+)/(\d+)`。</span><span class="sxs-lookup"><span data-stu-id="9f643-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="9f643-245">`\d` 表示与数字匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="9f643-246">加号 (`+`) 表示与前面的一个或多个字符匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="9f643-247">因此，URL 必须包含数字加正斜杠加另一个数字的形式。</span><span class="sxs-lookup"><span data-stu-id="9f643-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="9f643-248">这些捕获组以 `$1` 和 `$2` 的形式注入重写 URL 中。</span><span class="sxs-lookup"><span data-stu-id="9f643-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="9f643-249">重写规则替换字符串将捕获组放入查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="9f643-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="9f643-250">重写 `/rewrite-rule/1234/5678` 的请求路径，获取 `/rewritten?var1=1234&var2=5678` 处的资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="9f643-251">如果原始请求中存在查询字符串，则重写 URL 时会保留此字符串。</span><span class="sxs-lookup"><span data-stu-id="9f643-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="9f643-252">无需往返服务器来获取资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="9f643-253">如果资源存在，系统会提取资源并以“200（正常）”状态代码返回给客户端。</span><span class="sxs-lookup"><span data-stu-id="9f643-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="9f643-254">因为客户端不会被重定向，所以浏览器地址栏中的 URL 不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="9f643-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="9f643-255">客户端无法检测到服务器上发生的 URL 重写操作。</span><span class="sxs-lookup"><span data-stu-id="9f643-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="9f643-256">尽可能使用 `skipRemainingRules: true`，因为匹配规则在计算上很昂贵并且增加了应用响应时间。</span><span class="sxs-lookup"><span data-stu-id="9f643-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="9f643-257">对于最快的应用响应：</span><span class="sxs-lookup"><span data-stu-id="9f643-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="9f643-258">按照从最频繁匹配的规则到最不频繁匹配的规则排列重写规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="9f643-259">如果出现匹配项且无需处理任何其他规则，则跳过剩余规则的处理。</span><span class="sxs-lookup"><span data-stu-id="9f643-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="9f643-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="9f643-260">Apache mod_rewrite</span></span>

<span data-ttu-id="9f643-261">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 应用 Apache mod_rewrite 规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="9f643-262">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="9f643-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="9f643-263">有关 mod_rewrite 规则的详细信息和示例，请参阅 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="9f643-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="9f643-264"><xref:System.IO.StreamReader> 用于读取 ApacheModRewrite.txt 规则文件中的规则：</span><span class="sxs-lookup"><span data-stu-id="9f643-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="9f643-265">示例应用将请求从 `/apache-mod-rules-redirect/(.\*)` 重定向到 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="9f643-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="9f643-266">响应状态代码为“302 (已找到)”。</span><span class="sxs-lookup"><span data-stu-id="9f643-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="9f643-267">原始请求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="9f643-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="9f643-269">中间件支持下列 Apache mod_rewrite 服务器变量：</span><span class="sxs-lookup"><span data-stu-id="9f643-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="9f643-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="9f643-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="9f643-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="9f643-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="9f643-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="9f643-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="9f643-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="9f643-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="9f643-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="9f643-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="9f643-275">HTTP_HOST</span></span>
* <span data-ttu-id="9f643-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="9f643-276">HTTP_REFERER</span></span>
* <span data-ttu-id="9f643-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="9f643-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="9f643-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9f643-278">HTTPS</span></span>
* <span data-ttu-id="9f643-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="9f643-279">IPV6</span></span>
* <span data-ttu-id="9f643-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="9f643-280">QUERY_STRING</span></span>
* <span data-ttu-id="9f643-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="9f643-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="9f643-282">REMOTE_PORT</span></span>
* <span data-ttu-id="9f643-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9f643-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="9f643-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="9f643-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="9f643-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="9f643-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="9f643-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="9f643-286">REQUEST_URI</span></span>
* <span data-ttu-id="9f643-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9f643-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="9f643-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-288">SERVER_ADDR</span></span>
* <span data-ttu-id="9f643-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="9f643-289">SERVER_PORT</span></span>
* <span data-ttu-id="9f643-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="9f643-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="9f643-291">TIME</span><span class="sxs-lookup"><span data-stu-id="9f643-291">TIME</span></span>
* <span data-ttu-id="9f643-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="9f643-292">TIME_DAY</span></span>
* <span data-ttu-id="9f643-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="9f643-293">TIME_HOUR</span></span>
* <span data-ttu-id="9f643-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="9f643-294">TIME_MIN</span></span>
* <span data-ttu-id="9f643-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="9f643-295">TIME_MON</span></span>
* <span data-ttu-id="9f643-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="9f643-296">TIME_SEC</span></span>
* <span data-ttu-id="9f643-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="9f643-297">TIME_WDAY</span></span>
* <span data-ttu-id="9f643-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="9f643-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="9f643-299">IIS URL 重写模块规则</span><span class="sxs-lookup"><span data-stu-id="9f643-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="9f643-300">若要使用适用于 IIS URL 重写模块的同一规则集，使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="9f643-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="9f643-301">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="9f643-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="9f643-302">当在 Windows Server IIS 上运行时，请勿指示中间件使用应用的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="9f643-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="9f643-303">使用 IIS 时，应将这些规则存储在应用的 web.config 文件之外，以避免与 IIS 重写模块发生冲突。</span><span class="sxs-lookup"><span data-stu-id="9f643-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="9f643-304">有关 IIS URL 重写模块规则的详细信息和示例，请参阅 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)（使用 URL 重写模块 2.0）和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）。</span><span class="sxs-lookup"><span data-stu-id="9f643-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="9f643-305"><xref:System.IO.StreamReader> 用于读取 IISUrlRewrite.xml 规则文件中的规则：</span><span class="sxs-lookup"><span data-stu-id="9f643-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="9f643-306">示例应用将请求从 `/iis-rules-rewrite/(.*)` 重写为 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="9f643-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="9f643-307">以“200 (正常)”状态代码作为响应发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="9f643-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="9f643-308">原始请求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="9f643-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="9f643-310">如果有配置了服务器级别规则（可对应用产生不利影响）的活动 IIS 重写模块，则可禁用应用的 IIS 重写模块。</span><span class="sxs-lookup"><span data-stu-id="9f643-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="9f643-311">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="9f643-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="9f643-312">不支持的功能</span><span class="sxs-lookup"><span data-stu-id="9f643-312">Unsupported features</span></span>

<span data-ttu-id="9f643-313">与 ASP.NET Core 2.x 一同发布的中间件不支持以下 IIS URL 重写模块功能：</span><span class="sxs-lookup"><span data-stu-id="9f643-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="9f643-314">出站规则</span><span class="sxs-lookup"><span data-stu-id="9f643-314">Outbound Rules</span></span>
* <span data-ttu-id="9f643-315">自定义服务器变量</span><span class="sxs-lookup"><span data-stu-id="9f643-315">Custom Server Variables</span></span>
* <span data-ttu-id="9f643-316">通配符</span><span class="sxs-lookup"><span data-stu-id="9f643-316">Wildcards</span></span>
* <span data-ttu-id="9f643-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="9f643-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="9f643-318">受支持的服务器变量</span><span class="sxs-lookup"><span data-stu-id="9f643-318">Supported server variables</span></span>

<span data-ttu-id="9f643-319">中间件支持下列 IIS URL 重写模块服务器变量：</span><span class="sxs-lookup"><span data-stu-id="9f643-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="9f643-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="9f643-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="9f643-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="9f643-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="9f643-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="9f643-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="9f643-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="9f643-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="9f643-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="9f643-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="9f643-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="9f643-325">HTTP_HOST</span></span>
* <span data-ttu-id="9f643-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="9f643-326">HTTP_REFERER</span></span>
* <span data-ttu-id="9f643-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="9f643-327">HTTP_URL</span></span>
* <span data-ttu-id="9f643-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="9f643-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="9f643-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9f643-329">HTTPS</span></span>
* <span data-ttu-id="9f643-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="9f643-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="9f643-331">QUERY_STRING</span></span>
* <span data-ttu-id="9f643-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="9f643-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="9f643-333">REMOTE_PORT</span></span>
* <span data-ttu-id="9f643-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9f643-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="9f643-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="9f643-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="9f643-336">也可通过 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 获取 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="9f643-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="9f643-337">这种方法可为重写规则文件的位置提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="9f643-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="9f643-338">请确保将重写规则文件部署到所提供路径的服务器中。</span><span class="sxs-lookup"><span data-stu-id="9f643-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="9f643-339">基于方法的规则</span><span class="sxs-lookup"><span data-stu-id="9f643-339">Method-based rule</span></span>

<span data-ttu-id="9f643-340">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在方法中实现自己的规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="9f643-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="9f643-341">`Add` 公开 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，这使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用于方法中。</span><span class="sxs-lookup"><span data-stu-id="9f643-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="9f643-342">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) 决定如何处理其他管道进程。</span><span class="sxs-lookup"><span data-stu-id="9f643-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="9f643-343">将值设置为下表中的 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 字段之一。</span><span class="sxs-lookup"><span data-stu-id="9f643-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="9f643-344">重写上下文结果</span><span class="sxs-lookup"><span data-stu-id="9f643-344">Rewrite context result</span></span>               | <span data-ttu-id="9f643-345">操作</span><span class="sxs-lookup"><span data-stu-id="9f643-345">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="9f643-346">`RuleResult.ContinueRules`（默认值）</span><span class="sxs-lookup"><span data-stu-id="9f643-346">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="9f643-347">继续应用规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-347">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="9f643-348">停止应用规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="9f643-348">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="9f643-349">停止应用规则并将上下文发送给下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-349">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="9f643-350">示例应用演示了如何对以 .xml 结尾的路径的请求进行重定向。</span><span class="sxs-lookup"><span data-stu-id="9f643-350">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="9f643-351">如果发出针对 `/file.xml` 的请求，请求将重定向到 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="9f643-351">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="9f643-352">状态代码设置为“301 (永久移动)”。</span><span class="sxs-lookup"><span data-stu-id="9f643-352">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="9f643-353">当浏览器发出针对 /xmlfiles/file.xml 的新请求后，静态文件中间件会将文件从 wwwroot / xmlfiles 文件夹提供给客户端 。</span><span class="sxs-lookup"><span data-stu-id="9f643-353">When the browser makes a new request for */xmlfiles/file.xml* , Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="9f643-354">对于重定向，请显式设置响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-354">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="9f643-355">否则，将会返回“200 (正常)”状态代码，且客户端上不会发生重写。</span><span class="sxs-lookup"><span data-stu-id="9f643-355">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="9f643-356">*RewriteRules.cs* :</span><span class="sxs-lookup"><span data-stu-id="9f643-356">*RewriteRules.cs* :</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="9f643-357">此方法还可以重写请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-357">This approach can also rewrite requests.</span></span> <span data-ttu-id="9f643-358">示例应用演示了如何重写任何文本文件请求的路径以从 wwwroot 文件夹中提供 file.txt 文本文件 。</span><span class="sxs-lookup"><span data-stu-id="9f643-358">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="9f643-359">静态文件中间件基于更新的请求路径来提供文件：</span><span class="sxs-lookup"><span data-stu-id="9f643-359">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="9f643-360">*RewriteRules.cs* :</span><span class="sxs-lookup"><span data-stu-id="9f643-360">*RewriteRules.cs* :</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="9f643-361">基于 IRule 的规则</span><span class="sxs-lookup"><span data-stu-id="9f643-361">IRule-based rule</span></span>

<span data-ttu-id="9f643-362">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在实现 <xref:Microsoft.AspNetCore.Rewrite.IRule> 接口的类中使用规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="9f643-362">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="9f643-363">与使用基于方法的规则方法相比，`IRule` 提供了更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="9f643-363">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="9f643-364">实现类可能包含构造函数，可在其中传入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的参数。</span><span class="sxs-lookup"><span data-stu-id="9f643-364">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="9f643-365">检查示例应用中 `extension` 和 `newPath` 的参数值是否符合多个条件。</span><span class="sxs-lookup"><span data-stu-id="9f643-365">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="9f643-366">`extension` 须包含一个值，并且该值必须是 .png、.jpg 或 .gif  。</span><span class="sxs-lookup"><span data-stu-id="9f643-366">The `extension` must contain a value, and the value must be *.png* , *.jpg* , or *.gif*.</span></span> <span data-ttu-id="9f643-367">如果 `newPath` 无效，则会引发 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="9f643-367">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="9f643-368">如果发出针对 image.png 的请求，请求将重定向到 `/png-images/image.png`。</span><span class="sxs-lookup"><span data-stu-id="9f643-368">If a request is made for *image.png* , the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="9f643-369">如果发出针对 image.png 的请求，请求将重定向到 `/jpg-images/image.jpg`。</span><span class="sxs-lookup"><span data-stu-id="9f643-369">If a request is made for *image.jpg* , the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="9f643-370">状态代码设置为“301 (永久移动)”，`context.Result` 设置为停止处理规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="9f643-370">The status code is set to *301 - Moved Permanently* , and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="9f643-371">原始请求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="9f643-371">Original Request: `/image.png`</span></span>

![开发人员工具正跟踪 image.png 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="9f643-373">原始请求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="9f643-373">Original Request: `/image.jpg`</span></span>

![开发人员工具正跟踪 image.jpg 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="9f643-375">正则表达式示例</span><span class="sxs-lookup"><span data-stu-id="9f643-375">Regex examples</span></span>

| <span data-ttu-id="9f643-376">目标</span><span class="sxs-lookup"><span data-stu-id="9f643-376">Goal</span></span> | <span data-ttu-id="9f643-377">正则表达式字符串和</span><span class="sxs-lookup"><span data-stu-id="9f643-377">Regex String &</span></span><br><span data-ttu-id="9f643-378">匹配示例</span><span class="sxs-lookup"><span data-stu-id="9f643-378">Match Example</span></span> | <span data-ttu-id="9f643-379">替换字符串和</span><span class="sxs-lookup"><span data-stu-id="9f643-379">Replacement String &</span></span><br><span data-ttu-id="9f643-380">输出示例</span><span class="sxs-lookup"><span data-stu-id="9f643-380">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="9f643-381">将路径重写为查询字符串</span><span class="sxs-lookup"><span data-stu-id="9f643-381">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="9f643-382">去掉尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="9f643-382">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="9f643-383">强制添加尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="9f643-383">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="9f643-384">避免重写特定请求</span><span class="sxs-lookup"><span data-stu-id="9f643-384">Avoid rewriting specific requests</span></span> | <span data-ttu-id="9f643-385">`^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="9f643-385">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="9f643-386">正确：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="9f643-386">Yes: `/resource.htm`</span></span><br><span data-ttu-id="9f643-387">错误：`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="9f643-387">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="9f643-388">重新排列 URL 段</span><span class="sxs-lookup"><span data-stu-id="9f643-388">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="9f643-389">替换 URL 段</span><span class="sxs-lookup"><span data-stu-id="9f643-389">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f643-390">本文档介绍 URL 重写并说明如何在 ASP.NET Core 应用中使用 URL 重写中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-390">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="9f643-391">URL 重写是根据一个或多个预定义规则修改请求 URL 的行为。</span><span class="sxs-lookup"><span data-stu-id="9f643-391">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="9f643-392">URL 重写会在资源位置和地址之间创建一个抽象，使位置和地址不紧密相连。</span><span class="sxs-lookup"><span data-stu-id="9f643-392">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="9f643-393">在以下几种方案中，URL 重写很有价值：</span><span class="sxs-lookup"><span data-stu-id="9f643-393">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="9f643-394">暂时或永久移动或替换服务器资源，并维护这些资源的稳定定位符。</span><span class="sxs-lookup"><span data-stu-id="9f643-394">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="9f643-395">拆分在不同应用或同一应用的不同区域中处理的请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-395">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="9f643-396">删除、添加或重新组织传入请求上的 URL 段。</span><span class="sxs-lookup"><span data-stu-id="9f643-396">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="9f643-397">优化搜索引擎优化 (SEO) 的公共 URL。</span><span class="sxs-lookup"><span data-stu-id="9f643-397">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="9f643-398">允许使用友好的公共 URL 来帮助访问者预测请求资源后返回的内容。</span><span class="sxs-lookup"><span data-stu-id="9f643-398">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="9f643-399">将不安全请求重定向到安全终结点。</span><span class="sxs-lookup"><span data-stu-id="9f643-399">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="9f643-400">防止热链接，外部站点会通过热链接将其他站点的资产链接到其自己的内容，从而利用托管在其他站点上的静态资产。</span><span class="sxs-lookup"><span data-stu-id="9f643-400">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="9f643-401">URL 重写可能会降低应用的性能。</span><span class="sxs-lookup"><span data-stu-id="9f643-401">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="9f643-402">如果可行，应限制规则的数量和复杂度。</span><span class="sxs-lookup"><span data-stu-id="9f643-402">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="9f643-403">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9f643-403">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="9f643-404">URL 重定向和 URL 重写</span><span class="sxs-lookup"><span data-stu-id="9f643-404">URL redirect and URL rewrite</span></span>

<span data-ttu-id="9f643-405">URL 重定向和 URL 重写之间的用词差异很细微，但这对于向客户端提供资源具有重要意义 。</span><span class="sxs-lookup"><span data-stu-id="9f643-405">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="9f643-406">ASP.NET Core 的 URL 重写中间件能够满足两者的需求。</span><span class="sxs-lookup"><span data-stu-id="9f643-406">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="9f643-407">URL 重定向涉及客户端操作，指示客户端访问与客户端最初请求地址不同的资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-407">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="9f643-408">这需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="9f643-408">This requires a round trip to the server.</span></span> <span data-ttu-id="9f643-409">客户端对资源发出新请求时，返回客户端的重定向 URL 会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="9f643-409">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="9f643-410">如果 `/resource` 被重定向到 `/different-resource`，则服务器作出响应，指示客户端应在 `/different-resource` 获取资源，所提供的状态代码指示重定向是临时的还是永久的。</span><span class="sxs-lookup"><span data-stu-id="9f643-410">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 服务终结点已暂时从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="9f643-416">将请求重定向到不同 URL 时，通过使用响应指定状态代码来指示重定向是永久还是临时：</span><span class="sxs-lookup"><span data-stu-id="9f643-416">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="9f643-417">如果资源有一个新的永久性 URL，并且你希望指示客户端所有将来的资源请求都使用新 URL，则使用“301 (永久移动)”状态代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-417">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="9f643-418">收到 301 状态代码时，客户端可能会缓存响应并重用这段代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-418">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="9f643-419">“302 (找到)”状态代码用于后列情况：重定向操作是临时的或通常会发生变化。</span><span class="sxs-lookup"><span data-stu-id="9f643-419">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="9f643-420">302 状态代码向客户端指示不存储 URL 并在将来使用。</span><span class="sxs-lookup"><span data-stu-id="9f643-420">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="9f643-421">有关状态代码的详细信息，请参阅 [RFC 2616：状态代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="9f643-421">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="9f643-422">URL 重写是服务器端操作，它从与客户端请求的资源地址不同的资源地址提供资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-422">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="9f643-423">重写 URL 不需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="9f643-423">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="9f643-424">重写的 URL 不会返回客户端，也不会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="9f643-424">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="9f643-425">如果 `/resource` 重写到 `/different-resource`，服务器会在内部提取并返回 `/different-resource` 处的资源 。</span><span class="sxs-lookup"><span data-stu-id="9f643-425">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="9f643-426">尽管客户端可能能够检索已重写 URL 处的资源，但是，客户端发出请求并收到响应时，并不知道已重写 URL 处存在的资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-426">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 服务终结点已从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="9f643-431">URL 重写示例应用</span><span class="sxs-lookup"><span data-stu-id="9f643-431">URL rewriting sample app</span></span>

<span data-ttu-id="9f643-432">可使用[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)了解 URL 重写中间件的功能。</span><span class="sxs-lookup"><span data-stu-id="9f643-432">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="9f643-433">该应用程序应用重定向和重写规则，并显示多个方案的重定向或重写的 URL。</span><span class="sxs-lookup"><span data-stu-id="9f643-433">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="9f643-434">何时使用 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="9f643-434">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="9f643-435">如果无法使用以下方法，请使用 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="9f643-435">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="9f643-436">在 Windows Server 上使用带 IIS 的 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="9f643-436">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="9f643-437">在 Apache 服务器上使用 Apache mod_rewrite 模块</span><span class="sxs-lookup"><span data-stu-id="9f643-437">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="9f643-438">Nginx 上的 URL 重写</span><span class="sxs-lookup"><span data-stu-id="9f643-438">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="9f643-439">此外，如果应用程序在 [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（旧称 WebListener）上托管，请使用中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-439">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="9f643-440">使用 IIS、Apache 和 Nginx 中的基于服务器的 URL 重写技术的主要原因：</span><span class="sxs-lookup"><span data-stu-id="9f643-440">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="9f643-441">中间件不支持这些模块的完整功能。</span><span class="sxs-lookup"><span data-stu-id="9f643-441">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="9f643-442">服务器模块的一些功能不适用于 ASP.NET Core 项目，例如 IIS 重写模块的 `IsFile` 和 `IsDirectory` 约束。</span><span class="sxs-lookup"><span data-stu-id="9f643-442">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="9f643-443">在这些情况下，请改为使用中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-443">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="9f643-444">中间件性能与模块性能不匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-444">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="9f643-445">基准测试是确定哪种方法会最大程度降低性能或降低的性能是否可忽略不计的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="9f643-445">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="9f643-446">Package</span><span class="sxs-lookup"><span data-stu-id="9f643-446">Package</span></span>

<span data-ttu-id="9f643-447">要在项目中包含中间件，请在项目文件中添加对 [Microsoft.AspNetCore.App 元数据包](xref:fundamentals/metapackage-app)的包引用，该文件包含 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 包。</span><span class="sxs-lookup"><span data-stu-id="9f643-447">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="9f643-448">不使用 `Microsoft.AspNetCore.App` 元包时，向 `Microsoft.AspNetCore.Rewrite` 包添加项目引用。</span><span class="sxs-lookup"><span data-stu-id="9f643-448">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="9f643-449">扩展和选项</span><span class="sxs-lookup"><span data-stu-id="9f643-449">Extension and options</span></span>

<span data-ttu-id="9f643-450">通过使用扩展方法为每条重写规则创建 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 类的实例，建立 URL 重写和重写定向规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-450">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="9f643-451">按所需的处理顺序链接多个规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-451">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="9f643-452">使用 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 将 `RewriteOptions` 添加到请求管道时，它会被传递到 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="9f643-452">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="9f643-453">将非 www 重定向到 www</span><span class="sxs-lookup"><span data-stu-id="9f643-453">Redirect non-www to www</span></span>

<span data-ttu-id="9f643-454">三个选项允许应用将非 `www` 重新定向到 `www`：</span><span class="sxs-lookup"><span data-stu-id="9f643-454">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="9f643-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>：如果请求是非 `www`，则将请求永久重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="9f643-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="9f643-456">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="9f643-456">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="9f643-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>：如果传入请求是非 `www`，则将请求重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="9f643-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="9f643-458">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="9f643-458">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="9f643-459">重载允许提供响应状态代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-459">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="9f643-460">使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 类的字段实现状态代码分配。</span><span class="sxs-lookup"><span data-stu-id="9f643-460">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="9f643-461">URL 重定向</span><span class="sxs-lookup"><span data-stu-id="9f643-461">URL redirect</span></span>

<span data-ttu-id="9f643-462">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 将请求重定向。</span><span class="sxs-lookup"><span data-stu-id="9f643-462">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="9f643-463">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="9f643-463">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="9f643-464">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="9f643-464">The second parameter is the replacement string.</span></span> <span data-ttu-id="9f643-465">第三个参数（如有）指定状态代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-465">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="9f643-466">如不指定状态代码，则状态代码默认为“302 (已找到)”，指示资源暂时移动或替换。</span><span class="sxs-lookup"><span data-stu-id="9f643-466">If you don't specify the status code, the status code defaults to *302 - Found* , which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="9f643-467">在启用了开发人员工具的浏览器中，向路径为 `/redirect-rule/1234/5678` 的示例应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-467">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="9f643-468">正则表达式匹配 `redirect-rule/(.*)` 上的请求路径，且该路径会被 `/redirected/1234/5678` 替代。</span><span class="sxs-lookup"><span data-stu-id="9f643-468">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="9f643-469">重定向 URL 以“302 (已找到)”状态代码发回客户端。</span><span class="sxs-lookup"><span data-stu-id="9f643-469">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="9f643-470">浏览器会在浏览器地址栏中出现的重定向 URL 上发出新请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-470">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="9f643-471">由于示例应用中的规则都不匹配重定向 URL：</span><span class="sxs-lookup"><span data-stu-id="9f643-471">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="9f643-472">第二个请求从应用程序收到“200 (正常)”响应。</span><span class="sxs-lookup"><span data-stu-id="9f643-472">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="9f643-473">响应正文显示了重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="9f643-473">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="9f643-474">重定向 URL 时，系统将向服务器进行一次往返。</span><span class="sxs-lookup"><span data-stu-id="9f643-474">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="9f643-475">建立重定向规则时务必小心。</span><span class="sxs-lookup"><span data-stu-id="9f643-475">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="9f643-476">系统会根据应用的每个请求（包括重定向后的请求）对重定向规则进行评估。</span><span class="sxs-lookup"><span data-stu-id="9f643-476">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="9f643-477">很容易便会意外创建无限重定向循环。</span><span class="sxs-lookup"><span data-stu-id="9f643-477">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="9f643-478">原始请求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="9f643-478">Original Request: `/redirect-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="9f643-480">括号内的表达式部分称为“捕获组”。</span><span class="sxs-lookup"><span data-stu-id="9f643-480">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="9f643-481">表达式的点 (`.`) 表示匹配任何字符。</span><span class="sxs-lookup"><span data-stu-id="9f643-481">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="9f643-482">星号 (`*`) 表示零次或多次匹配前面的字符。</span><span class="sxs-lookup"><span data-stu-id="9f643-482">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="9f643-483">因此，URL 的最后两个路径段 `1234/5678` 由捕获组 `(.*)` 捕获。</span><span class="sxs-lookup"><span data-stu-id="9f643-483">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="9f643-484">在请求 URL 中提供的位于 `redirect-rule/` 之后的任何值均由此单个捕获组捕获。</span><span class="sxs-lookup"><span data-stu-id="9f643-484">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="9f643-485">在替换字符串中，将捕获组注入带有美元符号 (`$`)、后跟捕获序列号的字符串中。</span><span class="sxs-lookup"><span data-stu-id="9f643-485">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="9f643-486">获取的第一个捕获组值为 `$1`，第二个为 `$2`，并且正则表达式中的其他捕获组值将依次继续排列。</span><span class="sxs-lookup"><span data-stu-id="9f643-486">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="9f643-487">示例应用的重定向规则正则表达式中只有一个捕获组，因此替换字符串中只有一个注入组，即 `$1`。</span><span class="sxs-lookup"><span data-stu-id="9f643-487">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="9f643-488">如果应用此规则，URL 将变为 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="9f643-488">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="9f643-489">URL 重定向到安全的终结点</span><span class="sxs-lookup"><span data-stu-id="9f643-489">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="9f643-490">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 将 HTTP 请求重定向到采用 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="9f643-490">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="9f643-491">如不提供状态代码，则中间件默认为“302(已找到)”。</span><span class="sxs-lookup"><span data-stu-id="9f643-491">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="9f643-492">如果不提供端口：</span><span class="sxs-lookup"><span data-stu-id="9f643-492">If the port isn't supplied:</span></span>

* <span data-ttu-id="9f643-493">中间件默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="9f643-493">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="9f643-494">方案更改为 `https`（HTTPS 协议），客户端访问端口 443 上的资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-494">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="9f643-495">下面的示例演示如何将状态代码设置为“301(永久移动)”并将端口更改为 5001。</span><span class="sxs-lookup"><span data-stu-id="9f643-495">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="9f643-496">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 将不安全的请求重定向到端口 443 上的采用安全 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="9f643-496">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="9f643-497">中间件将状态代码设置为“301 (永久移动)”。</span><span class="sxs-lookup"><span data-stu-id="9f643-497">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="9f643-498">当重定向到安全的终结点并且不需要其他重定向规则时，建议使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-498">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="9f643-499">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl#require-https)主题。</span><span class="sxs-lookup"><span data-stu-id="9f643-499">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="9f643-500">示例应用能够演示如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="9f643-500">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="9f643-501">将扩展方法添加到 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="9f643-501">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="9f643-502">在任何 URL 上向应用发出不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-502">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="9f643-503">消除自签名证书不受信任的浏览器安全警告，或创建例外以信任证书。</span><span class="sxs-lookup"><span data-stu-id="9f643-503">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="9f643-504">使用 `AddRedirectToHttps(301, 5001)` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="9f643-504">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="9f643-506">使用 `AddRedirectToHttpsPermanent` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="9f643-506">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="9f643-508">URL 重写</span><span class="sxs-lookup"><span data-stu-id="9f643-508">URL rewrite</span></span>

<span data-ttu-id="9f643-509">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 创建重写 URL 的规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-509">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="9f643-510">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="9f643-510">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="9f643-511">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="9f643-511">The second parameter is the replacement string.</span></span> <span data-ttu-id="9f643-512">第三个参数 `skipRemainingRules: {true|false}` 指示如果当前规则适用，中间件是否要跳过其他重写规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-512">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="9f643-513">原始请求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="9f643-513">Original Request: `/rewrite-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="9f643-515">表达式开头的脱字号 (`^`) 意味着匹配从 URL 路径的开头处开始。</span><span class="sxs-lookup"><span data-stu-id="9f643-515">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="9f643-516">在前面的重定向规则 `redirect-rule/(.*)` 的示例中，正则表达式的开头没有脱字号 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="9f643-516">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="9f643-517">因此，路径中 `redirect-rule/` 之前的任何字符都可能成功匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-517">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="9f643-518">路径</span><span class="sxs-lookup"><span data-stu-id="9f643-518">Path</span></span>                               | <span data-ttu-id="9f643-519">匹配</span><span class="sxs-lookup"><span data-stu-id="9f643-519">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="9f643-520">是</span><span class="sxs-lookup"><span data-stu-id="9f643-520">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="9f643-521">是</span><span class="sxs-lookup"><span data-stu-id="9f643-521">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="9f643-522">是</span><span class="sxs-lookup"><span data-stu-id="9f643-522">Yes</span></span>   |

<span data-ttu-id="9f643-523">重写规则 `^rewrite-rule/(\d+)/(\d+)` 只能与以 `rewrite-rule/` 开头的路径匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-523">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="9f643-524">注意下表中的匹配差异。</span><span class="sxs-lookup"><span data-stu-id="9f643-524">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="9f643-525">路径</span><span class="sxs-lookup"><span data-stu-id="9f643-525">Path</span></span>                              | <span data-ttu-id="9f643-526">匹配</span><span class="sxs-lookup"><span data-stu-id="9f643-526">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="9f643-527">是</span><span class="sxs-lookup"><span data-stu-id="9f643-527">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="9f643-528">否</span><span class="sxs-lookup"><span data-stu-id="9f643-528">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="9f643-529">否</span><span class="sxs-lookup"><span data-stu-id="9f643-529">No</span></span>    |

<span data-ttu-id="9f643-530">在表达式的 `^rewrite-rule/` 部分之后，有两个捕获组 `(\d+)/(\d+)`。</span><span class="sxs-lookup"><span data-stu-id="9f643-530">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="9f643-531">`\d` 表示与数字匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-531">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="9f643-532">加号 (`+`) 表示与前面的一个或多个字符匹配。</span><span class="sxs-lookup"><span data-stu-id="9f643-532">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="9f643-533">因此，URL 必须包含数字加正斜杠加另一个数字的形式。</span><span class="sxs-lookup"><span data-stu-id="9f643-533">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="9f643-534">这些捕获组以 `$1` 和 `$2` 的形式注入重写 URL 中。</span><span class="sxs-lookup"><span data-stu-id="9f643-534">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="9f643-535">重写规则替换字符串将捕获组放入查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="9f643-535">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="9f643-536">重写 `/rewrite-rule/1234/5678` 的请求路径，获取 `/rewritten?var1=1234&var2=5678` 处的资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-536">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="9f643-537">如果原始请求中存在查询字符串，则重写 URL 时会保留此字符串。</span><span class="sxs-lookup"><span data-stu-id="9f643-537">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="9f643-538">无需往返服务器来获取资源。</span><span class="sxs-lookup"><span data-stu-id="9f643-538">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="9f643-539">如果资源存在，系统会提取资源并以“200（正常）”状态代码返回给客户端。</span><span class="sxs-lookup"><span data-stu-id="9f643-539">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="9f643-540">因为客户端不会被重定向，所以浏览器地址栏中的 URL 不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="9f643-540">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="9f643-541">客户端无法检测到服务器上发生的 URL 重写操作。</span><span class="sxs-lookup"><span data-stu-id="9f643-541">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="9f643-542">尽可能使用 `skipRemainingRules: true`，因为匹配规则在计算上很昂贵并且增加了应用响应时间。</span><span class="sxs-lookup"><span data-stu-id="9f643-542">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="9f643-543">对于最快的应用响应：</span><span class="sxs-lookup"><span data-stu-id="9f643-543">For the fastest app response:</span></span>
>
> * <span data-ttu-id="9f643-544">按照从最频繁匹配的规则到最不频繁匹配的规则排列重写规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-544">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="9f643-545">如果出现匹配项且无需处理任何其他规则，则跳过剩余规则的处理。</span><span class="sxs-lookup"><span data-stu-id="9f643-545">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="9f643-546">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="9f643-546">Apache mod_rewrite</span></span>

<span data-ttu-id="9f643-547">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 应用 Apache mod_rewrite 规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-547">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="9f643-548">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="9f643-548">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="9f643-549">有关 mod_rewrite 规则的详细信息和示例，请参阅 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="9f643-549">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="9f643-550"><xref:System.IO.StreamReader> 用于读取 ApacheModRewrite.txt 规则文件中的规则：</span><span class="sxs-lookup"><span data-stu-id="9f643-550">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="9f643-551">示例应用将请求从 `/apache-mod-rules-redirect/(.\*)` 重定向到 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="9f643-551">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="9f643-552">响应状态代码为“302 (已找到)”。</span><span class="sxs-lookup"><span data-stu-id="9f643-552">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="9f643-553">原始请求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="9f643-553">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="9f643-555">中间件支持下列 Apache mod_rewrite 服务器变量：</span><span class="sxs-lookup"><span data-stu-id="9f643-555">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="9f643-556">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-556">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="9f643-557">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="9f643-557">HTTP_ACCEPT</span></span>
* <span data-ttu-id="9f643-558">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="9f643-558">HTTP_CONNECTION</span></span>
* <span data-ttu-id="9f643-559">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="9f643-559">HTTP_COOKIE</span></span>
* <span data-ttu-id="9f643-560">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="9f643-560">HTTP_FORWARDED</span></span>
* <span data-ttu-id="9f643-561">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="9f643-561">HTTP_HOST</span></span>
* <span data-ttu-id="9f643-562">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="9f643-562">HTTP_REFERER</span></span>
* <span data-ttu-id="9f643-563">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="9f643-563">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="9f643-564">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9f643-564">HTTPS</span></span>
* <span data-ttu-id="9f643-565">IPV6</span><span class="sxs-lookup"><span data-stu-id="9f643-565">IPV6</span></span>
* <span data-ttu-id="9f643-566">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="9f643-566">QUERY_STRING</span></span>
* <span data-ttu-id="9f643-567">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-567">REMOTE_ADDR</span></span>
* <span data-ttu-id="9f643-568">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="9f643-568">REMOTE_PORT</span></span>
* <span data-ttu-id="9f643-569">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9f643-569">REQUEST_FILENAME</span></span>
* <span data-ttu-id="9f643-570">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="9f643-570">REQUEST_METHOD</span></span>
* <span data-ttu-id="9f643-571">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="9f643-571">REQUEST_SCHEME</span></span>
* <span data-ttu-id="9f643-572">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="9f643-572">REQUEST_URI</span></span>
* <span data-ttu-id="9f643-573">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9f643-573">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="9f643-574">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-574">SERVER_ADDR</span></span>
* <span data-ttu-id="9f643-575">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="9f643-575">SERVER_PORT</span></span>
* <span data-ttu-id="9f643-576">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="9f643-576">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="9f643-577">TIME</span><span class="sxs-lookup"><span data-stu-id="9f643-577">TIME</span></span>
* <span data-ttu-id="9f643-578">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="9f643-578">TIME_DAY</span></span>
* <span data-ttu-id="9f643-579">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="9f643-579">TIME_HOUR</span></span>
* <span data-ttu-id="9f643-580">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="9f643-580">TIME_MIN</span></span>
* <span data-ttu-id="9f643-581">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="9f643-581">TIME_MON</span></span>
* <span data-ttu-id="9f643-582">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="9f643-582">TIME_SEC</span></span>
* <span data-ttu-id="9f643-583">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="9f643-583">TIME_WDAY</span></span>
* <span data-ttu-id="9f643-584">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="9f643-584">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="9f643-585">IIS URL 重写模块规则</span><span class="sxs-lookup"><span data-stu-id="9f643-585">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="9f643-586">若要使用适用于 IIS URL 重写模块的同一规则集，使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="9f643-586">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="9f643-587">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="9f643-587">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="9f643-588">当在 Windows Server IIS 上运行时，请勿指示中间件使用应用的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="9f643-588">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="9f643-589">使用 IIS 时，应将这些规则存储在应用的 web.config 文件之外，以避免与 IIS 重写模块发生冲突。</span><span class="sxs-lookup"><span data-stu-id="9f643-589">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="9f643-590">有关 IIS URL 重写模块规则的详细信息和示例，请参阅 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)（使用 URL 重写模块 2.0）和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）。</span><span class="sxs-lookup"><span data-stu-id="9f643-590">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="9f643-591"><xref:System.IO.StreamReader> 用于读取 IISUrlRewrite.xml 规则文件中的规则：</span><span class="sxs-lookup"><span data-stu-id="9f643-591">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="9f643-592">示例应用将请求从 `/iis-rules-rewrite/(.*)` 重写为 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="9f643-592">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="9f643-593">以“200 (正常)”状态代码作为响应发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="9f643-593">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="9f643-594">原始请求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="9f643-594">Original Request: `/iis-rules-rewrite/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="9f643-596">如果有配置了服务器级别规则（可对应用产生不利影响）的活动 IIS 重写模块，则可禁用应用的 IIS 重写模块。</span><span class="sxs-lookup"><span data-stu-id="9f643-596">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="9f643-597">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="9f643-597">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="9f643-598">不支持的功能</span><span class="sxs-lookup"><span data-stu-id="9f643-598">Unsupported features</span></span>

<span data-ttu-id="9f643-599">与 ASP.NET Core 2.x 一同发布的中间件不支持以下 IIS URL 重写模块功能：</span><span class="sxs-lookup"><span data-stu-id="9f643-599">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="9f643-600">出站规则</span><span class="sxs-lookup"><span data-stu-id="9f643-600">Outbound Rules</span></span>
* <span data-ttu-id="9f643-601">自定义服务器变量</span><span class="sxs-lookup"><span data-stu-id="9f643-601">Custom Server Variables</span></span>
* <span data-ttu-id="9f643-602">通配符</span><span class="sxs-lookup"><span data-stu-id="9f643-602">Wildcards</span></span>
* <span data-ttu-id="9f643-603">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="9f643-603">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="9f643-604">受支持的服务器变量</span><span class="sxs-lookup"><span data-stu-id="9f643-604">Supported server variables</span></span>

<span data-ttu-id="9f643-605">中间件支持下列 IIS URL 重写模块服务器变量：</span><span class="sxs-lookup"><span data-stu-id="9f643-605">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="9f643-606">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="9f643-606">CONTENT_LENGTH</span></span>
* <span data-ttu-id="9f643-607">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="9f643-607">CONTENT_TYPE</span></span>
* <span data-ttu-id="9f643-608">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="9f643-608">HTTP_ACCEPT</span></span>
* <span data-ttu-id="9f643-609">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="9f643-609">HTTP_CONNECTION</span></span>
* <span data-ttu-id="9f643-610">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="9f643-610">HTTP_COOKIE</span></span>
* <span data-ttu-id="9f643-611">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="9f643-611">HTTP_HOST</span></span>
* <span data-ttu-id="9f643-612">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="9f643-612">HTTP_REFERER</span></span>
* <span data-ttu-id="9f643-613">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="9f643-613">HTTP_URL</span></span>
* <span data-ttu-id="9f643-614">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="9f643-614">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="9f643-615">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9f643-615">HTTPS</span></span>
* <span data-ttu-id="9f643-616">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-616">LOCAL_ADDR</span></span>
* <span data-ttu-id="9f643-617">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="9f643-617">QUERY_STRING</span></span>
* <span data-ttu-id="9f643-618">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="9f643-618">REMOTE_ADDR</span></span>
* <span data-ttu-id="9f643-619">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="9f643-619">REMOTE_PORT</span></span>
* <span data-ttu-id="9f643-620">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="9f643-620">REQUEST_FILENAME</span></span>
* <span data-ttu-id="9f643-621">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="9f643-621">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="9f643-622">也可通过 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 获取 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="9f643-622">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="9f643-623">这种方法可为重写规则文件的位置提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="9f643-623">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="9f643-624">请确保将重写规则文件部署到所提供路径的服务器中。</span><span class="sxs-lookup"><span data-stu-id="9f643-624">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="9f643-625">基于方法的规则</span><span class="sxs-lookup"><span data-stu-id="9f643-625">Method-based rule</span></span>

<span data-ttu-id="9f643-626">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在方法中实现自己的规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="9f643-626">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="9f643-627">`Add` 公开 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，这使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用于方法中。</span><span class="sxs-lookup"><span data-stu-id="9f643-627">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="9f643-628">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) 决定如何处理其他管道进程。</span><span class="sxs-lookup"><span data-stu-id="9f643-628">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="9f643-629">将值设置为下表中的 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 字段之一。</span><span class="sxs-lookup"><span data-stu-id="9f643-629">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="9f643-630">重写上下文结果</span><span class="sxs-lookup"><span data-stu-id="9f643-630">Rewrite context result</span></span>               | <span data-ttu-id="9f643-631">操作</span><span class="sxs-lookup"><span data-stu-id="9f643-631">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="9f643-632">`RuleResult.ContinueRules`（默认值）</span><span class="sxs-lookup"><span data-stu-id="9f643-632">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="9f643-633">继续应用规则。</span><span class="sxs-lookup"><span data-stu-id="9f643-633">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="9f643-634">停止应用规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="9f643-634">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="9f643-635">停止应用规则并将上下文发送给下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="9f643-635">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="9f643-636">示例应用演示了如何对以 .xml 结尾的路径的请求进行重定向。</span><span class="sxs-lookup"><span data-stu-id="9f643-636">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="9f643-637">如果发出针对 `/file.xml` 的请求，请求将重定向到 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="9f643-637">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="9f643-638">状态代码设置为“301 (永久移动)”。</span><span class="sxs-lookup"><span data-stu-id="9f643-638">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="9f643-639">当浏览器发出针对 /xmlfiles/file.xml 的新请求后，静态文件中间件会将文件从 wwwroot / xmlfiles 文件夹提供给客户端 。</span><span class="sxs-lookup"><span data-stu-id="9f643-639">When the browser makes a new request for */xmlfiles/file.xml* , Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="9f643-640">对于重定向，请显式设置响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="9f643-640">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="9f643-641">否则，将会返回“200 (正常)”状态代码，且客户端上不会发生重写。</span><span class="sxs-lookup"><span data-stu-id="9f643-641">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="9f643-642">*RewriteRules.cs* :</span><span class="sxs-lookup"><span data-stu-id="9f643-642">*RewriteRules.cs* :</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="9f643-643">此方法还可以重写请求。</span><span class="sxs-lookup"><span data-stu-id="9f643-643">This approach can also rewrite requests.</span></span> <span data-ttu-id="9f643-644">示例应用演示了如何重写任何文本文件请求的路径以从 wwwroot 文件夹中提供 file.txt 文本文件 。</span><span class="sxs-lookup"><span data-stu-id="9f643-644">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="9f643-645">静态文件中间件基于更新的请求路径来提供文件：</span><span class="sxs-lookup"><span data-stu-id="9f643-645">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="9f643-646">*RewriteRules.cs* :</span><span class="sxs-lookup"><span data-stu-id="9f643-646">*RewriteRules.cs* :</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="9f643-647">基于 IRule 的规则</span><span class="sxs-lookup"><span data-stu-id="9f643-647">IRule-based rule</span></span>

<span data-ttu-id="9f643-648">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在实现 <xref:Microsoft.AspNetCore.Rewrite.IRule> 接口的类中使用规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="9f643-648">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="9f643-649">与使用基于方法的规则方法相比，`IRule` 提供了更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="9f643-649">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="9f643-650">实现类可能包含构造函数，可在其中传入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的参数。</span><span class="sxs-lookup"><span data-stu-id="9f643-650">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="9f643-651">检查示例应用中 `extension` 和 `newPath` 的参数值是否符合多个条件。</span><span class="sxs-lookup"><span data-stu-id="9f643-651">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="9f643-652">`extension` 须包含一个值，并且该值必须是 .png、.jpg 或 .gif  。</span><span class="sxs-lookup"><span data-stu-id="9f643-652">The `extension` must contain a value, and the value must be *.png* , *.jpg* , or *.gif*.</span></span> <span data-ttu-id="9f643-653">如果 `newPath` 无效，则会引发 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="9f643-653">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="9f643-654">如果发出针对 image.png 的请求，请求将重定向到 `/png-images/image.png`。</span><span class="sxs-lookup"><span data-stu-id="9f643-654">If a request is made for *image.png* , the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="9f643-655">如果发出针对 image.png 的请求，请求将重定向到 `/jpg-images/image.jpg`。</span><span class="sxs-lookup"><span data-stu-id="9f643-655">If a request is made for *image.jpg* , the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="9f643-656">状态代码设置为“301 (永久移动)”，`context.Result` 设置为停止处理规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="9f643-656">The status code is set to *301 - Moved Permanently* , and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="9f643-657">原始请求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="9f643-657">Original Request: `/image.png`</span></span>

![开发人员工具正跟踪 image.png 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="9f643-659">原始请求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="9f643-659">Original Request: `/image.jpg`</span></span>

![开发人员工具正跟踪 image.jpg 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="9f643-661">正则表达式示例</span><span class="sxs-lookup"><span data-stu-id="9f643-661">Regex examples</span></span>

| <span data-ttu-id="9f643-662">目标</span><span class="sxs-lookup"><span data-stu-id="9f643-662">Goal</span></span> | <span data-ttu-id="9f643-663">正则表达式字符串和</span><span class="sxs-lookup"><span data-stu-id="9f643-663">Regex String &</span></span><br><span data-ttu-id="9f643-664">匹配示例</span><span class="sxs-lookup"><span data-stu-id="9f643-664">Match Example</span></span> | <span data-ttu-id="9f643-665">替换字符串和</span><span class="sxs-lookup"><span data-stu-id="9f643-665">Replacement String &</span></span><br><span data-ttu-id="9f643-666">输出示例</span><span class="sxs-lookup"><span data-stu-id="9f643-666">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="9f643-667">将路径重写为查询字符串</span><span class="sxs-lookup"><span data-stu-id="9f643-667">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="9f643-668">去掉尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="9f643-668">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="9f643-669">强制添加尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="9f643-669">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="9f643-670">避免重写特定请求</span><span class="sxs-lookup"><span data-stu-id="9f643-670">Avoid rewriting specific requests</span></span> | <span data-ttu-id="9f643-671">`^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="9f643-671">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="9f643-672">正确：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="9f643-672">Yes: `/resource.htm`</span></span><br><span data-ttu-id="9f643-673">错误：`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="9f643-673">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="9f643-674">重新排列 URL 段</span><span class="sxs-lookup"><span data-stu-id="9f643-674">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="9f643-675">替换 URL 段</span><span class="sxs-lookup"><span data-stu-id="9f643-675">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9f643-676">其他资源</span><span class="sxs-lookup"><span data-stu-id="9f643-676">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="9f643-677">.NET 中的正则表达式</span><span class="sxs-lookup"><span data-stu-id="9f643-677">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="9f643-678">正则表达式语言 - 快速参考</span><span class="sxs-lookup"><span data-stu-id="9f643-678">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="9f643-679">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="9f643-679">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="9f643-680">使用 URL 重写模块 2.0（适用于 IIS）</span><span class="sxs-lookup"><span data-stu-id="9f643-680">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* <span data-ttu-id="9f643-681">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）</span><span class="sxs-lookup"><span data-stu-id="9f643-681">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span></span>
* [<span data-ttu-id="9f643-682">IIS URL 重写模块论坛</span><span class="sxs-lookup"><span data-stu-id="9f643-682">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* <span data-ttu-id="9f643-683">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)（保持简单的 URL 结构）</span><span class="sxs-lookup"><span data-stu-id="9f643-683">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)</span></span>
* <span data-ttu-id="9f643-684">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)（10 个 URL 重写提示和技巧）</span><span class="sxs-lookup"><span data-stu-id="9f643-684">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)</span></span>
* <span data-ttu-id="9f643-685">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)（用斜杠或不用斜杠）</span><span class="sxs-lookup"><span data-stu-id="9f643-685">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)</span></span>
