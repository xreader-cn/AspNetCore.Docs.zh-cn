---
title: ASP.NET Core 中的 URL 重写中间件
author: guardrex
description: 了解如何在 ASP.NET Core 应用程序中使用 URL 重写中间件进行 URL 重写和重定向。
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/url-rewriting
ms.openlocfilehash: 5be53baf4b9eb8774501fbf7f781370f7f687d0c
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814952"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="192a5-103">ASP.NET Core 中的 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="192a5-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="192a5-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="192a5-104">By [Luke Latham](https://github.com/guardrex) and [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="192a5-105">对于本主题的 1.1 版本，请下载 [ASP.NET Core（版本 1.1，PDF）中的 URL 重写中间件](https://webpifeed.blob.core.windows.net/webpifeed/Partners/URL_Rewriting_1.1.pdf)。</span><span class="sxs-lookup"><span data-stu-id="192a5-105">For the 1.1 version of this topic, download [URL Rewriting Middleware in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/URL_Rewriting_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="192a5-106">本文档介绍 URL 重写并说明如何在 ASP.NET Core 应用中使用 URL 重写中间件。</span><span class="sxs-lookup"><span data-stu-id="192a5-106">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="192a5-107">URL 重写是根据一个或多个预定义规则修改请求 URL 的行为。</span><span class="sxs-lookup"><span data-stu-id="192a5-107">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="192a5-108">URL 重写会在资源位置和地址之间创建一个抽象，使位置和地址不紧密相连。</span><span class="sxs-lookup"><span data-stu-id="192a5-108">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="192a5-109">在以下几种方案中，URL 重写很有价值：</span><span class="sxs-lookup"><span data-stu-id="192a5-109">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="192a5-110">暂时或永久移动或替换服务器资源，并维护这些资源的稳定定位符。</span><span class="sxs-lookup"><span data-stu-id="192a5-110">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="192a5-111">拆分在不同应用或同一应用的不同区域中处理的请求。</span><span class="sxs-lookup"><span data-stu-id="192a5-111">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="192a5-112">删除、添加或重新组织传入请求上的 URL 段。</span><span class="sxs-lookup"><span data-stu-id="192a5-112">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="192a5-113">优化搜索引擎优化 (SEO) 的公共 URL。</span><span class="sxs-lookup"><span data-stu-id="192a5-113">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="192a5-114">允许使用友好的公共 URL 来帮助访问者预测请求资源后返回的内容。</span><span class="sxs-lookup"><span data-stu-id="192a5-114">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="192a5-115">将不安全请求重定向到安全终结点。</span><span class="sxs-lookup"><span data-stu-id="192a5-115">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="192a5-116">防止热链接，外部站点会通过热链接将其他站点的资产链接到其自己的内容，从而利用托管在其他站点上的静态资产。</span><span class="sxs-lookup"><span data-stu-id="192a5-116">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="192a5-117">URL 重写可能会降低应用的性能。</span><span class="sxs-lookup"><span data-stu-id="192a5-117">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="192a5-118">如果可行，应限制规则的数量和复杂度。</span><span class="sxs-lookup"><span data-stu-id="192a5-118">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="192a5-119">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="192a5-119">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="192a5-120">URL 重定向和 URL 重写</span><span class="sxs-lookup"><span data-stu-id="192a5-120">URL redirect and URL rewrite</span></span>

