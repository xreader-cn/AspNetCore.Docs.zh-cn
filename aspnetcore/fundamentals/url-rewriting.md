---
title: ASP.NET Core 中的 URL 重写中间件
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用程序中使用 URL 重写中间件进行 URL 重写和重定向。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/url-rewriting
ms.openlocfilehash: 9e12831f57af02cd427d2a66d9d4c4d654905106
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774855"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="1e6c9-103">ASP.NET Core 中的 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="1e6c9-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="1e6c9-104">作者：[Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="1e6c9-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e6c9-105">本文档介绍 URL 重写并说明如何在 ASP.NET Core 应用中使用 URL 重写中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="1e6c9-106">URL 重写是根据一个或多个预定义规则修改请求 URL 的行为。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="1e6c9-107">URL 重写会在资源位置和地址之间创建一个抽象，使位置和地址不紧密相连。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="1e6c9-108">在以下几种方案中，URL 重写很有价值：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="1e6c9-109">暂时或永久移动或替换服务器资源，并维护这些资源的稳定定位符。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="1e6c9-110">拆分在不同应用或同一应用的不同区域中处理的请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="1e6c9-111">删除、添加或重新组织传入请求上的 URL 段。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="1e6c9-112">优化搜索引擎优化 (SEO) 的公共 URL。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="1e6c9-113">允许使用友好的公共 URL 来帮助访问者预测请求资源后返回的内容。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="1e6c9-114">将不安全请求重定向到安全终结点。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="1e6c9-115">防止热链接，外部站点会通过热链接将其他站点的资产链接到其自己的内容，从而利用托管在其他站点上的静态资产。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="1e6c9-116">URL 重写可能会降低应用的性能。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="1e6c9-117">如果可行，应限制规则的数量和复杂度。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="1e6c9-118">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="1e6c9-119">URL 重定向和 URL 重写</span><span class="sxs-lookup"><span data-stu-id="1e6c9-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="1e6c9-120">URL 重定向和 URL 重写之间的用词差异很细微，但这对于向客户端提供资源具有重要意义   。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="1e6c9-121">ASP.NET Core 的 URL 重写中间件能够满足两者的需求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="1e6c9-122">URL 重定向涉及客户端操作，指示客户端访问与客户端最初请求地址不同的资源  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="1e6c9-123">这需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-123">This requires a round trip to the server.</span></span> <span data-ttu-id="1e6c9-124">客户端对资源发出新请求时，返回客户端的重定向 URL 会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="1e6c9-125">如果 `/resource` 被重定向到 *，则服务器作出响应，指示客户端应在*  获取资源，所提供的状态代码指示重定向是临时的还是永久的`/different-resource``/different-resource`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 服务终结点已暂时从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="1e6c9-131">将请求重定向到不同 URL 时，通过使用响应指定状态代码来指示重定向是永久还是临时：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="1e6c9-132">如果资源有一个新的永久性 URL，并且你希望指示客户端所有将来的资源请求都使用新 URL，则使用“301 (永久移动)”状态代码  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="1e6c9-133">收到 301 状态代码时，客户端可能会缓存响应并重用这段代码  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="1e6c9-134">“302 (找到)”状态代码用于后列情况：重定向操作是临时的或通常会发生变化  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="1e6c9-135">302 状态代码向客户端指示不存储 URL 并在将来使用。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="1e6c9-136">有关状态代码的详细信息，请参阅 [RFC 2616：状态代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="1e6c9-137">URL 重写是服务器端操作，它从与客户端请求的资源地址不同的资源地址提供资源  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="1e6c9-138">重写 URL 不需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="1e6c9-139">重写的 URL 不会返回客户端，也不会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="1e6c9-140">如果 `/resource` 重写到 *，服务器会在内部提取并返回*  处的资源`/different-resource`  `/different-resource`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="1e6c9-141">尽管客户端可能能够检索已重写 URL 处的资源，但是，客户端发出请求并收到响应时，并不知道已重写 URL 处存在的资源。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 服务终结点已从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="1e6c9-146">URL 重写示例应用</span><span class="sxs-lookup"><span data-stu-id="1e6c9-146">URL rewriting sample app</span></span>

<span data-ttu-id="1e6c9-147">可使用[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)了解 URL 重写中间件的功能。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="1e6c9-148">该应用程序应用重定向和重写规则，并显示多个方案的重定向或重写的 URL。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="1e6c9-149">何时使用 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="1e6c9-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="1e6c9-150">如果无法使用以下方法，请使用 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="1e6c9-151">在 Windows Server 上使用带 IIS 的 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="1e6c9-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="1e6c9-152">在 Apache 服务器上使用 Apache mod_rewrite 模块</span><span class="sxs-lookup"><span data-stu-id="1e6c9-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="1e6c9-153">Nginx 上的 URL 重写</span><span class="sxs-lookup"><span data-stu-id="1e6c9-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="1e6c9-154">此外，如果应用程序在 [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（旧称 WebListener）上托管，请使用中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="1e6c9-155">使用 IIS、Apache 和 Nginx 中的基于服务器的 URL 重写技术的主要原因：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="1e6c9-156">中间件不支持这些模块的完整功能。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="1e6c9-157">服务器模块的一些功能不适用于 ASP.NET Core 项目，例如 IIS 重写模块的 `IsFile` 和 `IsDirectory` 约束。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="1e6c9-158">在这些情况下，请改为使用中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="1e6c9-159">中间件性能与模块性能不匹配。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="1e6c9-160">基准测试是确定哪种方法会最大程度降低性能或降低的性能是否可忽略不计的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="1e6c9-161">包</span><span class="sxs-lookup"><span data-stu-id="1e6c9-161">Package</span></span>

<span data-ttu-id="1e6c9-162">URL 重写中间件由 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 包提供，该包隐式包含在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="1e6c9-163">扩展和选项</span><span class="sxs-lookup"><span data-stu-id="1e6c9-163">Extension and options</span></span>

<span data-ttu-id="1e6c9-164">通过使用扩展方法为每条重写规则创建 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 类的实例，建立 URL 重写和重写定向规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="1e6c9-165">按所需的处理顺序链接多个规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="1e6c9-166">使用 `RewriteOptions` 将 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 添加到请求管道时，它会被传递到 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="1e6c9-167">将非 www 重定向到 www</span><span class="sxs-lookup"><span data-stu-id="1e6c9-167">Redirect non-www to www</span></span>

<span data-ttu-id="1e6c9-168">三个选项允许应用将非 `www` 重新定向到 `www`：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="1e6c9-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; 如果请求是非 `www`，则将请求永久重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="1e6c9-170">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="1e6c9-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; 如果传入请求是非 `www`，则将请求重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="1e6c9-172">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="1e6c9-173">重载允许提供响应状态代码。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="1e6c9-174">使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 类的字段实现状态代码分配。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="1e6c9-175">URL 重定向</span><span class="sxs-lookup"><span data-stu-id="1e6c9-175">URL redirect</span></span>

<span data-ttu-id="1e6c9-176">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 将请求重定向。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="1e6c9-177">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="1e6c9-178">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="1e6c9-179">第三个参数（如有）指定状态代码。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="1e6c9-180">如不指定状态代码，则状态代码默认为“302 (已找到)”，指示资源暂时移动或替换  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="1e6c9-181">在启用了开发人员工具的浏览器中，向路径为 `/redirect-rule/1234/5678` 的示例应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="1e6c9-182">正则表达式匹配 `redirect-rule/(.*)` 上的请求路径，且该路径会被 `/redirected/1234/5678` 替代。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="1e6c9-183">重定向 URL 以“302 (已找到)”状态代码发回客户端  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="1e6c9-184">浏览器会在浏览器地址栏中出现的重定向 URL 上发出新请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="1e6c9-185">由于示例应用中的规则都不匹配重定向 URL：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="1e6c9-186">第二个请求从应用程序收到“200 (正常)”响应  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="1e6c9-187">响应正文显示了重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="1e6c9-188">重定向 URL 时，系统将向服务器进行一次往返  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="1e6c9-189">建立重定向规则时务必小心。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="1e6c9-190">系统会根据应用的每个请求（包括重定向后的请求）对重定向规则进行评估。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="1e6c9-191">很容易便会意外创建无限重定向循环  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="1e6c9-192">原始请求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="1e6c9-194">括号内的表达式部分称为“捕获组”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="1e6c9-195">表达式的点 (`.`) 表示匹配任何字符  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="1e6c9-196">星号 (`*`) 表示零次或多次匹配前面的字符  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="1e6c9-197">因此，URL 的最后两个路径段 `1234/5678` 由捕获组 `(.*)` 捕获。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="1e6c9-198">在请求 URL 中提供的位于 `redirect-rule/` 之后的任何值均由此单个捕获组捕获。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="1e6c9-199">在替换字符串中，将捕获组注入带有美元符号 (`$`)、后跟捕获序列号的字符串中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="1e6c9-200">获取的第一个捕获组值为 `$1`，第二个为 `$2`，并且正则表达式中的其他捕获组值将依次继续排列。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="1e6c9-201">示例应用的重定向规则正则表达式中只有一个捕获组，因此替换字符串中只有一个注入组，即 `$1`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="1e6c9-202">如果应用此规则，URL 将变为 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="1e6c9-203">URL 重定向到安全的终结点</span><span class="sxs-lookup"><span data-stu-id="1e6c9-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="1e6c9-204">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 将 HTTP 请求重定向到采用 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="1e6c9-205">如不提供状态代码，则中间件默认为“302(已找到)”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="1e6c9-206">如果不提供端口：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="1e6c9-207">中间件默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="1e6c9-208">方案更改为 `https`（HTTPS 协议），客户端访问端口 443 上的资源。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="1e6c9-209">下面的示例演示如何将状态代码设置为“301(永久移动)”并将端口更改为 5001  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="1e6c9-210">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 将不安全的请求重定向到端口 443 上的采用安全 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="1e6c9-211">中间件将状态代码设置为“301 (永久移动)”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="1e6c9-212">当重定向到安全的终结点并且不需要其他重定向规则时，建议使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="1e6c9-213">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl#require-https)主题。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="1e6c9-214">示例应用能够演示如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="1e6c9-215">将扩展方法添加到 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="1e6c9-216">在任何 URL 上向应用发出不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="1e6c9-217">消除自签名证书不受信任的浏览器安全警告，或创建例外以信任证书。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="1e6c9-218">使用 `AddRedirectToHttps(301, 5001)` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="1e6c9-220">使用 `AddRedirectToHttpsPermanent` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="1e6c9-222">URL 重写</span><span class="sxs-lookup"><span data-stu-id="1e6c9-222">URL rewrite</span></span>

<span data-ttu-id="1e6c9-223">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 创建重写 URL 的规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="1e6c9-224">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="1e6c9-225">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="1e6c9-226">第三个参数 `skipRemainingRules: {true|false}` 指示如果当前规则适用，中间件是否要跳过其他重写规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="1e6c9-227">原始请求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="1e6c9-229">表达式开头的脱字号 (`^`) 意味着匹配从 URL 路径的开头处开始。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="1e6c9-230">在前面的重定向规则 `redirect-rule/(.*)` 的示例中，正则表达式的开头没有脱字号 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="1e6c9-231">因此，路径中 `redirect-rule/` 之前的任何字符都可能成功匹配。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="1e6c9-232">路径</span><span class="sxs-lookup"><span data-stu-id="1e6c9-232">Path</span></span>                               | <span data-ttu-id="1e6c9-233">匹配</span><span class="sxs-lookup"><span data-stu-id="1e6c9-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="1e6c9-234">是</span><span class="sxs-lookup"><span data-stu-id="1e6c9-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="1e6c9-235">是</span><span class="sxs-lookup"><span data-stu-id="1e6c9-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="1e6c9-236">是</span><span class="sxs-lookup"><span data-stu-id="1e6c9-236">Yes</span></span>   |

<span data-ttu-id="1e6c9-237">重写规则 `^rewrite-rule/(\d+)/(\d+)` 只能与以 `rewrite-rule/` 开头的路径匹配。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="1e6c9-238">注意下表中的匹配差异。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="1e6c9-239">路径</span><span class="sxs-lookup"><span data-stu-id="1e6c9-239">Path</span></span>                              | <span data-ttu-id="1e6c9-240">匹配</span><span class="sxs-lookup"><span data-stu-id="1e6c9-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="1e6c9-241">是</span><span class="sxs-lookup"><span data-stu-id="1e6c9-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="1e6c9-242">No</span><span class="sxs-lookup"><span data-stu-id="1e6c9-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="1e6c9-243">No</span><span class="sxs-lookup"><span data-stu-id="1e6c9-243">No</span></span>    |

<span data-ttu-id="1e6c9-244">在表达式的 `^rewrite-rule/` 部分之后，有两个捕获组 `(\d+)/(\d+)`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="1e6c9-245">`\d` 表示与数字匹配  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="1e6c9-246">加号 (`+`) 表示与前面的一个或多个字符匹配  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="1e6c9-247">因此，URL 必须包含数字加正斜杠加另一个数字的形式。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="1e6c9-248">这些捕获组以 `$1` 和 `$2` 的形式注入重写 URL 中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="1e6c9-249">重写规则替换字符串将捕获组放入查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="1e6c9-250">重写 `/rewrite-rule/1234/5678` 的请求路径，获取 `/rewritten?var1=1234&var2=5678` 处的资源。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="1e6c9-251">如果原始请求中存在查询字符串，则重写 URL 时会保留此字符串。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="1e6c9-252">无需往返服务器来获取资源。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="1e6c9-253">如果资源存在，系统会提取资源并以“200（正常）”状态代码返回给客户端  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="1e6c9-254">因为客户端不会被重定向，所以浏览器地址栏中的 URL 不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="1e6c9-255">客户端无法检测到服务器上发生的 URL 重写操作。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1e6c9-256">尽可能使用 `skipRemainingRules: true`，因为匹配规则在计算上很昂贵并且增加了应用响应时间。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="1e6c9-257">对于最快的应用响应：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="1e6c9-258">按照从最频繁匹配的规则到最不频繁匹配的规则排列重写规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="1e6c9-259">如果出现匹配项且无需处理任何其他规则，则跳过剩余规则的处理。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="1e6c9-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1e6c9-260">Apache mod_rewrite</span></span>

<span data-ttu-id="1e6c9-261">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 应用 Apache mod_rewrite 规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="1e6c9-262">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1e6c9-263">有关 mod_rewrite 规则的详细信息和示例，请参阅 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="1e6c9-264"><xref:System.IO.StreamReader> 用于读取 ApacheModRewrite.txt 规则文件中的规则  ：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="1e6c9-265">示例应用将请求从 `/apache-mod-rules-redirect/(.\*)` 重定向到 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="1e6c9-266">响应状态代码为“302 (已找到)”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="1e6c9-267">原始请求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="1e6c9-269">中间件支持下列 Apache mod_rewrite 服务器变量：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="1e6c9-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="1e6c9-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1e6c9-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1e6c9-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1e6c9-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1e6c9-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="1e6c9-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="1e6c9-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="1e6c9-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1e6c9-275">HTTP_HOST</span></span>
* <span data-ttu-id="1e6c9-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1e6c9-276">HTTP_REFERER</span></span>
* <span data-ttu-id="1e6c9-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1e6c9-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1e6c9-278">HTTPS</span></span>
* <span data-ttu-id="1e6c9-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="1e6c9-279">IPV6</span></span>
* <span data-ttu-id="1e6c9-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1e6c9-280">QUERY_STRING</span></span>
* <span data-ttu-id="1e6c9-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="1e6c9-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-282">REMOTE_PORT</span></span>
* <span data-ttu-id="1e6c9-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1e6c9-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="1e6c9-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="1e6c9-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="1e6c9-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1e6c9-286">REQUEST_URI</span></span>
* <span data-ttu-id="1e6c9-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="1e6c9-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-288">SERVER_ADDR</span></span>
* <span data-ttu-id="1e6c9-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-289">SERVER_PORT</span></span>
* <span data-ttu-id="1e6c9-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="1e6c9-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="1e6c9-291">TIME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-291">TIME</span></span>
* <span data-ttu-id="1e6c9-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="1e6c9-292">TIME_DAY</span></span>
* <span data-ttu-id="1e6c9-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-293">TIME_HOUR</span></span>
* <span data-ttu-id="1e6c9-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="1e6c9-294">TIME_MIN</span></span>
* <span data-ttu-id="1e6c9-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="1e6c9-295">TIME_MON</span></span>
* <span data-ttu-id="1e6c9-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="1e6c9-296">TIME_SEC</span></span>
* <span data-ttu-id="1e6c9-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="1e6c9-297">TIME_WDAY</span></span>
* <span data-ttu-id="1e6c9-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="1e6c9-299">IIS URL 重写模块规则</span><span class="sxs-lookup"><span data-stu-id="1e6c9-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="1e6c9-300">若要使用适用于 IIS URL 重写模块的同一规则集，使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="1e6c9-301">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1e6c9-302">当在 Windows Server IIS 上运行时，请勿指示中间件使用应用的 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="1e6c9-303">使用 IIS 时，应将这些规则存储在应用的 web.config 文件之外，以避免与 IIS 重写模块发生冲突  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="1e6c9-304">有关 IIS URL 重写模块规则的详细信息和示例，请参阅 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)（使用 URL 重写模块 2.0）和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="1e6c9-305"><xref:System.IO.StreamReader> 用于读取 IISUrlRewrite.xml 规则文件中的规则  ：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="1e6c9-306">示例应用将请求从 `/iis-rules-rewrite/(.*)` 重写为 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="1e6c9-307">以“200 (正常)”状态代码作为响应发送到客户端  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="1e6c9-308">原始请求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="1e6c9-310">如果有配置了服务器级别规则（可对应用产生不利影响）的活动 IIS 重写模块，则可禁用应用的 IIS 重写模块。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="1e6c9-311">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="1e6c9-312">不支持的功能</span><span class="sxs-lookup"><span data-stu-id="1e6c9-312">Unsupported features</span></span>

<span data-ttu-id="1e6c9-313">与 ASP.NET Core 2.x 一同发布的中间件不支持以下 IIS URL 重写模块功能：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="1e6c9-314">出站规则</span><span class="sxs-lookup"><span data-stu-id="1e6c9-314">Outbound Rules</span></span>
* <span data-ttu-id="1e6c9-315">自定义服务器变量</span><span class="sxs-lookup"><span data-stu-id="1e6c9-315">Custom Server Variables</span></span>
* <span data-ttu-id="1e6c9-316">通配符</span><span class="sxs-lookup"><span data-stu-id="1e6c9-316">Wildcards</span></span>
* <span data-ttu-id="1e6c9-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="1e6c9-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="1e6c9-318">支持的服务器变量</span><span class="sxs-lookup"><span data-stu-id="1e6c9-318">Supported server variables</span></span>

<span data-ttu-id="1e6c9-319">中间件支持下列 IIS URL 重写模块服务器变量：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="1e6c9-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="1e6c9-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="1e6c9-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="1e6c9-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="1e6c9-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1e6c9-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1e6c9-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1e6c9-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1e6c9-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="1e6c9-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1e6c9-325">HTTP_HOST</span></span>
* <span data-ttu-id="1e6c9-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1e6c9-326">HTTP_REFERER</span></span>
* <span data-ttu-id="1e6c9-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="1e6c9-327">HTTP_URL</span></span>
* <span data-ttu-id="1e6c9-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1e6c9-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1e6c9-329">HTTPS</span></span>
* <span data-ttu-id="1e6c9-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="1e6c9-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1e6c9-331">QUERY_STRING</span></span>
* <span data-ttu-id="1e6c9-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="1e6c9-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-333">REMOTE_PORT</span></span>
* <span data-ttu-id="1e6c9-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1e6c9-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1e6c9-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="1e6c9-336">也可通过 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 获取 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="1e6c9-337">这种方法可为重写规则文件的位置提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="1e6c9-338">请确保将重写规则文件部署到所提供路径的服务器中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="1e6c9-339">基于方法的规则</span><span class="sxs-lookup"><span data-stu-id="1e6c9-339">Method-based rule</span></span>

<span data-ttu-id="1e6c9-340">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在方法中实现自己的规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="1e6c9-341">`Add` 公开 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，这使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用于方法中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="1e6c9-342">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) 决定如何处理其他管道进程。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="1e6c9-343">将值设置为下表中的 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 字段之一。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="1e6c9-344">操作</span><span class="sxs-lookup"><span data-stu-id="1e6c9-344">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="1e6c9-345">`RuleResult.ContinueRules`（默认值）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-345">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="1e6c9-346">继续应用规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-346">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="1e6c9-347">停止应用规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-347">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="1e6c9-348">停止应用规则并将上下文发送给下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-348">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="1e6c9-349">示例应用演示了如何对以 .xml 结尾的路径的请求进行重定向  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-349">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="1e6c9-350">如果发出针对 `/file.xml` 的请求，请求将重定向到 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-350">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="1e6c9-351">状态代码设置为“301 (永久移动)”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-351">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="1e6c9-352">当浏览器发出针对 /xmlfiles/file.xml 的新请求后，静态文件中间件会将文件从 wwwroot / xmlfiles 文件夹提供给客户端   。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-352">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="1e6c9-353">对于重定向，请显式设置响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-353">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="1e6c9-354">否则，将会返回“200 (正常)”状态代码，且客户端上不会发生重写  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-354">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="1e6c9-355">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e6c9-355">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="1e6c9-356">此方法还可以重写请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-356">This approach can also rewrite requests.</span></span> <span data-ttu-id="1e6c9-357">示例应用演示了如何重写任何文本文件请求的路径以从 wwwroot 文件夹中提供 file.txt 文本文件   。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-357">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="1e6c9-358">静态文件中间件基于更新的请求路径来提供文件：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-358">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="1e6c9-359">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e6c9-359">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="1e6c9-360">基于 IRule 的规则</span><span class="sxs-lookup"><span data-stu-id="1e6c9-360">IRule-based rule</span></span>

<span data-ttu-id="1e6c9-361">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在实现 <xref:Microsoft.AspNetCore.Rewrite.IRule> 接口的类中使用规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-361">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="1e6c9-362">与使用基于方法的规则方法相比，`IRule` 提供了更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-362">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="1e6c9-363">实现类可能包含构造函数，可在其中传入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的参数。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-363">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="1e6c9-364">检查示例应用中 `extension` 和 `newPath` 的参数值是否符合多个条件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-364">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="1e6c9-365">`extension` 须包含一个值，并且该值必须是 .png、.jpg 或 .gif    。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-365">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="1e6c9-366">如果 `newPath` 无效，则会引发 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-366">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="1e6c9-367">如果发出针对 image.png 的请求，请求将重定向到  `/png-images/image.png`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-367">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="1e6c9-368">如果发出针对 image.png 的请求，请求将重定向到  `/jpg-images/image.jpg`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-368">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="1e6c9-369">状态代码设置为“301 (永久移动)”， *设置为停止处理规则并发送响应*`context.Result`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-369">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="1e6c9-370">原始请求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-370">Original Request: `/image.png`</span></span>

![开发人员工具正跟踪 image.png 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="1e6c9-372">原始请求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-372">Original Request: `/image.jpg`</span></span>

![开发人员工具正跟踪 image.jpg 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="1e6c9-374">正则表达式示例</span><span class="sxs-lookup"><span data-stu-id="1e6c9-374">Regex examples</span></span>

| <span data-ttu-id="1e6c9-375">目标</span><span class="sxs-lookup"><span data-stu-id="1e6c9-375">Goal</span></span> | <span data-ttu-id="1e6c9-376">正则表达式字符串和</span><span class="sxs-lookup"><span data-stu-id="1e6c9-376">Regex String &</span></span><br><span data-ttu-id="1e6c9-377">匹配示例</span><span class="sxs-lookup"><span data-stu-id="1e6c9-377">Match Example</span></span> | <span data-ttu-id="1e6c9-378">替换字符串和</span><span class="sxs-lookup"><span data-stu-id="1e6c9-378">Replacement String &</span></span><br><span data-ttu-id="1e6c9-379">输出示例</span><span class="sxs-lookup"><span data-stu-id="1e6c9-379">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="1e6c9-380">将路径重写为查询字符串</span><span class="sxs-lookup"><span data-stu-id="1e6c9-380">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="1e6c9-381">去掉尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="1e6c9-381">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="1e6c9-382">强制添加尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="1e6c9-382">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="1e6c9-383">避免重写特定请求</span><span class="sxs-lookup"><span data-stu-id="1e6c9-383">Avoid rewriting specific requests</span></span> | <span data-ttu-id="1e6c9-384">`^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-384">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="1e6c9-385">正确：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-385">Yes: `/resource.htm`</span></span><br><span data-ttu-id="1e6c9-386">错误：`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-386">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="1e6c9-387">重新排列 URL 段</span><span class="sxs-lookup"><span data-stu-id="1e6c9-387">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="1e6c9-388">替换 URL 段</span><span class="sxs-lookup"><span data-stu-id="1e6c9-388">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e6c9-389">本文档介绍 URL 重写并说明如何在 ASP.NET Core 应用中使用 URL 重写中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-389">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="1e6c9-390">URL 重写是根据一个或多个预定义规则修改请求 URL 的行为。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-390">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="1e6c9-391">URL 重写会在资源位置和地址之间创建一个抽象，使位置和地址不紧密相连。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-391">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="1e6c9-392">在以下几种方案中，URL 重写很有价值：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-392">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="1e6c9-393">暂时或永久移动或替换服务器资源，并维护这些资源的稳定定位符。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-393">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="1e6c9-394">拆分在不同应用或同一应用的不同区域中处理的请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-394">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="1e6c9-395">删除、添加或重新组织传入请求上的 URL 段。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-395">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="1e6c9-396">优化搜索引擎优化 (SEO) 的公共 URL。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-396">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="1e6c9-397">允许使用友好的公共 URL 来帮助访问者预测请求资源后返回的内容。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-397">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="1e6c9-398">将不安全请求重定向到安全终结点。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-398">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="1e6c9-399">防止热链接，外部站点会通过热链接将其他站点的资产链接到其自己的内容，从而利用托管在其他站点上的静态资产。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-399">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="1e6c9-400">URL 重写可能会降低应用的性能。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-400">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="1e6c9-401">如果可行，应限制规则的数量和复杂度。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-401">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="1e6c9-402">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-402">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="1e6c9-403">URL 重定向和 URL 重写</span><span class="sxs-lookup"><span data-stu-id="1e6c9-403">URL redirect and URL rewrite</span></span>

<span data-ttu-id="1e6c9-404">URL 重定向和 URL 重写之间的用词差异很细微，但这对于向客户端提供资源具有重要意义   。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-404">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="1e6c9-405">ASP.NET Core 的 URL 重写中间件能够满足两者的需求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-405">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="1e6c9-406">URL 重定向涉及客户端操作，指示客户端访问与客户端最初请求地址不同的资源  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-406">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="1e6c9-407">这需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-407">This requires a round trip to the server.</span></span> <span data-ttu-id="1e6c9-408">客户端对资源发出新请求时，返回客户端的重定向 URL 会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-408">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="1e6c9-409">如果 `/resource` 被重定向到 *，则服务器作出响应，指示客户端应在*  获取资源，所提供的状态代码指示重定向是临时的还是永久的`/different-resource``/different-resource`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-409">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 服务终结点已暂时从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="1e6c9-415">将请求重定向到不同 URL 时，通过使用响应指定状态代码来指示重定向是永久还是临时：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-415">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="1e6c9-416">如果资源有一个新的永久性 URL，并且你希望指示客户端所有将来的资源请求都使用新 URL，则使用“301 (永久移动)”状态代码  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-416">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="1e6c9-417">收到 301 状态代码时，客户端可能会缓存响应并重用这段代码  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-417">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="1e6c9-418">“302 (找到)”状态代码用于后列情况：重定向操作是临时的或通常会发生变化  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-418">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="1e6c9-419">302 状态代码向客户端指示不存储 URL 并在将来使用。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-419">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="1e6c9-420">有关状态代码的详细信息，请参阅 [RFC 2616：状态代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-420">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="1e6c9-421">URL 重写是服务器端操作，它从与客户端请求的资源地址不同的资源地址提供资源  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-421">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="1e6c9-422">重写 URL 不需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-422">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="1e6c9-423">重写的 URL 不会返回客户端，也不会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-423">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="1e6c9-424">如果 `/resource` 重写到 *，服务器会在内部提取并返回*  处的资源`/different-resource`  `/different-resource`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-424">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="1e6c9-425">尽管客户端可能能够检索已重写 URL 处的资源，但是，客户端发出请求并收到响应时，并不知道已重写 URL 处存在的资源。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-425">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 服务终结点已从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="1e6c9-430">URL 重写示例应用</span><span class="sxs-lookup"><span data-stu-id="1e6c9-430">URL rewriting sample app</span></span>

<span data-ttu-id="1e6c9-431">可使用[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)了解 URL 重写中间件的功能。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-431">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="1e6c9-432">该应用程序应用重定向和重写规则，并显示多个方案的重定向或重写的 URL。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-432">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="1e6c9-433">何时使用 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="1e6c9-433">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="1e6c9-434">如果无法使用以下方法，请使用 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-434">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="1e6c9-435">在 Windows Server 上使用带 IIS 的 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="1e6c9-435">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="1e6c9-436">在 Apache 服务器上使用 Apache mod_rewrite 模块</span><span class="sxs-lookup"><span data-stu-id="1e6c9-436">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="1e6c9-437">Nginx 上的 URL 重写</span><span class="sxs-lookup"><span data-stu-id="1e6c9-437">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="1e6c9-438">此外，如果应用程序在 [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（旧称 WebListener）上托管，请使用中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-438">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="1e6c9-439">使用 IIS、Apache 和 Nginx 中的基于服务器的 URL 重写技术的主要原因：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-439">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="1e6c9-440">中间件不支持这些模块的完整功能。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-440">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="1e6c9-441">服务器模块的一些功能不适用于 ASP.NET Core 项目，例如 IIS 重写模块的 `IsFile` 和 `IsDirectory` 约束。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-441">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="1e6c9-442">在这些情况下，请改为使用中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-442">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="1e6c9-443">中间件性能与模块性能不匹配。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-443">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="1e6c9-444">基准测试是确定哪种方法会最大程度降低性能或降低的性能是否可忽略不计的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-444">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="1e6c9-445">包</span><span class="sxs-lookup"><span data-stu-id="1e6c9-445">Package</span></span>

<span data-ttu-id="1e6c9-446">要在项目中包含中间件，请在项目文件中添加对 [Microsoft.AspNetCore.App 元数据包](xref:fundamentals/metapackage-app)的包引用，该文件包含 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 包。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-446">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="1e6c9-447">不使用 `Microsoft.AspNetCore.App` 元包时，向 `Microsoft.AspNetCore.Rewrite` 包添加项目引用。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-447">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="1e6c9-448">扩展和选项</span><span class="sxs-lookup"><span data-stu-id="1e6c9-448">Extension and options</span></span>

<span data-ttu-id="1e6c9-449">通过使用扩展方法为每条重写规则创建 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 类的实例，建立 URL 重写和重写定向规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-449">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="1e6c9-450">按所需的处理顺序链接多个规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-450">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="1e6c9-451">使用 `RewriteOptions` 将 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 添加到请求管道时，它会被传递到 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-451">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="1e6c9-452">将非 www 重定向到 www</span><span class="sxs-lookup"><span data-stu-id="1e6c9-452">Redirect non-www to www</span></span>

<span data-ttu-id="1e6c9-453">三个选项允许应用将非 `www` 重新定向到 `www`：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-453">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="1e6c9-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; 如果请求是非 `www`，则将请求永久重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="1e6c9-455">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-455">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="1e6c9-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; 如果传入请求是非 `www`，则将请求重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="1e6c9-457">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-457">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="1e6c9-458">重载允许提供响应状态代码。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-458">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="1e6c9-459">使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 类的字段实现状态代码分配。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-459">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="1e6c9-460">URL 重定向</span><span class="sxs-lookup"><span data-stu-id="1e6c9-460">URL redirect</span></span>

<span data-ttu-id="1e6c9-461">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 将请求重定向。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-461">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="1e6c9-462">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-462">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="1e6c9-463">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-463">The second parameter is the replacement string.</span></span> <span data-ttu-id="1e6c9-464">第三个参数（如有）指定状态代码。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-464">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="1e6c9-465">如不指定状态代码，则状态代码默认为“302 (已找到)”，指示资源暂时移动或替换  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-465">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="1e6c9-466">在启用了开发人员工具的浏览器中，向路径为 `/redirect-rule/1234/5678` 的示例应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-466">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="1e6c9-467">正则表达式匹配 `redirect-rule/(.*)` 上的请求路径，且该路径会被 `/redirected/1234/5678` 替代。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-467">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="1e6c9-468">重定向 URL 以“302 (已找到)”状态代码发回客户端  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-468">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="1e6c9-469">浏览器会在浏览器地址栏中出现的重定向 URL 上发出新请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-469">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="1e6c9-470">由于示例应用中的规则都不匹配重定向 URL：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-470">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="1e6c9-471">第二个请求从应用程序收到“200 (正常)”响应  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-471">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="1e6c9-472">响应正文显示了重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-472">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="1e6c9-473">重定向 URL 时，系统将向服务器进行一次往返  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-473">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="1e6c9-474">建立重定向规则时务必小心。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-474">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="1e6c9-475">系统会根据应用的每个请求（包括重定向后的请求）对重定向规则进行评估。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-475">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="1e6c9-476">很容易便会意外创建无限重定向循环  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-476">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="1e6c9-477">原始请求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-477">Original Request: `/redirect-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="1e6c9-479">括号内的表达式部分称为“捕获组”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-479">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="1e6c9-480">表达式的点 (`.`) 表示匹配任何字符  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-480">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="1e6c9-481">星号 (`*`) 表示零次或多次匹配前面的字符  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-481">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="1e6c9-482">因此，URL 的最后两个路径段 `1234/5678` 由捕获组 `(.*)` 捕获。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-482">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="1e6c9-483">在请求 URL 中提供的位于 `redirect-rule/` 之后的任何值均由此单个捕获组捕获。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-483">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="1e6c9-484">在替换字符串中，将捕获组注入带有美元符号 (`$`)、后跟捕获序列号的字符串中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-484">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="1e6c9-485">获取的第一个捕获组值为 `$1`，第二个为 `$2`，并且正则表达式中的其他捕获组值将依次继续排列。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-485">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="1e6c9-486">示例应用的重定向规则正则表达式中只有一个捕获组，因此替换字符串中只有一个注入组，即 `$1`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-486">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="1e6c9-487">如果应用此规则，URL 将变为 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-487">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="1e6c9-488">URL 重定向到安全的终结点</span><span class="sxs-lookup"><span data-stu-id="1e6c9-488">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="1e6c9-489">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 将 HTTP 请求重定向到采用 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-489">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="1e6c9-490">如不提供状态代码，则中间件默认为“302(已找到)”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-490">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="1e6c9-491">如果不提供端口：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-491">If the port isn't supplied:</span></span>

* <span data-ttu-id="1e6c9-492">中间件默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-492">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="1e6c9-493">方案更改为 `https`（HTTPS 协议），客户端访问端口 443 上的资源。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-493">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="1e6c9-494">下面的示例演示如何将状态代码设置为“301(永久移动)”并将端口更改为 5001  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-494">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="1e6c9-495">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 将不安全的请求重定向到端口 443 上的采用安全 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-495">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="1e6c9-496">中间件将状态代码设置为“301 (永久移动)”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-496">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="1e6c9-497">当重定向到安全的终结点并且不需要其他重定向规则时，建议使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-497">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="1e6c9-498">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl#require-https)主题。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-498">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="1e6c9-499">示例应用能够演示如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-499">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="1e6c9-500">将扩展方法添加到 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-500">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="1e6c9-501">在任何 URL 上向应用发出不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-501">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="1e6c9-502">消除自签名证书不受信任的浏览器安全警告，或创建例外以信任证书。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-502">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="1e6c9-503">使用 `AddRedirectToHttps(301, 5001)` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-503">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="1e6c9-505">使用 `AddRedirectToHttpsPermanent` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-505">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="1e6c9-507">URL 重写</span><span class="sxs-lookup"><span data-stu-id="1e6c9-507">URL rewrite</span></span>

<span data-ttu-id="1e6c9-508">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 创建重写 URL 的规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-508">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="1e6c9-509">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-509">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="1e6c9-510">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-510">The second parameter is the replacement string.</span></span> <span data-ttu-id="1e6c9-511">第三个参数 `skipRemainingRules: {true|false}` 指示如果当前规则适用，中间件是否要跳过其他重写规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-511">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="1e6c9-512">原始请求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-512">Original Request: `/rewrite-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="1e6c9-514">表达式开头的脱字号 (`^`) 意味着匹配从 URL 路径的开头处开始。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-514">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="1e6c9-515">在前面的重定向规则 `redirect-rule/(.*)` 的示例中，正则表达式的开头没有脱字号 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-515">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="1e6c9-516">因此，路径中 `redirect-rule/` 之前的任何字符都可能成功匹配。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-516">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="1e6c9-517">路径</span><span class="sxs-lookup"><span data-stu-id="1e6c9-517">Path</span></span>                               | <span data-ttu-id="1e6c9-518">匹配</span><span class="sxs-lookup"><span data-stu-id="1e6c9-518">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="1e6c9-519">是</span><span class="sxs-lookup"><span data-stu-id="1e6c9-519">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="1e6c9-520">是</span><span class="sxs-lookup"><span data-stu-id="1e6c9-520">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="1e6c9-521">是</span><span class="sxs-lookup"><span data-stu-id="1e6c9-521">Yes</span></span>   |

<span data-ttu-id="1e6c9-522">重写规则 `^rewrite-rule/(\d+)/(\d+)` 只能与以 `rewrite-rule/` 开头的路径匹配。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-522">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="1e6c9-523">注意下表中的匹配差异。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-523">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="1e6c9-524">路径</span><span class="sxs-lookup"><span data-stu-id="1e6c9-524">Path</span></span>                              | <span data-ttu-id="1e6c9-525">匹配</span><span class="sxs-lookup"><span data-stu-id="1e6c9-525">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="1e6c9-526">是</span><span class="sxs-lookup"><span data-stu-id="1e6c9-526">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="1e6c9-527">No</span><span class="sxs-lookup"><span data-stu-id="1e6c9-527">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="1e6c9-528">No</span><span class="sxs-lookup"><span data-stu-id="1e6c9-528">No</span></span>    |

<span data-ttu-id="1e6c9-529">在表达式的 `^rewrite-rule/` 部分之后，有两个捕获组 `(\d+)/(\d+)`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-529">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="1e6c9-530">`\d` 表示与数字匹配  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-530">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="1e6c9-531">加号 (`+`) 表示与前面的一个或多个字符匹配  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-531">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="1e6c9-532">因此，URL 必须包含数字加正斜杠加另一个数字的形式。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-532">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="1e6c9-533">这些捕获组以 `$1` 和 `$2` 的形式注入重写 URL 中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-533">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="1e6c9-534">重写规则替换字符串将捕获组放入查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-534">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="1e6c9-535">重写 `/rewrite-rule/1234/5678` 的请求路径，获取 `/rewritten?var1=1234&var2=5678` 处的资源。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-535">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="1e6c9-536">如果原始请求中存在查询字符串，则重写 URL 时会保留此字符串。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-536">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="1e6c9-537">无需往返服务器来获取资源。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-537">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="1e6c9-538">如果资源存在，系统会提取资源并以“200（正常）”状态代码返回给客户端  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-538">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="1e6c9-539">因为客户端不会被重定向，所以浏览器地址栏中的 URL 不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-539">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="1e6c9-540">客户端无法检测到服务器上发生的 URL 重写操作。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-540">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1e6c9-541">尽可能使用 `skipRemainingRules: true`，因为匹配规则在计算上很昂贵并且增加了应用响应时间。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-541">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="1e6c9-542">对于最快的应用响应：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-542">For the fastest app response:</span></span>
>
> * <span data-ttu-id="1e6c9-543">按照从最频繁匹配的规则到最不频繁匹配的规则排列重写规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-543">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="1e6c9-544">如果出现匹配项且无需处理任何其他规则，则跳过剩余规则的处理。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-544">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="1e6c9-545">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1e6c9-545">Apache mod_rewrite</span></span>

<span data-ttu-id="1e6c9-546">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 应用 Apache mod_rewrite 规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-546">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="1e6c9-547">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-547">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1e6c9-548">有关 mod_rewrite 规则的详细信息和示例，请参阅 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-548">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="1e6c9-549"><xref:System.IO.StreamReader> 用于读取 ApacheModRewrite.txt 规则文件中的规则  ：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-549">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="1e6c9-550">示例应用将请求从 `/apache-mod-rules-redirect/(.\*)` 重定向到 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-550">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="1e6c9-551">响应状态代码为“302 (已找到)”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-551">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="1e6c9-552">原始请求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-552">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="1e6c9-554">中间件支持下列 Apache mod_rewrite 服务器变量：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-554">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="1e6c9-555">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-555">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="1e6c9-556">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-556">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1e6c9-557">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1e6c9-557">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1e6c9-558">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1e6c9-558">HTTP_COOKIE</span></span>
* <span data-ttu-id="1e6c9-559">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="1e6c9-559">HTTP_FORWARDED</span></span>
* <span data-ttu-id="1e6c9-560">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1e6c9-560">HTTP_HOST</span></span>
* <span data-ttu-id="1e6c9-561">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1e6c9-561">HTTP_REFERER</span></span>
* <span data-ttu-id="1e6c9-562">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-562">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1e6c9-563">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1e6c9-563">HTTPS</span></span>
* <span data-ttu-id="1e6c9-564">IPV6</span><span class="sxs-lookup"><span data-stu-id="1e6c9-564">IPV6</span></span>
* <span data-ttu-id="1e6c9-565">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1e6c9-565">QUERY_STRING</span></span>
* <span data-ttu-id="1e6c9-566">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-566">REMOTE_ADDR</span></span>
* <span data-ttu-id="1e6c9-567">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-567">REMOTE_PORT</span></span>
* <span data-ttu-id="1e6c9-568">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-568">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1e6c9-569">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="1e6c9-569">REQUEST_METHOD</span></span>
* <span data-ttu-id="1e6c9-570">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-570">REQUEST_SCHEME</span></span>
* <span data-ttu-id="1e6c9-571">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1e6c9-571">REQUEST_URI</span></span>
* <span data-ttu-id="1e6c9-572">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-572">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="1e6c9-573">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-573">SERVER_ADDR</span></span>
* <span data-ttu-id="1e6c9-574">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-574">SERVER_PORT</span></span>
* <span data-ttu-id="1e6c9-575">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="1e6c9-575">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="1e6c9-576">TIME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-576">TIME</span></span>
* <span data-ttu-id="1e6c9-577">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="1e6c9-577">TIME_DAY</span></span>
* <span data-ttu-id="1e6c9-578">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-578">TIME_HOUR</span></span>
* <span data-ttu-id="1e6c9-579">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="1e6c9-579">TIME_MIN</span></span>
* <span data-ttu-id="1e6c9-580">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="1e6c9-580">TIME_MON</span></span>
* <span data-ttu-id="1e6c9-581">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="1e6c9-581">TIME_SEC</span></span>
* <span data-ttu-id="1e6c9-582">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="1e6c9-582">TIME_WDAY</span></span>
* <span data-ttu-id="1e6c9-583">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-583">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="1e6c9-584">IIS URL 重写模块规则</span><span class="sxs-lookup"><span data-stu-id="1e6c9-584">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="1e6c9-585">若要使用适用于 IIS URL 重写模块的同一规则集，使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-585">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="1e6c9-586">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-586">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1e6c9-587">当在 Windows Server IIS 上运行时，请勿指示中间件使用应用的 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-587">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="1e6c9-588">使用 IIS 时，应将这些规则存储在应用的 web.config 文件之外，以避免与 IIS 重写模块发生冲突  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-588">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="1e6c9-589">有关 IIS URL 重写模块规则的详细信息和示例，请参阅 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)（使用 URL 重写模块 2.0）和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-589">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="1e6c9-590"><xref:System.IO.StreamReader> 用于读取 IISUrlRewrite.xml 规则文件中的规则  ：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-590">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="1e6c9-591">示例应用将请求从 `/iis-rules-rewrite/(.*)` 重写为 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-591">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="1e6c9-592">以“200 (正常)”状态代码作为响应发送到客户端  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-592">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="1e6c9-593">原始请求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-593">Original Request: `/iis-rules-rewrite/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="1e6c9-595">如果有配置了服务器级别规则（可对应用产生不利影响）的活动 IIS 重写模块，则可禁用应用的 IIS 重写模块。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-595">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="1e6c9-596">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-596">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="1e6c9-597">不支持的功能</span><span class="sxs-lookup"><span data-stu-id="1e6c9-597">Unsupported features</span></span>

<span data-ttu-id="1e6c9-598">与 ASP.NET Core 2.x 一同发布的中间件不支持以下 IIS URL 重写模块功能：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-598">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="1e6c9-599">出站规则</span><span class="sxs-lookup"><span data-stu-id="1e6c9-599">Outbound Rules</span></span>
* <span data-ttu-id="1e6c9-600">自定义服务器变量</span><span class="sxs-lookup"><span data-stu-id="1e6c9-600">Custom Server Variables</span></span>
* <span data-ttu-id="1e6c9-601">通配符</span><span class="sxs-lookup"><span data-stu-id="1e6c9-601">Wildcards</span></span>
* <span data-ttu-id="1e6c9-602">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="1e6c9-602">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="1e6c9-603">支持的服务器变量</span><span class="sxs-lookup"><span data-stu-id="1e6c9-603">Supported server variables</span></span>

<span data-ttu-id="1e6c9-604">中间件支持下列 IIS URL 重写模块服务器变量：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-604">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="1e6c9-605">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="1e6c9-605">CONTENT_LENGTH</span></span>
* <span data-ttu-id="1e6c9-606">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="1e6c9-606">CONTENT_TYPE</span></span>
* <span data-ttu-id="1e6c9-607">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-607">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1e6c9-608">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1e6c9-608">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1e6c9-609">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1e6c9-609">HTTP_COOKIE</span></span>
* <span data-ttu-id="1e6c9-610">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1e6c9-610">HTTP_HOST</span></span>
* <span data-ttu-id="1e6c9-611">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1e6c9-611">HTTP_REFERER</span></span>
* <span data-ttu-id="1e6c9-612">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="1e6c9-612">HTTP_URL</span></span>
* <span data-ttu-id="1e6c9-613">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-613">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1e6c9-614">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1e6c9-614">HTTPS</span></span>
* <span data-ttu-id="1e6c9-615">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-615">LOCAL_ADDR</span></span>
* <span data-ttu-id="1e6c9-616">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1e6c9-616">QUERY_STRING</span></span>
* <span data-ttu-id="1e6c9-617">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e6c9-617">REMOTE_ADDR</span></span>
* <span data-ttu-id="1e6c9-618">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1e6c9-618">REMOTE_PORT</span></span>
* <span data-ttu-id="1e6c9-619">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e6c9-619">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1e6c9-620">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1e6c9-620">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="1e6c9-621">也可通过 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 获取 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-621">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="1e6c9-622">这种方法可为重写规则文件的位置提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-622">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="1e6c9-623">请确保将重写规则文件部署到所提供路径的服务器中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-623">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="1e6c9-624">基于方法的规则</span><span class="sxs-lookup"><span data-stu-id="1e6c9-624">Method-based rule</span></span>

<span data-ttu-id="1e6c9-625">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在方法中实现自己的规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-625">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="1e6c9-626">`Add` 公开 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，这使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用于方法中。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-626">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="1e6c9-627">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) 决定如何处理其他管道进程。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-627">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="1e6c9-628">将值设置为下表中的 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 字段之一。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-628">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="1e6c9-629">操作</span><span class="sxs-lookup"><span data-stu-id="1e6c9-629">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="1e6c9-630">`RuleResult.ContinueRules`（默认值）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-630">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="1e6c9-631">继续应用规则。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-631">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="1e6c9-632">停止应用规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-632">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="1e6c9-633">停止应用规则并将上下文发送给下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-633">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="1e6c9-634">示例应用演示了如何对以 .xml 结尾的路径的请求进行重定向  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-634">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="1e6c9-635">如果发出针对 `/file.xml` 的请求，请求将重定向到 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-635">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="1e6c9-636">状态代码设置为“301 (永久移动)”  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-636">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="1e6c9-637">当浏览器发出针对 /xmlfiles/file.xml 的新请求后，静态文件中间件会将文件从 wwwroot / xmlfiles 文件夹提供给客户端   。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-637">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="1e6c9-638">对于重定向，请显式设置响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-638">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="1e6c9-639">否则，将会返回“200 (正常)”状态代码，且客户端上不会发生重写  。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-639">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="1e6c9-640">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e6c9-640">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="1e6c9-641">此方法还可以重写请求。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-641">This approach can also rewrite requests.</span></span> <span data-ttu-id="1e6c9-642">示例应用演示了如何重写任何文本文件请求的路径以从 wwwroot 文件夹中提供 file.txt 文本文件   。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-642">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="1e6c9-643">静态文件中间件基于更新的请求路径来提供文件：</span><span class="sxs-lookup"><span data-stu-id="1e6c9-643">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="1e6c9-644">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e6c9-644">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="1e6c9-645">基于 IRule 的规则</span><span class="sxs-lookup"><span data-stu-id="1e6c9-645">IRule-based rule</span></span>

<span data-ttu-id="1e6c9-646">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在实现 <xref:Microsoft.AspNetCore.Rewrite.IRule> 接口的类中使用规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-646">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="1e6c9-647">与使用基于方法的规则方法相比，`IRule` 提供了更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-647">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="1e6c9-648">实现类可能包含构造函数，可在其中传入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的参数。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-648">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="1e6c9-649">检查示例应用中 `extension` 和 `newPath` 的参数值是否符合多个条件。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-649">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="1e6c9-650">`extension` 须包含一个值，并且该值必须是 .png、.jpg 或 .gif    。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-650">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="1e6c9-651">如果 `newPath` 无效，则会引发 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-651">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="1e6c9-652">如果发出针对 image.png 的请求，请求将重定向到  `/png-images/image.png`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-652">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="1e6c9-653">如果发出针对 image.png 的请求，请求将重定向到  `/jpg-images/image.jpg`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-653">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="1e6c9-654">状态代码设置为“301 (永久移动)”， *设置为停止处理规则并发送响应*`context.Result`。</span><span class="sxs-lookup"><span data-stu-id="1e6c9-654">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="1e6c9-655">原始请求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-655">Original Request: `/image.png`</span></span>

![开发人员工具正跟踪 image.png 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="1e6c9-657">原始请求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-657">Original Request: `/image.jpg`</span></span>

![开发人员工具正跟踪 image.jpg 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="1e6c9-659">正则表达式示例</span><span class="sxs-lookup"><span data-stu-id="1e6c9-659">Regex examples</span></span>

| <span data-ttu-id="1e6c9-660">目标</span><span class="sxs-lookup"><span data-stu-id="1e6c9-660">Goal</span></span> | <span data-ttu-id="1e6c9-661">正则表达式字符串和</span><span class="sxs-lookup"><span data-stu-id="1e6c9-661">Regex String &</span></span><br><span data-ttu-id="1e6c9-662">匹配示例</span><span class="sxs-lookup"><span data-stu-id="1e6c9-662">Match Example</span></span> | <span data-ttu-id="1e6c9-663">替换字符串和</span><span class="sxs-lookup"><span data-stu-id="1e6c9-663">Replacement String &</span></span><br><span data-ttu-id="1e6c9-664">输出示例</span><span class="sxs-lookup"><span data-stu-id="1e6c9-664">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="1e6c9-665">将路径重写为查询字符串</span><span class="sxs-lookup"><span data-stu-id="1e6c9-665">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="1e6c9-666">去掉尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="1e6c9-666">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="1e6c9-667">强制添加尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="1e6c9-667">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="1e6c9-668">避免重写特定请求</span><span class="sxs-lookup"><span data-stu-id="1e6c9-668">Avoid rewriting specific requests</span></span> | <span data-ttu-id="1e6c9-669">`^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-669">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="1e6c9-670">正确：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-670">Yes: `/resource.htm`</span></span><br><span data-ttu-id="1e6c9-671">错误：`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="1e6c9-671">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="1e6c9-672">重新排列 URL 段</span><span class="sxs-lookup"><span data-stu-id="1e6c9-672">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="1e6c9-673">替换 URL 段</span><span class="sxs-lookup"><span data-stu-id="1e6c9-673">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1e6c9-674">其他资源</span><span class="sxs-lookup"><span data-stu-id="1e6c9-674">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="1e6c9-675">.NET 中的正则表达式</span><span class="sxs-lookup"><span data-stu-id="1e6c9-675">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="1e6c9-676">正则表达式语言 - 快速参考</span><span class="sxs-lookup"><span data-stu-id="1e6c9-676">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="1e6c9-677">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1e6c9-677">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="1e6c9-678">使用 URL 重写模块 2.0（适用于 IIS）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-678">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* <span data-ttu-id="1e6c9-679">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-679">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span></span>
* [<span data-ttu-id="1e6c9-680">IIS URL 重写模块论坛</span><span class="sxs-lookup"><span data-stu-id="1e6c9-680">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* <span data-ttu-id="1e6c9-681">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)（保持简单的 URL 结构）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-681">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)</span></span>
* <span data-ttu-id="1e6c9-682">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)（10 个 URL 重写提示和技巧）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-682">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)</span></span>
* <span data-ttu-id="1e6c9-683">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)（用斜杠或不用斜杠）</span><span class="sxs-lookup"><span data-stu-id="1e6c9-683">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)</span></span>