<span data-ttu-id="192a5-121">URL 重定向和 URL 重写之间的用词差异很细微，但这对于向客户端提供资源具有重要意义   。</span><span class="sxs-lookup"><span data-stu-id="192a5-121">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="192a5-122">ASP.NET Core 的 URL 重写中间件能够满足两者的需求。</span><span class="sxs-lookup"><span data-stu-id="192a5-122">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="192a5-123">URL 重定向涉及客户端操作，指示客户端访问与客户端最初请求地址不同的资源  。</span><span class="sxs-lookup"><span data-stu-id="192a5-123">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="192a5-124">这需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="192a5-124">This requires a round trip to the server.</span></span> <span data-ttu-id="192a5-125">客户端对资源发出新请求时，返回客户端的重定向 URL 会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="192a5-125">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="192a5-126">如果 `/resource` 被重定向到 `/different-resource`，则服务器作出响应，指示客户端应在 `/different-resource` 获取资源，所提供的状态代码指示重定向是临时的还是永久的  。</span><span class="sxs-lookup"><span data-stu-id="192a5-126">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 服务终结点已暂时从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="192a5-132">将请求重定向到不同 URL 时，通过使用响应指定状态代码来指示重定向是永久还是临时：</span><span class="sxs-lookup"><span data-stu-id="192a5-132">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="192a5-133">如果资源有一个新的永久性 URL，并且你希望指示客户端所有将来的资源请求都使用新 URL，则使用“301 (永久移动)”状态代码  。</span><span class="sxs-lookup"><span data-stu-id="192a5-133">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="192a5-134">收到 301 状态代码时，客户端可能会缓存响应并重用这段代码  。</span><span class="sxs-lookup"><span data-stu-id="192a5-134">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="192a5-135">“302 (找到)”状态代码用于后列情况：重定向操作是临时的或通常会发生变化  。</span><span class="sxs-lookup"><span data-stu-id="192a5-135">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="192a5-136">302 状态代码向客户端指示不存储 URL 并在将来使用。</span><span class="sxs-lookup"><span data-stu-id="192a5-136">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="192a5-137">有关状态代码的详细信息，请参阅 [RFC 2616：状态代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="192a5-137">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="192a5-138">URL 重写是服务器端操作，它从与客户端请求的资源地址不同的资源地址提供资源  。</span><span class="sxs-lookup"><span data-stu-id="192a5-138">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="192a5-139">重写 URL 不需要往返服务器。</span><span class="sxs-lookup"><span data-stu-id="192a5-139">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="192a5-140">重写的 URL 不会返回客户端，也不会出现在浏览器地址栏。</span><span class="sxs-lookup"><span data-stu-id="192a5-140">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="192a5-141">如果 `/resource` 重写到 `/different-resource`，服务器会在内部提取并返回 `/different-resource` 处的资源   。</span><span class="sxs-lookup"><span data-stu-id="192a5-141">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="192a5-142">尽管客户端可能能够检索已重写 URL 处的资源，但是，客户端发出请求并收到响应时，并不知道已重写 URL 处存在的资源。</span><span class="sxs-lookup"><span data-stu-id="192a5-142">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 服务终结点已从服务器上的版本 1 (v1) 更改为版本 2 (v2)。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="192a5-147">URL 重写示例应用</span><span class="sxs-lookup"><span data-stu-id="192a5-147">URL rewriting sample app</span></span>

<span data-ttu-id="192a5-148">可使用[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)了解 URL 重写中间件的功能。</span><span class="sxs-lookup"><span data-stu-id="192a5-148">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="192a5-149">该应用程序应用重定向和重写规则，并显示多个方案的重定向或重写的 URL。</span><span class="sxs-lookup"><span data-stu-id="192a5-149">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="192a5-150">何时使用 URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="192a5-150">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="192a5-151">如果无法使用以下方法，请使用 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="192a5-151">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="192a5-152">在 Windows Server 上使用带 IIS 的 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="192a5-152">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="192a5-153">在 Apache 服务器上使用 Apache mod_rewrite 模块</span><span class="sxs-lookup"><span data-stu-id="192a5-153">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="192a5-154">Nginx 上的 URL 重写</span><span class="sxs-lookup"><span data-stu-id="192a5-154">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="192a5-155">此外，如果应用程序在 [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（旧称 WebListener）上托管，请使用中间件。</span><span class="sxs-lookup"><span data-stu-id="192a5-155">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="192a5-156">使用 IIS、Apache 和 Nginx 中的基于服务器的 URL 重写技术的主要原因：</span><span class="sxs-lookup"><span data-stu-id="192a5-156">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="192a5-157">中间件不支持这些模块的完整功能。</span><span class="sxs-lookup"><span data-stu-id="192a5-157">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="192a5-158">服务器模块的一些功能不适用于 ASP.NET Core 项目，例如 IIS 重写模块的 `IsFile` 和 `IsDirectory` 约束。</span><span class="sxs-lookup"><span data-stu-id="192a5-158">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="192a5-159">在这些情况下，请改为使用中间件。</span><span class="sxs-lookup"><span data-stu-id="192a5-159">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="192a5-160">中间件性能与模块性能不匹配。</span><span class="sxs-lookup"><span data-stu-id="192a5-160">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="192a5-161">基准测试是确定哪种方法会最大程度降低性能或降低的性能是否可忽略不计的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="192a5-161">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="192a5-162">Package</span><span class="sxs-lookup"><span data-stu-id="192a5-162">Package</span></span>

<span data-ttu-id="192a5-163">要在项目中包含中间件，请在项目文件中添加对 [Microsoft.AspNetCore.App 元数据包](xref:fundamentals/metapackage-app)的包引用，该文件包含 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 包。</span><span class="sxs-lookup"><span data-stu-id="192a5-163">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="192a5-164">不使用 `Microsoft.AspNetCore.App` 元包时，向 `Microsoft.AspNetCore.Rewrite` 包添加项目引用。</span><span class="sxs-lookup"><span data-stu-id="192a5-164">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="192a5-165">扩展和选项</span><span class="sxs-lookup"><span data-stu-id="192a5-165">Extension and options</span></span>

<span data-ttu-id="192a5-166">通过使用扩展方法为每条重写规则创建 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 类的实例，建立 URL 重写和重写定向规则。</span><span class="sxs-lookup"><span data-stu-id="192a5-166">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="192a5-167">按所需的处理顺序链接多个规则。</span><span class="sxs-lookup"><span data-stu-id="192a5-167">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="192a5-168">使用 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> 将 `RewriteOptions` 添加到请求管道时，它会被传递到 URL 重写中间件：</span><span class="sxs-lookup"><span data-stu-id="192a5-168">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="192a5-169">将非 www 重定向到 www</span><span class="sxs-lookup"><span data-stu-id="192a5-169">Redirect non-www to www</span></span>

<span data-ttu-id="192a5-170">三个选项允许应用将非 `www` 重新定向到 `www`：</span><span class="sxs-lookup"><span data-stu-id="192a5-170">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="192a5-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; 如果请求是非 `www`，则将请求永久重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="192a5-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="192a5-172">使用 [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="192a5-172">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="192a5-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; 如果传入请求是非 `www`，则将请求重定向到 `www` 子域。</span><span class="sxs-lookup"><span data-stu-id="192a5-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="192a5-174">使用 [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状态代码进行重定向。</span><span class="sxs-lookup"><span data-stu-id="192a5-174">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="192a5-175">重载允许提供响应状态代码。</span><span class="sxs-lookup"><span data-stu-id="192a5-175">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="192a5-176">使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 类的字段实现状态代码分配。</span><span class="sxs-lookup"><span data-stu-id="192a5-176">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="192a5-177">URL 重定向</span><span class="sxs-lookup"><span data-stu-id="192a5-177">URL redirect</span></span>

<span data-ttu-id="192a5-178">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> 将请求重定向。</span><span class="sxs-lookup"><span data-stu-id="192a5-178">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="192a5-179">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="192a5-179">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="192a5-180">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="192a5-180">The second parameter is the replacement string.</span></span> <span data-ttu-id="192a5-181">第三个参数（如有）指定状态代码。</span><span class="sxs-lookup"><span data-stu-id="192a5-181">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="192a5-182">如不指定状态代码，则状态代码默认为“302 (已找到)”，指示资源暂时移动或替换  。</span><span class="sxs-lookup"><span data-stu-id="192a5-182">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="192a5-183">在启用了开发人员工具的浏览器中，向路径为 `/redirect-rule/1234/5678` 的示例应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="192a5-183">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="192a5-184">正则表达式匹配 `redirect-rule/(.*)` 上的请求路径，且该路径会被 `/redirected/1234/5678` 替代。</span><span class="sxs-lookup"><span data-stu-id="192a5-184">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="192a5-185">重定向 URL 以“302 (已找到)”状态代码发回客户端  。</span><span class="sxs-lookup"><span data-stu-id="192a5-185">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="192a5-186">浏览器会在浏览器地址栏中出现的重定向 URL 上发出新请求。</span><span class="sxs-lookup"><span data-stu-id="192a5-186">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="192a5-187">由于示例应用中的规则都不匹配重定向 URL：</span><span class="sxs-lookup"><span data-stu-id="192a5-187">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="192a5-188">第二个请求从应用程序收到“200 (正常)”响应  。</span><span class="sxs-lookup"><span data-stu-id="192a5-188">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="192a5-189">响应正文显示了重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="192a5-189">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="192a5-190">重定向 URL 时，系统将向服务器进行一次往返  。</span><span class="sxs-lookup"><span data-stu-id="192a5-190">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="192a5-191">建立重定向规则时务必小心。</span><span class="sxs-lookup"><span data-stu-id="192a5-191">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="192a5-192">系统会根据应用的每个请求（包括重定向后的请求）对重定向规则进行评估。</span><span class="sxs-lookup"><span data-stu-id="192a5-192">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="192a5-193">很容易便会意外创建无限重定向循环  。</span><span class="sxs-lookup"><span data-stu-id="192a5-193">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="192a5-194">原始请求：`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="192a5-194">Original Request: `/redirect-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="192a5-196">括号内的表达式部分称为“捕获组”  。</span><span class="sxs-lookup"><span data-stu-id="192a5-196">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="192a5-197">表达式的点 (`.`) 表示匹配任何字符  。</span><span class="sxs-lookup"><span data-stu-id="192a5-197">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="192a5-198">星号 (`*`) 表示零次或多次匹配前面的字符  。</span><span class="sxs-lookup"><span data-stu-id="192a5-198">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="192a5-199">因此，URL 的最后两个路径段 `1234/5678` 由捕获组 `(.*)` 捕获。</span><span class="sxs-lookup"><span data-stu-id="192a5-199">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="192a5-200">在请求 URL 中提供的位于 `redirect-rule/` 之后的任何值均由此单个捕获组捕获。</span><span class="sxs-lookup"><span data-stu-id="192a5-200">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="192a5-201">在替换字符串中，将捕获组注入带有美元符号 (`$`)、后跟捕获序列号的字符串中。</span><span class="sxs-lookup"><span data-stu-id="192a5-201">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="192a5-202">获取的第一个捕获组值为 `$1`，第二个为 `$2`，并且正则表达式中的其他捕获组值将依次继续排列。</span><span class="sxs-lookup"><span data-stu-id="192a5-202">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="192a5-203">示例应用的重定向规则正则表达式中只有一个捕获组，因此替换字符串中只有一个注入组，即 `$1`。</span><span class="sxs-lookup"><span data-stu-id="192a5-203">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="192a5-204">如果应用此规则，URL 将变为 `/redirected/1234/5678`。</span><span class="sxs-lookup"><span data-stu-id="192a5-204">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="192a5-205">URL 重定向到安全的终结点</span><span class="sxs-lookup"><span data-stu-id="192a5-205">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="192a5-206">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> 将 HTTP 请求重定向到采用 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="192a5-206">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="192a5-207">如不提供状态代码，则中间件默认为“302(已找到)”  。</span><span class="sxs-lookup"><span data-stu-id="192a5-207">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="192a5-208">如果不提供端口：</span><span class="sxs-lookup"><span data-stu-id="192a5-208">If the port isn't supplied:</span></span>

* <span data-ttu-id="192a5-209">中间件默认为 `null`。</span><span class="sxs-lookup"><span data-stu-id="192a5-209">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="192a5-210">方案更改为 `https`（HTTPS 协议），客户端访问端口 443 上的资源。</span><span class="sxs-lookup"><span data-stu-id="192a5-210">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="192a5-211">下面的示例演示如何将状态代码设置为“301(永久移动)”并将端口更改为 5001  。</span><span class="sxs-lookup"><span data-stu-id="192a5-211">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="192a5-212">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> 将不安全的请求重定向到端口 443 上的采用安全 HTTPS 协议的相同主机和路径。</span><span class="sxs-lookup"><span data-stu-id="192a5-212">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="192a5-213">中间件将状态代码设置为“301 (永久移动)”  。</span><span class="sxs-lookup"><span data-stu-id="192a5-213">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="192a5-214">当重定向到安全的终结点并且不需要其他重定向规则时，建议使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="192a5-214">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="192a5-215">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl#require-https)主题。</span><span class="sxs-lookup"><span data-stu-id="192a5-215">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="192a5-216">示例应用能够演示如何使用 `AddRedirectToHttps` 或 `AddRedirectToHttpsPermanent`。</span><span class="sxs-lookup"><span data-stu-id="192a5-216">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="192a5-217">将扩展方法添加到 `RewriteOptions`。</span><span class="sxs-lookup"><span data-stu-id="192a5-217">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="192a5-218">在任何 URL 上向应用发出不安全的请求。</span><span class="sxs-lookup"><span data-stu-id="192a5-218">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="192a5-219">消除自签名证书不受信任的浏览器安全警告，或创建例外以信任证书。</span><span class="sxs-lookup"><span data-stu-id="192a5-219">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="192a5-220">使用 `AddRedirectToHttps(301, 5001)` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="192a5-220">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="192a5-222">使用 `AddRedirectToHttpsPermanent` 的原始请求：`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="192a5-222">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="192a5-224">URL 重写</span><span class="sxs-lookup"><span data-stu-id="192a5-224">URL rewrite</span></span>

<span data-ttu-id="192a5-225">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 创建重写 URL 的规则。</span><span class="sxs-lookup"><span data-stu-id="192a5-225">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="192a5-226">第一个参数包含用于匹配传入 URL 路径的正则表达式。</span><span class="sxs-lookup"><span data-stu-id="192a5-226">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="192a5-227">第二个参数是替换字符串。</span><span class="sxs-lookup"><span data-stu-id="192a5-227">The second parameter is the replacement string.</span></span> <span data-ttu-id="192a5-228">第三个参数 `skipRemainingRules: {true|false}` 指示如果当前规则适用，中间件是否要跳过其他重写规则。</span><span class="sxs-lookup"><span data-stu-id="192a5-228">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="192a5-229">原始请求：`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="192a5-229">Original Request: `/rewrite-rule/1234/5678`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="192a5-231">表达式开头的脱字号 (`^`) 意味着匹配从 URL 路径的开头处开始。</span><span class="sxs-lookup"><span data-stu-id="192a5-231">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="192a5-232">在前面的重定向规则 `redirect-rule/(.*)` 的示例中，正则表达式的开头没有脱字号 (`^`)。</span><span class="sxs-lookup"><span data-stu-id="192a5-232">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="192a5-233">因此，路径中 `redirect-rule/` 之前的任何字符都可能成功匹配。</span><span class="sxs-lookup"><span data-stu-id="192a5-233">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="192a5-234">路径</span><span class="sxs-lookup"><span data-stu-id="192a5-234">Path</span></span>                               | <span data-ttu-id="192a5-235">匹配</span><span class="sxs-lookup"><span data-stu-id="192a5-235">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="192a5-236">是</span><span class="sxs-lookup"><span data-stu-id="192a5-236">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="192a5-237">是</span><span class="sxs-lookup"><span data-stu-id="192a5-237">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="192a5-238">是</span><span class="sxs-lookup"><span data-stu-id="192a5-238">Yes</span></span>   |

<span data-ttu-id="192a5-239">重写规则 `^rewrite-rule/(\d+)/(\d+)` 只能与以 `rewrite-rule/` 开头的路径匹配。</span><span class="sxs-lookup"><span data-stu-id="192a5-239">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="192a5-240">注意下表中的匹配差异。</span><span class="sxs-lookup"><span data-stu-id="192a5-240">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="192a5-241">路径</span><span class="sxs-lookup"><span data-stu-id="192a5-241">Path</span></span>                              | <span data-ttu-id="192a5-242">匹配</span><span class="sxs-lookup"><span data-stu-id="192a5-242">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="192a5-243">是</span><span class="sxs-lookup"><span data-stu-id="192a5-243">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="192a5-244">否</span><span class="sxs-lookup"><span data-stu-id="192a5-244">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="192a5-245">No</span><span class="sxs-lookup"><span data-stu-id="192a5-245">No</span></span>    |

<span data-ttu-id="192a5-246">在表达式的 `^rewrite-rule/` 部分之后，有两个捕获组 `(\d+)/(\d+)`。</span><span class="sxs-lookup"><span data-stu-id="192a5-246">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="192a5-247">`\d` 表示与数字匹配  。</span><span class="sxs-lookup"><span data-stu-id="192a5-247">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="192a5-248">加号 (`+`) 表示与前面的一个或多个字符匹配  。</span><span class="sxs-lookup"><span data-stu-id="192a5-248">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="192a5-249">因此，URL 必须包含数字加正斜杠加另一个数字的形式。</span><span class="sxs-lookup"><span data-stu-id="192a5-249">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="192a5-250">这些捕获组以 `$1` 和 `$2` 的形式注入重写 URL 中。</span><span class="sxs-lookup"><span data-stu-id="192a5-250">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="192a5-251">重写规则替换字符串将捕获组放入查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="192a5-251">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="192a5-252">重写 `/rewrite-rule/1234/5678` 的请求路径，获取 `/rewritten?var1=1234&var2=5678` 处的资源。</span><span class="sxs-lookup"><span data-stu-id="192a5-252">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="192a5-253">如果原始请求中存在查询字符串，则重写 URL 时会保留此字符串。</span><span class="sxs-lookup"><span data-stu-id="192a5-253">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="192a5-254">无需往返服务器来获取资源。</span><span class="sxs-lookup"><span data-stu-id="192a5-254">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="192a5-255">如果资源存在，系统会提取资源并以“200（正常）”状态代码返回给客户端  。</span><span class="sxs-lookup"><span data-stu-id="192a5-255">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="192a5-256">因为客户端不会被重定向，所以浏览器地址栏中的 URL 不会发生更改。</span><span class="sxs-lookup"><span data-stu-id="192a5-256">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="192a5-257">客户端无法检测到服务器上发生的 URL 重写操作。</span><span class="sxs-lookup"><span data-stu-id="192a5-257">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="192a5-258">尽可能使用 `skipRemainingRules: true`，因为匹配规则在计算上很昂贵并且增加了应用响应时间。</span><span class="sxs-lookup"><span data-stu-id="192a5-258">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="192a5-259">对于最快的应用响应：</span><span class="sxs-lookup"><span data-stu-id="192a5-259">For the fastest app response:</span></span>
>
> * <span data-ttu-id="192a5-260">按照从最频繁匹配的规则到最不频繁匹配的规则排列重写规则。</span><span class="sxs-lookup"><span data-stu-id="192a5-260">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="192a5-261">如果出现匹配项且无需处理任何其他规则，则跳过剩余规则的处理。</span><span class="sxs-lookup"><span data-stu-id="192a5-261">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-modrewrite"></a><span data-ttu-id="192a5-262">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="192a5-262">Apache mod_rewrite</span></span>

<span data-ttu-id="192a5-263">使用 <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> 应用 Apache mod_rewrite 规则。</span><span class="sxs-lookup"><span data-stu-id="192a5-263">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="192a5-264">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="192a5-264">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="192a5-265">有关 mod_rewrite 规则的详细信息和示例，请参阅 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)。</span><span class="sxs-lookup"><span data-stu-id="192a5-265">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="192a5-266"><xref:System.IO.StreamReader> 用于读取 ApacheModRewrite.txt 规则文件中的规则  ：</span><span class="sxs-lookup"><span data-stu-id="192a5-266">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="192a5-267">示例应用将请求从 `/apache-mod-rules-redirect/(.\*)` 重定向到 `/redirected?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="192a5-267">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="192a5-268">响应状态代码为“302 (已找到)”  。</span><span class="sxs-lookup"><span data-stu-id="192a5-268">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="192a5-269">原始请求：`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="192a5-269">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="192a5-271">中间件支持下列 Apache mod_rewrite 服务器变量：</span><span class="sxs-lookup"><span data-stu-id="192a5-271">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="192a5-272">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="192a5-272">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="192a5-273">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="192a5-273">HTTP_ACCEPT</span></span>
* <span data-ttu-id="192a5-274">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="192a5-274">HTTP_CONNECTION</span></span>
* <span data-ttu-id="192a5-275">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="192a5-275">HTTP_COOKIE</span></span>
* <span data-ttu-id="192a5-276">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="192a5-276">HTTP_FORWARDED</span></span>
* <span data-ttu-id="192a5-277">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="192a5-277">HTTP_HOST</span></span>
* <span data-ttu-id="192a5-278">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="192a5-278">HTTP_REFERER</span></span>
* <span data-ttu-id="192a5-279">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="192a5-279">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="192a5-280">HTTPS</span><span class="sxs-lookup"><span data-stu-id="192a5-280">HTTPS</span></span>
* <span data-ttu-id="192a5-281">IPV6</span><span class="sxs-lookup"><span data-stu-id="192a5-281">IPV6</span></span>
* <span data-ttu-id="192a5-282">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="192a5-282">QUERY_STRING</span></span>
* <span data-ttu-id="192a5-283">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="192a5-283">REMOTE_ADDR</span></span>
* <span data-ttu-id="192a5-284">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="192a5-284">REMOTE_PORT</span></span>
* <span data-ttu-id="192a5-285">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="192a5-285">REQUEST_FILENAME</span></span>
* <span data-ttu-id="192a5-286">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="192a5-286">REQUEST_METHOD</span></span>
* <span data-ttu-id="192a5-287">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="192a5-287">REQUEST_SCHEME</span></span>
* <span data-ttu-id="192a5-288">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="192a5-288">REQUEST_URI</span></span>
* <span data-ttu-id="192a5-289">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="192a5-289">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="192a5-290">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="192a5-290">SERVER_ADDR</span></span>
* <span data-ttu-id="192a5-291">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="192a5-291">SERVER_PORT</span></span>
* <span data-ttu-id="192a5-292">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="192a5-292">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="192a5-293">TIME</span><span class="sxs-lookup"><span data-stu-id="192a5-293">TIME</span></span>
* <span data-ttu-id="192a5-294">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="192a5-294">TIME_DAY</span></span>
* <span data-ttu-id="192a5-295">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="192a5-295">TIME_HOUR</span></span>
* <span data-ttu-id="192a5-296">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="192a5-296">TIME_MIN</span></span>
* <span data-ttu-id="192a5-297">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="192a5-297">TIME_MON</span></span>
* <span data-ttu-id="192a5-298">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="192a5-298">TIME_SEC</span></span>
* <span data-ttu-id="192a5-299">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="192a5-299">TIME_WDAY</span></span>
* <span data-ttu-id="192a5-300">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="192a5-300">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="192a5-301">IIS URL 重写模块规则</span><span class="sxs-lookup"><span data-stu-id="192a5-301">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="192a5-302">若要使用适用于 IIS URL 重写模块的同一规则集，使用 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>。</span><span class="sxs-lookup"><span data-stu-id="192a5-302">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="192a5-303">请确保将规则文件与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="192a5-303">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="192a5-304">当在 Windows Server IIS 上运行时，请勿指示中间件使用应用的 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="192a5-304">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="192a5-305">使用 IIS 时，应将这些规则存储在应用的 web.config 文件之外，以避免与 IIS 重写模块发生冲突  。</span><span class="sxs-lookup"><span data-stu-id="192a5-305">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="192a5-306">有关 IIS URL 重写模块规则的详细信息和示例，请参阅 [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)（使用 URL 重写模块 2.0）和 [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）。</span><span class="sxs-lookup"><span data-stu-id="192a5-306">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="192a5-307"><xref:System.IO.StreamReader> 用于读取 IISUrlRewrite.xml 规则文件中的规则  ：</span><span class="sxs-lookup"><span data-stu-id="192a5-307">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="192a5-308">示例应用将请求从 `/iis-rules-rewrite/(.*)` 重写为 `/rewritten?id=$1`。</span><span class="sxs-lookup"><span data-stu-id="192a5-308">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="192a5-309">以“200 (正常)”状态代码作为响应发送到客户端  。</span><span class="sxs-lookup"><span data-stu-id="192a5-309">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="192a5-310">原始请求：`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="192a5-310">Original Request: `/iis-rules-rewrite/1234`</span></span>

![开发人员工具正跟踪请求和响应的浏览器窗口](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="192a5-312">如果有配置了服务器级别规则（可对应用产生不利影响）的活动 IIS 重写模块，则可禁用应用的 IIS 重写模块。</span><span class="sxs-lookup"><span data-stu-id="192a5-312">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="192a5-313">有关详细信息，请参阅[禁用 IIS 模块](xref:host-and-deploy/iis/modules#disabling-iis-modules)。</span><span class="sxs-lookup"><span data-stu-id="192a5-313">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="192a5-314">不支持的功能</span><span class="sxs-lookup"><span data-stu-id="192a5-314">Unsupported features</span></span>

<span data-ttu-id="192a5-315">与 ASP.NET Core 2.x 一同发布的中间件不支持以下 IIS URL 重写模块功能：</span><span class="sxs-lookup"><span data-stu-id="192a5-315">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="192a5-316">出站规则</span><span class="sxs-lookup"><span data-stu-id="192a5-316">Outbound Rules</span></span>
* <span data-ttu-id="192a5-317">自定义服务器变量</span><span class="sxs-lookup"><span data-stu-id="192a5-317">Custom Server Variables</span></span>
* <span data-ttu-id="192a5-318">通配符</span><span class="sxs-lookup"><span data-stu-id="192a5-318">Wildcards</span></span>
* <span data-ttu-id="192a5-319">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="192a5-319">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="192a5-320">受支持的服务器变量</span><span class="sxs-lookup"><span data-stu-id="192a5-320">Supported server variables</span></span>

<span data-ttu-id="192a5-321">中间件支持下列 IIS URL 重写模块服务器变量：</span><span class="sxs-lookup"><span data-stu-id="192a5-321">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="192a5-322">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="192a5-322">CONTENT_LENGTH</span></span>
* <span data-ttu-id="192a5-323">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="192a5-323">CONTENT_TYPE</span></span>
* <span data-ttu-id="192a5-324">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="192a5-324">HTTP_ACCEPT</span></span>
* <span data-ttu-id="192a5-325">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="192a5-325">HTTP_CONNECTION</span></span>
* <span data-ttu-id="192a5-326">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="192a5-326">HTTP_COOKIE</span></span>
* <span data-ttu-id="192a5-327">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="192a5-327">HTTP_HOST</span></span>
* <span data-ttu-id="192a5-328">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="192a5-328">HTTP_REFERER</span></span>
* <span data-ttu-id="192a5-329">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="192a5-329">HTTP_URL</span></span>
* <span data-ttu-id="192a5-330">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="192a5-330">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="192a5-331">HTTPS</span><span class="sxs-lookup"><span data-stu-id="192a5-331">HTTPS</span></span>
* <span data-ttu-id="192a5-332">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="192a5-332">LOCAL_ADDR</span></span>
* <span data-ttu-id="192a5-333">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="192a5-333">QUERY_STRING</span></span>
* <span data-ttu-id="192a5-334">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="192a5-334">REMOTE_ADDR</span></span>
* <span data-ttu-id="192a5-335">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="192a5-335">REMOTE_PORT</span></span>
* <span data-ttu-id="192a5-336">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="192a5-336">REQUEST_FILENAME</span></span>
* <span data-ttu-id="192a5-337">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="192a5-337">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="192a5-338">也可通过 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 获取 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="192a5-338">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="192a5-339">这种方法可为重写规则文件的位置提供更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="192a5-339">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="192a5-340">请确保将重写规则文件部署到所提供路径的服务器中。</span><span class="sxs-lookup"><span data-stu-id="192a5-340">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="192a5-341">基于方法的规则</span><span class="sxs-lookup"><span data-stu-id="192a5-341">Method-based rule</span></span>

<span data-ttu-id="192a5-342">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在方法中实现自己的规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="192a5-342">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="192a5-343">`Add` 公开 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>，这使 <xref:Microsoft.AspNetCore.Http.HttpContext> 可用于方法中。</span><span class="sxs-lookup"><span data-stu-id="192a5-343">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="192a5-344">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) 决定如何处理其他管道进程。</span><span class="sxs-lookup"><span data-stu-id="192a5-344">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="192a5-345">将值设置为下表中的 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 字段之一。</span><span class="sxs-lookup"><span data-stu-id="192a5-345">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="192a5-346">操作</span><span class="sxs-lookup"><span data-stu-id="192a5-346">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="192a5-347">`RuleResult.ContinueRules`（默认值）</span><span class="sxs-lookup"><span data-stu-id="192a5-347">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="192a5-348">继续应用规则。</span><span class="sxs-lookup"><span data-stu-id="192a5-348">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="192a5-349">停止应用规则并发送响应。</span><span class="sxs-lookup"><span data-stu-id="192a5-349">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="192a5-350">停止应用规则并将上下文发送给下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="192a5-350">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="192a5-351">示例应用演示了如何对以 .xml 结尾的路径的请求进行重定向  。</span><span class="sxs-lookup"><span data-stu-id="192a5-351">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="192a5-352">如果发出针对 `/file.xml` 的请求，请求将重定向到 `/xmlfiles/file.xml`。</span><span class="sxs-lookup"><span data-stu-id="192a5-352">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="192a5-353">状态代码设置为“301 (永久移动)”  。</span><span class="sxs-lookup"><span data-stu-id="192a5-353">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="192a5-354">当浏览器发出针对 /xmlfiles/file.xml 的新请求后，静态文件中间件会将文件从 wwwroot / xmlfiles 文件夹提供给客户端   。</span><span class="sxs-lookup"><span data-stu-id="192a5-354">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="192a5-355">对于重定向，请显式设置响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="192a5-355">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="192a5-356">否则，将会返回“200 (正常)”状态代码，且客户端上不会发生重写  。</span><span class="sxs-lookup"><span data-stu-id="192a5-356">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="192a5-357">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="192a5-357">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="192a5-358">此方法还可以重写请求。</span><span class="sxs-lookup"><span data-stu-id="192a5-358">This approach can also rewrite requests.</span></span> <span data-ttu-id="192a5-359">示例应用演示了如何重写任何文本文件请求的路径以从 wwwroot 文件夹中提供 file.txt 文本文件   。</span><span class="sxs-lookup"><span data-stu-id="192a5-359">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="192a5-360">静态文件中间件基于更新的请求路径来提供文件：</span><span class="sxs-lookup"><span data-stu-id="192a5-360">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="192a5-361">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="192a5-361">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="192a5-362">基于 IRule 的规则</span><span class="sxs-lookup"><span data-stu-id="192a5-362">IRule-based rule</span></span>

<span data-ttu-id="192a5-363">使用 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> 在实现 <xref:Microsoft.AspNetCore.Rewrite.IRule> 接口的类中使用规则逻辑。</span><span class="sxs-lookup"><span data-stu-id="192a5-363">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="192a5-364">与使用基于方法的规则方法相比，`IRule` 提供了更大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="192a5-364">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="192a5-365">实现类可能包含构造函数，可在其中传入 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 方法的参数。</span><span class="sxs-lookup"><span data-stu-id="192a5-365">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="192a5-366">检查示例应用中 `extension` 和 `newPath` 的参数值是否符合多个条件。</span><span class="sxs-lookup"><span data-stu-id="192a5-366">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="192a5-367">`extension` 须包含一个值，并且该值必须是 .png、.jpg 或 .gif    。</span><span class="sxs-lookup"><span data-stu-id="192a5-367">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="192a5-368">如果 `newPath` 无效，则会引发 <xref:System.ArgumentException>。</span><span class="sxs-lookup"><span data-stu-id="192a5-368">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="192a5-369">如果发出针对 image.png 的请求，请求将重定向到 `/png-images/image.png`  。</span><span class="sxs-lookup"><span data-stu-id="192a5-369">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="192a5-370">如果发出针对 image.png 的请求，请求将重定向到 `/jpg-images/image.jpg`  。</span><span class="sxs-lookup"><span data-stu-id="192a5-370">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="192a5-371">状态代码设置为“301 (永久移动)”，`context.Result` 设置为停止处理规则并发送响应  。</span><span class="sxs-lookup"><span data-stu-id="192a5-371">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="192a5-372">原始请求：`/image.png`</span><span class="sxs-lookup"><span data-stu-id="192a5-372">Original Request: `/image.png`</span></span>

![开发人员工具正跟踪 image.png 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="192a5-374">原始请求：`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="192a5-374">Original Request: `/image.jpg`</span></span>

![开发人员工具正跟踪 image.jpg 的请求和响应的浏览器窗口](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="192a5-376">正则表达式示例</span><span class="sxs-lookup"><span data-stu-id="192a5-376">Regex examples</span></span>

| <span data-ttu-id="192a5-377">目标</span><span class="sxs-lookup"><span data-stu-id="192a5-377">Goal</span></span> | <span data-ttu-id="192a5-378">正则表达式字符串和</span><span class="sxs-lookup"><span data-stu-id="192a5-378">Regex String &</span></span><br><span data-ttu-id="192a5-379">匹配示例</span><span class="sxs-lookup"><span data-stu-id="192a5-379">Match Example</span></span> | <span data-ttu-id="192a5-380">替换字符串和</span><span class="sxs-lookup"><span data-stu-id="192a5-380">Replacement String &</span></span><br><span data-ttu-id="192a5-381">输出示例</span><span class="sxs-lookup"><span data-stu-id="192a5-381">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="192a5-382">将路径重写为查询字符串</span><span class="sxs-lookup"><span data-stu-id="192a5-382">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="192a5-383">去掉尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="192a5-383">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="192a5-384">强制添加尾部反斜杠</span><span class="sxs-lookup"><span data-stu-id="192a5-384">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="192a5-385">避免重写特定请求</span><span class="sxs-lookup"><span data-stu-id="192a5-385">Avoid rewriting specific requests</span></span> | <span data-ttu-id="192a5-386">`^(.*)(?<!\.axd)$` 或 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="192a5-386">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="192a5-387">正确：`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="192a5-387">Yes: `/resource.htm`</span></span><br><span data-ttu-id="192a5-388">错误：`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="192a5-388">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="192a5-389">重新排列 URL 段</span><span class="sxs-lookup"><span data-stu-id="192a5-389">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="192a5-390">替换 URL 段</span><span class="sxs-lookup"><span data-stu-id="192a5-390">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a><span data-ttu-id="192a5-391">其他资源</span><span class="sxs-lookup"><span data-stu-id="192a5-391">Additional resources</span></span>

* [<span data-ttu-id="192a5-392">应用程序启动</span><span class="sxs-lookup"><span data-stu-id="192a5-392">Application Startup</span></span>](startup.md)
* [<span data-ttu-id="192a5-393">中间件</span><span class="sxs-lookup"><span data-stu-id="192a5-393">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="192a5-394">.NET 中的正则表达式</span><span class="sxs-lookup"><span data-stu-id="192a5-394">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="192a5-395">正则表达式语言 - 快速参考</span><span class="sxs-lookup"><span data-stu-id="192a5-395">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="192a5-396">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="192a5-396">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="192a5-397">使用 URL 重写模块 2.0（适用于 IIS）</span><span class="sxs-lookup"><span data-stu-id="192a5-397">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* <span data-ttu-id="192a5-398">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)（URL 重写模块配置引用）</span><span class="sxs-lookup"><span data-stu-id="192a5-398">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span></span>
* [<span data-ttu-id="192a5-399">IIS URL 重写模块论坛</span><span class="sxs-lookup"><span data-stu-id="192a5-399">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* <span data-ttu-id="192a5-400">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)（保持简单的 URL 结构）</span><span class="sxs-lookup"><span data-stu-id="192a5-400">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)</span></span>
* <span data-ttu-id="192a5-401">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)（10 个 URL 重写提示和技巧）</span><span class="sxs-lookup"><span data-stu-id="192a5-401">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)</span></span>
* <span data-ttu-id="192a5-402">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)（用斜杠或不用斜杠）</span><span class="sxs-lookup"><span data-stu-id="192a5-402">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)</span></span>
