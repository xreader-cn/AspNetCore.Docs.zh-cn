---
title: 配置 ASP.NET Core 以使用代理服务器和负载均衡器
author: rick-anderson
description: 了解在代理服务器和负载均衡器后方托管的应用程序的配置，这通常会隐藏重要的请求信息。
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e329b8604b820818167a563b3a21f01f2ab2a6ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776378"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="6e24a-103">配置 ASP.NET Core 以使用代理服务器和负载均衡器</span><span class="sxs-lookup"><span data-stu-id="6e24a-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="6e24a-104">作者：[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="6e24a-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6e24a-105">在 ASP.NET Core 推荐配置中，应用使用 IIS/ASP.NET Core 模块、Nginx 或 Apache 进行托管。</span><span class="sxs-lookup"><span data-stu-id="6e24a-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="6e24a-106">代理服务器、负载均衡器和其他网络设备通常会在请求到达应用之前隐藏有关请求的信息：</span><span class="sxs-lookup"><span data-stu-id="6e24a-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="6e24a-107">当通过 HTTP 代理 HTTPS 请求时，原方案 (HTTPS) 将丢失，并且必须在标头中转接。</span><span class="sxs-lookup"><span data-stu-id="6e24a-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="6e24a-108">由于应用收到来自代理的请求，而不是 Internet 或公司网络上请求的真实源，因此原始客户端 IP 地址也必须在标头中转接。</span><span class="sxs-lookup"><span data-stu-id="6e24a-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="6e24a-109">此信息在请求处理中可能很重要，例如在重定向、身份验证、链接生成、策略评估和客户端地理位置中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="6e24a-110">转接头</span><span class="sxs-lookup"><span data-stu-id="6e24a-110">Forwarded headers</span></span>

<span data-ttu-id="6e24a-111">按照约定，代理转接 HTTP 标头中的信息。</span><span class="sxs-lookup"><span data-stu-id="6e24a-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="6e24a-112">Header</span><span class="sxs-lookup"><span data-stu-id="6e24a-112">Header</span></span> | <span data-ttu-id="6e24a-113">描述</span><span class="sxs-lookup"><span data-stu-id="6e24a-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="6e24a-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="6e24a-114">X-Forwarded-For</span></span> | <span data-ttu-id="6e24a-115">保存代理链中关于发起请求的客户端和后续代理的信息。</span><span class="sxs-lookup"><span data-stu-id="6e24a-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="6e24a-116">该参数可能包含 IP 地址（以及可选端口号）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="6e24a-117">在代理服务器链中，第一个参数表示最初发出请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="6e24a-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="6e24a-118">后续代理标识符以此类推。</span><span class="sxs-lookup"><span data-stu-id="6e24a-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="6e24a-119">链中的最后一个代理不在参数列表中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="6e24a-120">最后一个代理的 IP 地址以及可选的端口号可用作传输层的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="6e24a-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="6e24a-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="6e24a-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="6e24a-122">原方案的值 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="6e24a-123">如果请求已遍历多个代理，则该值也可以是方案列表。</span><span class="sxs-lookup"><span data-stu-id="6e24a-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="6e24a-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="6e24a-124">X-Forwarded-Host</span></span> | <span data-ttu-id="6e24a-125">主机标头字段的原始值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-125">The original value of the Host header field.</span></span> <span data-ttu-id="6e24a-126">代理通常不会修改主机标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="6e24a-127">有关特权提升漏洞的信息，请参阅 [Microsoft 安全公告 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)，该漏洞影响代理未验证或将主机标头限制为已知正确值的系统。</span><span class="sxs-lookup"><span data-stu-id="6e24a-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="6e24a-128">来自 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 包的转接头中间件读取这些标头，并填充 <xref:Microsoft.AspNetCore.Http.HttpContext> 上的关联字段。</span><span class="sxs-lookup"><span data-stu-id="6e24a-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="6e24a-129">中间件更新：</span><span class="sxs-lookup"><span data-stu-id="6e24a-129">The middleware updates:</span></span>

* <span data-ttu-id="6e24a-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; 使用 `X-Forwarded-For` 标头值进行设置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="6e24a-131">影响中间件如何设置 `RemoteIpAddress` 的其他设置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="6e24a-132">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="6e24a-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; 使用 `X-Forwarded-Proto` 标头值进行设置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="6e24a-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; 使用 `X-Forwarded-Host` 标头值进行设置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="6e24a-135">可以配置转接头中间件[默认设置](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="6e24a-136">默认设置为：</span><span class="sxs-lookup"><span data-stu-id="6e24a-136">The default settings are:</span></span>

* <span data-ttu-id="6e24a-137">应用和请求源之间只有一个代理  。</span><span class="sxs-lookup"><span data-stu-id="6e24a-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="6e24a-138">仅将环回地址配置为已知代理和已知网络。</span><span class="sxs-lookup"><span data-stu-id="6e24a-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="6e24a-139">转接头被命名为 `X-Forwarded-For` 和 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="6e24a-140">并非所有网络设备均可在没有其他配置的情况下添加 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="6e24a-141">如果代理请求在到达应用时未包含这些标头，请查阅设备制造商提供的指导。</span><span class="sxs-lookup"><span data-stu-id="6e24a-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="6e24a-142">如果设备使用 `X-Forwarded-For` 和 `X-Forwarded-Proto` 以外的其他标头名称，请设置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 和 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 选项，使其与设备所用的标头名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="6e24a-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="6e24a-143">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)和[配置使用不同标头名称的代理](#configuration-for-a-proxy-that-uses-different-header-names)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="6e24a-144">IIS/IIS Express 和 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="6e24a-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="6e24a-145">当应用托管在 IIS 和 ASP.NET Core 模块后方的[进程外](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)时，转接头中间件由 [IIS 集成中间件](xref:host-and-deploy/iis/index#out-of-process-hosting-model)默认启用。</span><span class="sxs-lookup"><span data-stu-id="6e24a-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="6e24a-146">由于转接头的信任问题（例如，[IP 欺骗](https://www.iplocation.net/ip-spoofing)），转接头中间件被激活为首先在中间件管道中运行，并具有特定于 ASP.NET Core 模块的受限配置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="6e24a-147">中间件配置为转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，并且被限制到单个本地 localhost 代理。</span><span class="sxs-lookup"><span data-stu-id="6e24a-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="6e24a-148">如果需要其他配置，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6e24a-149">其他代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="6e24a-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6e24a-150">除在[进程外](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)托管时使用 [IIS 集成](xref:host-and-deploy/iis/index#out-of-process-hosting-model)之外，不会默认启用转接头中间件。</span><span class="sxs-lookup"><span data-stu-id="6e24a-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="6e24a-151">必须启用应用的转接头中间件才能处理带有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的转接头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="6e24a-152">启用中间件后，如果没有为中间件指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，那么默认的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="6e24a-153">为中间件配置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 以转接 `X-Forwarded-For` 中的 `X-Forwarded-Proto` 和 `Startup.ConfigureServices` 标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e24a-154">调用其他中间件之前，先调用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 中的 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="6e24a-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="6e24a-155">如果没有在 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 中指定任何 `Startup.ConfigureServices`，或未使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 直接指定到扩展方法，则要转接的默认标头为 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="6e24a-156">必须为 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 属性配置要转接的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="6e24a-157">Nginx 配置</span><span class="sxs-lookup"><span data-stu-id="6e24a-157">Nginx configuration</span></span>

<span data-ttu-id="6e24a-158">要转发 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-nginx#configure-nginx>。</span><span class="sxs-lookup"><span data-stu-id="6e24a-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="6e24a-159">有关详细信息，请参阅 [NGINX：使用转接头](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="6e24a-160">Apache 配置</span><span class="sxs-lookup"><span data-stu-id="6e24a-160">Apache configuration</span></span>

<span data-ttu-id="6e24a-161">`X-Forwarded-For` 将会自动添加（请参阅 [Apache 模块 mod_proxy：反向代理请求标头](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="6e24a-162">要了解如何转发 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-apache#configure-apache>。</span><span class="sxs-lookup"><span data-stu-id="6e24a-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="6e24a-163">转接头中间件选项</span><span class="sxs-lookup"><span data-stu-id="6e24a-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="6e24a-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 控制转接头中间件的行为。</span><span class="sxs-lookup"><span data-stu-id="6e24a-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="6e24a-165">以下示例更改了默认值：</span><span class="sxs-lookup"><span data-stu-id="6e24a-165">The following example changes the default values:</span></span>

* <span data-ttu-id="6e24a-166">将转接头中的条目数量限制为 `2`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="6e24a-167">添加已知的代理地址 `127.0.10.1`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="6e24a-168">将转接头名称从默认的 `X-Forwarded-For` 更改为 `X-Forwarded-For-My-Custom-Header-Name`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="6e24a-169">选项</span><span class="sxs-lookup"><span data-stu-id="6e24a-169">Option</span></span> | <span data-ttu-id="6e24a-170">描述</span><span class="sxs-lookup"><span data-stu-id="6e24a-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="6e24a-171">通过 `X-Forwarded-Host` 标头将主机限制为提供的值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="6e24a-172">使用 ordinal-ignore-case 比较值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="6e24a-173">必须排除端口号。</span><span class="sxs-lookup"><span data-stu-id="6e24a-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="6e24a-174">如果列表为空，则允许使用所有主机。</span><span class="sxs-lookup"><span data-stu-id="6e24a-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="6e24a-175">顶级通配符 `*` 允许所有非空主机。</span><span class="sxs-lookup"><span data-stu-id="6e24a-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="6e24a-176">子域通配符允许使用，但不匹配根域。</span><span class="sxs-lookup"><span data-stu-id="6e24a-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="6e24a-177">例如，`*.contoso.com` 匹配子域 `foo.contoso.com`，但不匹配根域 `contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="6e24a-178">允许使用 Unicode 主机名，但应转换为 [Punycode](https://tools.ietf.org/html/rfc3492) 进行匹配。</span><span class="sxs-lookup"><span data-stu-id="6e24a-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="6e24a-179">[IPv6 地址](https://tools.ietf.org/html/rfc4291)必须包括边界方括号，而且必须位于[常规窗体](https://tools.ietf.org/html/rfc4291#section-2.2)（例如，`[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`）中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="6e24a-180">IPv6 地址并非专门用于检查不同格式之间的逻辑相等性，也不执行标准化。</span><span class="sxs-lookup"><span data-stu-id="6e24a-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="6e24a-181">未能限制允许的主机可能会允许攻击者访问该服务生成的欺骗链接。</span><span class="sxs-lookup"><span data-stu-id="6e24a-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="6e24a-182">默认值为空的 `IList<string>`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="6e24a-183">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="6e24a-184">如果代理/转发器不使用 `X-Forwarded-For` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="6e24a-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6e24a-185">默认值为 `X-Forwarded-For`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="6e24a-186">标识应处理的转发器。</span><span class="sxs-lookup"><span data-stu-id="6e24a-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="6e24a-187">对于应用的字段的列表，请参阅 [ForwardedHeaders 枚举](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="6e24a-188">分配给此属性的典型值为 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="6e24a-189">默认值是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="6e24a-190">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="6e24a-191">如果代理/转发器不使用 `X-Forwarded-Host` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="6e24a-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6e24a-192">默认值为 `X-Forwarded-Host`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="6e24a-193">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="6e24a-194">如果代理/转发器不使用 `X-Forwarded-Proto` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="6e24a-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6e24a-195">默认值为 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="6e24a-196">限制所处理的标头中的条目数。</span><span class="sxs-lookup"><span data-stu-id="6e24a-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="6e24a-197">设置为 `null` 以禁用此限制，但仅应在已配置 `KnownProxies` 或 `KnownNetworks` 的情况下执行此操作。</span><span class="sxs-lookup"><span data-stu-id="6e24a-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="6e24a-198">设置非 `null` 值是一种预防措施（但不是保证），防止错误配置的代理和恶意请求从网络的侧通道到达。</span><span class="sxs-lookup"><span data-stu-id="6e24a-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="6e24a-199">转接头中间件按照从右向左的相反顺序处理标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="6e24a-200">如果使用默认值 (`1`)，则只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="6e24a-201">默认值为 `1`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="6e24a-202">从中接受转接头的已知网络的地址范围。</span><span class="sxs-lookup"><span data-stu-id="6e24a-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="6e24a-203">使用无类别域际路由选择 (CIDR) 表示法提供 IP 范围。</span><span class="sxs-lookup"><span data-stu-id="6e24a-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="6e24a-204">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="6e24a-205">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6e24a-206">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="6e24a-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="6e24a-207">有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。</span><span class="sxs-lookup"><span data-stu-id="6e24a-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="6e24a-208">默认值是 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>，其中包含 `IPAddress.Loopback` 的单个条目。</span><span class="sxs-lookup"><span data-stu-id="6e24a-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="6e24a-209">从中接受转接头的已知代理的地址。</span><span class="sxs-lookup"><span data-stu-id="6e24a-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="6e24a-210">使用 `KnownProxies` 指定精确的 IP 地址匹配。</span><span class="sxs-lookup"><span data-stu-id="6e24a-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="6e24a-211">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="6e24a-212">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6e24a-213">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="6e24a-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="6e24a-214">有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。</span><span class="sxs-lookup"><span data-stu-id="6e24a-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="6e24a-215">默认值是 `IList`\<<xref:System.Net.IPAddress>>，其中包含 `IPAddress.IPv6Loopback` 的单个条目。</span><span class="sxs-lookup"><span data-stu-id="6e24a-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="6e24a-216">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="6e24a-217">默认值为 `X-Original-For`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="6e24a-218">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="6e24a-219">默认值为 `X-Original-Host`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="6e24a-220">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="6e24a-221">默认值为 `X-Original-Proto`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="6e24a-222">要求正在处理的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 之间的标头值数量保持同步。</span><span class="sxs-lookup"><span data-stu-id="6e24a-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="6e24a-223">ASP.NET Core 1.x 默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="6e24a-224">ASP.NET Core 2.0 默认值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="6e24a-225">方案与使用案例</span><span class="sxs-lookup"><span data-stu-id="6e24a-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="6e24a-226">无法添加转接头并且所有请求都安全的情况</span><span class="sxs-lookup"><span data-stu-id="6e24a-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="6e24a-227">在某些情况下，可能无法将转接头添加到代理到应用的请求中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="6e24a-228">如果代理强制将所有公共外部请求执行为 HTTPS，则在使用任何类型的中间件之前，可以在 `Startup.Configure` 中手动设置该方案：</span><span class="sxs-lookup"><span data-stu-id="6e24a-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="6e24a-229">此代码可以通过环境变量或者开发环境或过渡环境中的其他配置设置来禁用。</span><span class="sxs-lookup"><span data-stu-id="6e24a-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="6e24a-230">处理改变请求路径的基路径和代理</span><span class="sxs-lookup"><span data-stu-id="6e24a-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="6e24a-231">一些代理可完整地传递路径，但是会删除应用基路径以便路由可正常工作。</span><span class="sxs-lookup"><span data-stu-id="6e24a-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="6e24a-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 中间件将路径拆分为 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)，将应用基路径拆分为 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="6e24a-233">如果 `/foo` 是作为 `/foo/api/1` 传递的代理路径的应用基路径，则中间件使用以下命令将 `Request.PathBase` 设置为 `/foo`，将 `Request.Path` 设置为 `/api/1`：</span><span class="sxs-lookup"><span data-stu-id="6e24a-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="6e24a-234">当再次反向调用中间件时，将重新应用原始路径和基路径。</span><span class="sxs-lookup"><span data-stu-id="6e24a-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="6e24a-235">要详细了解中间件处理顺序，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="6e24a-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="6e24a-236">如果代理剪裁路径（例如，将 `/foo/api/1` 转接到 `/api/1`），请通过设置请求的 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 属性来修复重定向和链接：</span><span class="sxs-lookup"><span data-stu-id="6e24a-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="6e24a-237">如果代理要添加路径数据，请使用 <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> 并分配给 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 属性，从而放弃部分路径以修复重定向和链接：</span><span class="sxs-lookup"><span data-stu-id="6e24a-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="6e24a-238">配置使用不同标头名称的代理</span><span class="sxs-lookup"><span data-stu-id="6e24a-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="6e24a-239">如果代理不使用名为 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的标头来转发代理地址/端口和原始架构信息，则设置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 和 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 选项，使其与代理所用的标头名称相匹配：</span><span class="sxs-lookup"><span data-stu-id="6e24a-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="6e24a-240">对表示为 IPv6 地址的 IPv4 地址进行配置</span><span class="sxs-lookup"><span data-stu-id="6e24a-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="6e24a-241">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1` 或 `::ffff:a00:1`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="6e24a-242">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6e24a-243">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="6e24a-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="6e24a-244">在以下示例中，提供转接头的网络地址以 IPv6 格式添加到 `KnownNetworks` 列表中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="6e24a-245">IPv4 地址：`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="6e24a-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="6e24a-246">转换后的 IPv6 地址：`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="6e24a-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="6e24a-247">转换后的前缀长度：104</span><span class="sxs-lookup"><span data-stu-id="6e24a-247">Converted prefix length: 104</span></span>

<span data-ttu-id="6e24a-248">也可以提供十六进制格式的地址（`10.11.12.1` 以 IPv6 格式表示为 `::ffff:0a0b:0c01`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="6e24a-249">将 IPv4 地址转换为 IPv6 时，将 96 添加到 CIDR 前缀长度（示例中的 `8`）以说明其他 `::ffff:` IPv6 前缀 (8 + 96 = 104)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="6e24a-250">转发 Linux 和非 IIS 反向代理的方案</span><span class="sxs-lookup"><span data-stu-id="6e24a-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="6e24a-251">如果将站点部署到 Azure Linux 应用服务、Azure Linux 虚拟机 (VM)，或者部署到除 IIS 之外的任何其他反向代理之后，调用 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 和 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 的应用都会使站点进入无限循环。</span><span class="sxs-lookup"><span data-stu-id="6e24a-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="6e24a-252">反向代理终止 TLS，并且 Kestrel 未发现正确的请求方案。</span><span class="sxs-lookup"><span data-stu-id="6e24a-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="6e24a-253">由于 OAuth 和 OIDC 生成了错误的重定向，因此它们在此配置中也会出现故障。</span><span class="sxs-lookup"><span data-stu-id="6e24a-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="6e24a-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 在 IIS 之后运行时会添加和配置转接头中间件，但 Linux（Apache 或 Nginx 集成）没有匹配的自动配置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="6e24a-255">要从非 IIS 方案中的代理中转发方案，请添加并配置转接头中间件。</span><span class="sxs-lookup"><span data-stu-id="6e24a-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="6e24a-256">在 `Startup.ConfigureServices` 中，使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="6e24a-256">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="6e24a-257">转发证书</span><span class="sxs-lookup"><span data-stu-id="6e24a-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="6e24a-258">Azure</span><span class="sxs-lookup"><span data-stu-id="6e24a-258">Azure</span></span>

<span data-ttu-id="6e24a-259">若要为证书转发配置 Azure 应用服务，请参阅[为 Azure 应用服务配置 TLS 相互身份验证](/azure/app-service/app-service-web-configure-tls-mutual-auth)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="6e24a-260">以下指南与配置 ASP.NET Core 应用相关。</span><span class="sxs-lookup"><span data-stu-id="6e24a-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="6e24a-261">在 `Startup.Configure` 中，在调用 `app.UseAuthentication();` 前添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="6e24a-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="6e24a-262">配置证书转发中间件，以指定 Azure 使用的标头名称。</span><span class="sxs-lookup"><span data-stu-id="6e24a-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="6e24a-263">在 `Startup.ConfigureServices` 中，添加以下代码来配置中间件从中生成证书的标头：</span><span class="sxs-lookup"><span data-stu-id="6e24a-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="6e24a-264">其他 Web 代理</span><span class="sxs-lookup"><span data-stu-id="6e24a-264">Other web proxies</span></span>

<span data-ttu-id="6e24a-265">如果使用的代理不是 IIS 或 Azure 应用服务的应用程序请求路由 (ARR)，请配置代理，以便转发其在 HTTP 标头中收到的证书。</span><span class="sxs-lookup"><span data-stu-id="6e24a-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="6e24a-266">在 `Startup.Configure` 中，在调用 `app.UseAuthentication();` 前添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="6e24a-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="6e24a-267">配置证书转发中间件，以指定标头名称。</span><span class="sxs-lookup"><span data-stu-id="6e24a-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="6e24a-268">在 `Startup.ConfigureServices` 中，添加以下代码来配置中间件从中生成证书的标头：</span><span class="sxs-lookup"><span data-stu-id="6e24a-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="6e24a-269">如果代理不在对证书进行 base64 编码（与 Nginx 的情况一样），请设置 `HeaderConverter` 选项。</span><span class="sxs-lookup"><span data-stu-id="6e24a-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="6e24a-270">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="6e24a-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="6e24a-271">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6e24a-271">Troubleshoot</span></span>

<span data-ttu-id="6e24a-272">如果未按预期转接标头，请启用[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6e24a-273">如果日志没有提供足够的信息来解决问题，请枚举服务器收到的请求标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="6e24a-274">使用内联中间件将请求标头写入应用程序响应或记录标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="6e24a-275">要将标头写入应用的响应，请在 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 中调用 `Startup.Configure` 后立即放置以下终端内联中间件：</span><span class="sxs-lookup"><span data-stu-id="6e24a-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="6e24a-276">可以写入日志，而不是响应正文。</span><span class="sxs-lookup"><span data-stu-id="6e24a-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="6e24a-277">借助写入日志，站点可在调试时正常运行。</span><span class="sxs-lookup"><span data-stu-id="6e24a-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="6e24a-278">要写入日志而不是响应正文，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6e24a-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="6e24a-279">将 `ILogger<Startup>` 注入到 `Startup` 类中，如[在启动时创建日志](xref:fundamentals/logging/index#create-logs-in-startup)中所述。</span><span class="sxs-lookup"><span data-stu-id="6e24a-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="6e24a-280">在 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 中调用 `Startup.Configure` 之后，立即放置以下内联中间件。</span><span class="sxs-lookup"><span data-stu-id="6e24a-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="6e24a-281">处理时，`X-Forwarded-{For|Proto|Host}` 值将移至 `X-Original-{For|Proto|Host}`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="6e24a-282">如果给定标头中有多个值，则转接头中间件按照从右向左的相反顺序处理标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="6e24a-283">默认 `ForwardLimit` 为 `1`（一），因此只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="6e24a-284">在处理转接头之前，请求的原始远程 IP 必须与 `KnownProxies` 或 `KnownNetworks` 列表中的条目匹配。</span><span class="sxs-lookup"><span data-stu-id="6e24a-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="6e24a-285">这通过不接受来自不受信任的代理的转发器来限制标头欺骗。</span><span class="sxs-lookup"><span data-stu-id="6e24a-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="6e24a-286">检测到未知代理时，日志记录会指出代理的地址：</span><span class="sxs-lookup"><span data-stu-id="6e24a-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="6e24a-287">在上述示例中，10.0.0.100 是代理服务器。</span><span class="sxs-lookup"><span data-stu-id="6e24a-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="6e24a-288">如果该服务器是受信任的代理，请将服务器的 IP 地址添加到 `KnownProxies` 中的 `KnownNetworks`（或将受信任的网络添加到 `Startup.ConfigureServices`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e24a-289">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)部分。</span><span class="sxs-lookup"><span data-stu-id="6e24a-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="6e24a-290">仅允许受信任的代理和网络转接头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="6e24a-291">否则，可能会受到 [IP 欺骗](https://www.iplocation.net/ip-spoofing)攻击。</span><span class="sxs-lookup"><span data-stu-id="6e24a-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e24a-292">其他资源</span><span class="sxs-lookup"><span data-stu-id="6e24a-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="6e24a-293">Microsoft 安全公告 CVE-2018-0787：ASP.NET Core 特权提升漏洞</span><span class="sxs-lookup"><span data-stu-id="6e24a-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6e24a-294">在 ASP.NET Core 推荐配置中，应用使用 IIS/ASP.NET Core 模块、Nginx 或 Apache 进行托管。</span><span class="sxs-lookup"><span data-stu-id="6e24a-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="6e24a-295">代理服务器、负载均衡器和其他网络设备通常会在请求到达应用之前隐藏有关请求的信息：</span><span class="sxs-lookup"><span data-stu-id="6e24a-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="6e24a-296">当通过 HTTP 代理 HTTPS 请求时，原方案 (HTTPS) 将丢失，并且必须在标头中转接。</span><span class="sxs-lookup"><span data-stu-id="6e24a-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="6e24a-297">由于应用收到来自代理的请求，而不是 Internet 或公司网络上请求的真实源，因此原始客户端 IP 地址也必须在标头中转接。</span><span class="sxs-lookup"><span data-stu-id="6e24a-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="6e24a-298">此信息在请求处理中可能很重要，例如在重定向、身份验证、链接生成、策略评估和客户端地理位置中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="6e24a-299">转接头</span><span class="sxs-lookup"><span data-stu-id="6e24a-299">Forwarded headers</span></span>

<span data-ttu-id="6e24a-300">按照约定，代理转接 HTTP 标头中的信息。</span><span class="sxs-lookup"><span data-stu-id="6e24a-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="6e24a-301">Header</span><span class="sxs-lookup"><span data-stu-id="6e24a-301">Header</span></span> | <span data-ttu-id="6e24a-302">描述</span><span class="sxs-lookup"><span data-stu-id="6e24a-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="6e24a-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="6e24a-303">X-Forwarded-For</span></span> | <span data-ttu-id="6e24a-304">保存代理链中关于发起请求的客户端和后续代理的信息。</span><span class="sxs-lookup"><span data-stu-id="6e24a-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="6e24a-305">该参数可能包含 IP 地址（以及可选端口号）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="6e24a-306">在代理服务器链中，第一个参数表示最初发出请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="6e24a-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="6e24a-307">后续代理标识符以此类推。</span><span class="sxs-lookup"><span data-stu-id="6e24a-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="6e24a-308">链中的最后一个代理不在参数列表中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="6e24a-309">最后一个代理的 IP 地址以及可选的端口号可用作传输层的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="6e24a-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="6e24a-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="6e24a-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="6e24a-311">原方案的值 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="6e24a-312">如果请求已遍历多个代理，则该值也可以是方案列表。</span><span class="sxs-lookup"><span data-stu-id="6e24a-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="6e24a-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="6e24a-313">X-Forwarded-Host</span></span> | <span data-ttu-id="6e24a-314">主机标头字段的原始值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-314">The original value of the Host header field.</span></span> <span data-ttu-id="6e24a-315">代理通常不会修改主机标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="6e24a-316">有关特权提升漏洞的信息，请参阅 [Microsoft 安全公告 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)，该漏洞影响代理未验证或将主机标头限制为已知正确值的系统。</span><span class="sxs-lookup"><span data-stu-id="6e24a-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="6e24a-317">来自 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 包的转接头中间件读取这些标头，并填充 <xref:Microsoft.AspNetCore.Http.HttpContext> 上的关联字段。</span><span class="sxs-lookup"><span data-stu-id="6e24a-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="6e24a-318">中间件更新：</span><span class="sxs-lookup"><span data-stu-id="6e24a-318">The middleware updates:</span></span>

* <span data-ttu-id="6e24a-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; 使用 `X-Forwarded-For` 标头值进行设置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="6e24a-320">影响中间件如何设置 `RemoteIpAddress` 的其他设置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="6e24a-321">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="6e24a-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; 使用 `X-Forwarded-Proto` 标头值进行设置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="6e24a-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; 使用 `X-Forwarded-Host` 标头值进行设置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="6e24a-324">可以配置转接头中间件[默认设置](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="6e24a-325">默认设置为：</span><span class="sxs-lookup"><span data-stu-id="6e24a-325">The default settings are:</span></span>

* <span data-ttu-id="6e24a-326">应用和请求源之间只有一个代理  。</span><span class="sxs-lookup"><span data-stu-id="6e24a-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="6e24a-327">仅将环回地址配置为已知代理和已知网络。</span><span class="sxs-lookup"><span data-stu-id="6e24a-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="6e24a-328">转接头被命名为 `X-Forwarded-For` 和 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="6e24a-329">并非所有网络设备均可在没有其他配置的情况下添加 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="6e24a-330">如果代理请求在到达应用时未包含这些标头，请查阅设备制造商提供的指导。</span><span class="sxs-lookup"><span data-stu-id="6e24a-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="6e24a-331">如果设备使用 `X-Forwarded-For` 和 `X-Forwarded-Proto` 以外的其他标头名称，请设置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 和 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 选项，使其与设备所用的标头名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="6e24a-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="6e24a-332">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)和[配置使用不同标头名称的代理](#configuration-for-a-proxy-that-uses-different-header-names)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="6e24a-333">IIS/IIS Express 和 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="6e24a-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="6e24a-334">当应用托管在 IIS 和 ASP.NET Core 模块后方的[进程外](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)时，转接头中间件由 [IIS 集成中间件](xref:host-and-deploy/iis/index#out-of-process-hosting-model)默认启用。</span><span class="sxs-lookup"><span data-stu-id="6e24a-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="6e24a-335">由于转接头的信任问题（例如，[IP 欺骗](https://www.iplocation.net/ip-spoofing)），转接头中间件被激活为首先在中间件管道中运行，并具有特定于 ASP.NET Core 模块的受限配置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="6e24a-336">中间件配置为转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，并且被限制到单个本地 localhost 代理。</span><span class="sxs-lookup"><span data-stu-id="6e24a-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="6e24a-337">如果需要其他配置，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6e24a-338">其他代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="6e24a-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6e24a-339">除在[进程外](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)托管时使用 [IIS 集成](xref:host-and-deploy/iis/index#out-of-process-hosting-model)之外，不会默认启用转接头中间件。</span><span class="sxs-lookup"><span data-stu-id="6e24a-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="6e24a-340">必须启用应用的转接头中间件才能处理带有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的转接头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="6e24a-341">启用中间件后，如果没有为中间件指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，那么默认的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="6e24a-342">为中间件配置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 以转接 `X-Forwarded-For` 中的 `X-Forwarded-Proto` 和 `Startup.ConfigureServices` 标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e24a-343">调用其他中间件之前，先调用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 中的 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="6e24a-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="6e24a-344">如果没有在 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 中指定任何 `Startup.ConfigureServices`，或未使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 直接指定到扩展方法，则要转接的默认标头为 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="6e24a-345">必须为 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 属性配置要转接的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="6e24a-346">Nginx 配置</span><span class="sxs-lookup"><span data-stu-id="6e24a-346">Nginx configuration</span></span>

<span data-ttu-id="6e24a-347">要转发 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-nginx#configure-nginx>。</span><span class="sxs-lookup"><span data-stu-id="6e24a-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="6e24a-348">有关详细信息，请参阅 [NGINX：使用转接头](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="6e24a-349">Apache 配置</span><span class="sxs-lookup"><span data-stu-id="6e24a-349">Apache configuration</span></span>

<span data-ttu-id="6e24a-350">`X-Forwarded-For` 将会自动添加（请参阅 [Apache 模块 mod_proxy：反向代理请求标头](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="6e24a-351">要了解如何转发 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-apache#configure-apache>。</span><span class="sxs-lookup"><span data-stu-id="6e24a-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="6e24a-352">转接头中间件选项</span><span class="sxs-lookup"><span data-stu-id="6e24a-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="6e24a-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 控制转接头中间件的行为。</span><span class="sxs-lookup"><span data-stu-id="6e24a-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="6e24a-354">以下示例更改了默认值：</span><span class="sxs-lookup"><span data-stu-id="6e24a-354">The following example changes the default values:</span></span>

* <span data-ttu-id="6e24a-355">将转接头中的条目数量限制为 `2`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="6e24a-356">添加已知的代理地址 `127.0.10.1`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="6e24a-357">将转接头名称从默认的 `X-Forwarded-For` 更改为 `X-Forwarded-For-My-Custom-Header-Name`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="6e24a-358">选项</span><span class="sxs-lookup"><span data-stu-id="6e24a-358">Option</span></span> | <span data-ttu-id="6e24a-359">描述</span><span class="sxs-lookup"><span data-stu-id="6e24a-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="6e24a-360">通过 `X-Forwarded-Host` 标头将主机限制为提供的值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="6e24a-361">使用 ordinal-ignore-case 比较值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="6e24a-362">必须排除端口号。</span><span class="sxs-lookup"><span data-stu-id="6e24a-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="6e24a-363">如果列表为空，则允许使用所有主机。</span><span class="sxs-lookup"><span data-stu-id="6e24a-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="6e24a-364">顶级通配符 `*` 允许所有非空主机。</span><span class="sxs-lookup"><span data-stu-id="6e24a-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="6e24a-365">子域通配符允许使用，但不匹配根域。</span><span class="sxs-lookup"><span data-stu-id="6e24a-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="6e24a-366">例如，`*.contoso.com` 匹配子域 `foo.contoso.com`，但不匹配根域 `contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="6e24a-367">允许使用 Unicode 主机名，但应转换为 [Punycode](https://tools.ietf.org/html/rfc3492) 进行匹配。</span><span class="sxs-lookup"><span data-stu-id="6e24a-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="6e24a-368">[IPv6 地址](https://tools.ietf.org/html/rfc4291)必须包括边界方括号，而且必须位于[常规窗体](https://tools.ietf.org/html/rfc4291#section-2.2)（例如，`[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`）中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="6e24a-369">IPv6 地址并非专门用于检查不同格式之间的逻辑相等性，也不执行标准化。</span><span class="sxs-lookup"><span data-stu-id="6e24a-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="6e24a-370">未能限制允许的主机可能会允许攻击者访问该服务生成的欺骗链接。</span><span class="sxs-lookup"><span data-stu-id="6e24a-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="6e24a-371">默认值为空的 `IList<string>`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="6e24a-372">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="6e24a-373">如果代理/转发器不使用 `X-Forwarded-For` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="6e24a-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6e24a-374">默认值为 `X-Forwarded-For`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="6e24a-375">标识应处理的转发器。</span><span class="sxs-lookup"><span data-stu-id="6e24a-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="6e24a-376">对于应用的字段的列表，请参阅 [ForwardedHeaders 枚举](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="6e24a-377">分配给此属性的典型值为 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="6e24a-378">默认值是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="6e24a-379">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="6e24a-380">如果代理/转发器不使用 `X-Forwarded-Host` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="6e24a-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6e24a-381">默认值为 `X-Forwarded-Host`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="6e24a-382">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="6e24a-383">如果代理/转发器不使用 `X-Forwarded-Proto` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="6e24a-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6e24a-384">默认值为 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="6e24a-385">限制所处理的标头中的条目数。</span><span class="sxs-lookup"><span data-stu-id="6e24a-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="6e24a-386">设置为 `null` 以禁用此限制，但仅应在已配置 `KnownProxies` 或 `KnownNetworks` 的情况下执行此操作。</span><span class="sxs-lookup"><span data-stu-id="6e24a-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="6e24a-387">设置非 `null` 值是一种预防措施（但不是保证），防止错误配置的代理和恶意请求从网络的侧通道到达。</span><span class="sxs-lookup"><span data-stu-id="6e24a-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="6e24a-388">转接头中间件按照从右向左的相反顺序处理标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="6e24a-389">如果使用默认值 (`1`)，则只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="6e24a-390">默认值为 `1`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="6e24a-391">从中接受转接头的已知网络的地址范围。</span><span class="sxs-lookup"><span data-stu-id="6e24a-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="6e24a-392">使用无类别域际路由选择 (CIDR) 表示法提供 IP 范围。</span><span class="sxs-lookup"><span data-stu-id="6e24a-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="6e24a-393">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="6e24a-394">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6e24a-395">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="6e24a-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="6e24a-396">有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。</span><span class="sxs-lookup"><span data-stu-id="6e24a-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="6e24a-397">默认值是 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>，其中包含 `IPAddress.Loopback` 的单个条目。</span><span class="sxs-lookup"><span data-stu-id="6e24a-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="6e24a-398">从中接受转接头的已知代理的地址。</span><span class="sxs-lookup"><span data-stu-id="6e24a-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="6e24a-399">使用 `KnownProxies` 指定精确的 IP 地址匹配。</span><span class="sxs-lookup"><span data-stu-id="6e24a-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="6e24a-400">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="6e24a-401">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6e24a-402">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="6e24a-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="6e24a-403">有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。</span><span class="sxs-lookup"><span data-stu-id="6e24a-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="6e24a-404">默认值是 `IList`\<<xref:System.Net.IPAddress>>，其中包含 `IPAddress.IPv6Loopback` 的单个条目。</span><span class="sxs-lookup"><span data-stu-id="6e24a-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="6e24a-405">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="6e24a-406">默认值为 `X-Original-For`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="6e24a-407">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="6e24a-408">默认值为 `X-Original-Host`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="6e24a-409">使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="6e24a-410">默认值为 `X-Original-Proto`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="6e24a-411">要求正在处理的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 之间的标头值数量保持同步。</span><span class="sxs-lookup"><span data-stu-id="6e24a-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="6e24a-412">ASP.NET Core 1.x 默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="6e24a-413">ASP.NET Core 2.0 默认值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="6e24a-414">方案与使用案例</span><span class="sxs-lookup"><span data-stu-id="6e24a-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="6e24a-415">无法添加转接头并且所有请求都安全的情况</span><span class="sxs-lookup"><span data-stu-id="6e24a-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="6e24a-416">在某些情况下，可能无法将转接头添加到代理到应用的请求中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="6e24a-417">如果代理强制将所有公共外部请求执行为 HTTPS，则在使用任何类型的中间件之前，可以在 `Startup.Configure` 中手动设置该方案：</span><span class="sxs-lookup"><span data-stu-id="6e24a-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="6e24a-418">此代码可以通过环境变量或者开发环境或过渡环境中的其他配置设置来禁用。</span><span class="sxs-lookup"><span data-stu-id="6e24a-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="6e24a-419">处理改变请求路径的基路径和代理</span><span class="sxs-lookup"><span data-stu-id="6e24a-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="6e24a-420">一些代理可完整地传递路径，但是会删除应用基路径以便路由可正常工作。</span><span class="sxs-lookup"><span data-stu-id="6e24a-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="6e24a-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 中间件将路径拆分为 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)，将应用基路径拆分为 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="6e24a-422">如果 `/foo` 是作为 `/foo/api/1` 传递的代理路径的应用基路径，则中间件使用以下命令将 `Request.PathBase` 设置为 `/foo`，将 `Request.Path` 设置为 `/api/1`：</span><span class="sxs-lookup"><span data-stu-id="6e24a-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="6e24a-423">当再次反向调用中间件时，将重新应用原始路径和基路径。</span><span class="sxs-lookup"><span data-stu-id="6e24a-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="6e24a-424">要详细了解中间件处理顺序，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="6e24a-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="6e24a-425">如果代理剪裁路径（例如，将 `/foo/api/1` 转接到 `/api/1`），请通过设置请求的 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 属性来修复重定向和链接：</span><span class="sxs-lookup"><span data-stu-id="6e24a-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="6e24a-426">如果代理要添加路径数据，请使用 <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> 并分配给 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 属性，从而放弃部分路径以修复重定向和链接：</span><span class="sxs-lookup"><span data-stu-id="6e24a-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="6e24a-427">配置使用不同标头名称的代理</span><span class="sxs-lookup"><span data-stu-id="6e24a-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="6e24a-428">如果代理不使用名为 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的标头来转发代理地址/端口和原始架构信息，则设置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 和 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 选项，使其与代理所用的标头名称相匹配：</span><span class="sxs-lookup"><span data-stu-id="6e24a-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="6e24a-429">对表示为 IPv6 地址的 IPv4 地址进行配置</span><span class="sxs-lookup"><span data-stu-id="6e24a-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="6e24a-430">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1` 或 `::ffff:a00:1`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="6e24a-431">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6e24a-432">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="6e24a-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="6e24a-433">在以下示例中，提供转接头的网络地址以 IPv6 格式添加到 `KnownNetworks` 列表中。</span><span class="sxs-lookup"><span data-stu-id="6e24a-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="6e24a-434">IPv4 地址：`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="6e24a-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="6e24a-435">转换后的 IPv6 地址：`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="6e24a-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="6e24a-436">转换后的前缀长度：104</span><span class="sxs-lookup"><span data-stu-id="6e24a-436">Converted prefix length: 104</span></span>

<span data-ttu-id="6e24a-437">也可以提供十六进制格式的地址（`10.11.12.1` 以 IPv6 格式表示为 `::ffff:0a0b:0c01`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="6e24a-438">将 IPv4 地址转换为 IPv6 时，将 96 添加到 CIDR 前缀长度（示例中的 `8`）以说明其他 `::ffff:` IPv6 前缀 (8 + 96 = 104)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="6e24a-439">转发 Linux 和非 IIS 反向代理的方案</span><span class="sxs-lookup"><span data-stu-id="6e24a-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="6e24a-440">如果将站点部署到 Azure Linux 应用服务、Azure Linux 虚拟机 (VM)，或者部署到除 IIS 之外的任何其他反向代理之后，调用 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 和 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 的应用都会使站点进入无限循环。</span><span class="sxs-lookup"><span data-stu-id="6e24a-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="6e24a-441">反向代理终止 TLS，并且 Kestrel 未发现正确的请求方案。</span><span class="sxs-lookup"><span data-stu-id="6e24a-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="6e24a-442">由于 OAuth 和 OIDC 生成了错误的重定向，因此它们在此配置中也会出现故障。</span><span class="sxs-lookup"><span data-stu-id="6e24a-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="6e24a-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 在 IIS 之后运行时会添加和配置转接头中间件，但 Linux（Apache 或 Nginx 集成）没有匹配的自动配置。</span><span class="sxs-lookup"><span data-stu-id="6e24a-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="6e24a-444">要从非 IIS 方案中的代理中转发方案，请添加并配置转接头中间件。</span><span class="sxs-lookup"><span data-stu-id="6e24a-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="6e24a-445">在 `Startup.ConfigureServices` 中，使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="6e24a-445">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="6e24a-446">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6e24a-446">Troubleshoot</span></span>

<span data-ttu-id="6e24a-447">如果未按预期转接标头，请启用[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="6e24a-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6e24a-448">如果日志没有提供足够的信息来解决问题，请枚举服务器收到的请求标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="6e24a-449">使用内联中间件将请求标头写入应用程序响应或记录标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="6e24a-450">要将标头写入应用的响应，请在 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 中调用 `Startup.Configure` 后立即放置以下终端内联中间件：</span><span class="sxs-lookup"><span data-stu-id="6e24a-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="6e24a-451">可以写入日志，而不是响应正文。</span><span class="sxs-lookup"><span data-stu-id="6e24a-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="6e24a-452">借助写入日志，站点可在调试时正常运行。</span><span class="sxs-lookup"><span data-stu-id="6e24a-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="6e24a-453">要写入日志而不是响应正文，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6e24a-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="6e24a-454">将 `ILogger<Startup>` 注入到 `Startup` 类中，如[在启动时创建日志](xref:fundamentals/logging/index#create-logs-in-startup)中所述。</span><span class="sxs-lookup"><span data-stu-id="6e24a-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="6e24a-455">在 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 中调用 `Startup.Configure` 之后，立即放置以下内联中间件。</span><span class="sxs-lookup"><span data-stu-id="6e24a-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="6e24a-456">处理时，`X-Forwarded-{For|Proto|Host}` 值将移至 `X-Original-{For|Proto|Host}`。</span><span class="sxs-lookup"><span data-stu-id="6e24a-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="6e24a-457">如果给定标头中有多个值，则转接头中间件按照从右向左的相反顺序处理标头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="6e24a-458">默认 `ForwardLimit` 为 `1`（一），因此只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。</span><span class="sxs-lookup"><span data-stu-id="6e24a-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="6e24a-459">在处理转接头之前，请求的原始远程 IP 必须与 `KnownProxies` 或 `KnownNetworks` 列表中的条目匹配。</span><span class="sxs-lookup"><span data-stu-id="6e24a-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="6e24a-460">这通过不接受来自不受信任的代理的转发器来限制标头欺骗。</span><span class="sxs-lookup"><span data-stu-id="6e24a-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="6e24a-461">检测到未知代理时，日志记录会指出代理的地址：</span><span class="sxs-lookup"><span data-stu-id="6e24a-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="6e24a-462">在上述示例中，10.0.0.100 是代理服务器。</span><span class="sxs-lookup"><span data-stu-id="6e24a-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="6e24a-463">如果该服务器是受信任的代理，请将服务器的 IP 地址添加到 `KnownProxies` 中的 `KnownNetworks`（或将受信任的网络添加到 `Startup.ConfigureServices`）。</span><span class="sxs-lookup"><span data-stu-id="6e24a-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e24a-464">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)部分。</span><span class="sxs-lookup"><span data-stu-id="6e24a-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="6e24a-465">仅允许受信任的代理和网络转接头。</span><span class="sxs-lookup"><span data-stu-id="6e24a-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="6e24a-466">否则，可能会受到 [IP 欺骗](https://www.iplocation.net/ip-spoofing)攻击。</span><span class="sxs-lookup"><span data-stu-id="6e24a-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e24a-467">其他资源</span><span class="sxs-lookup"><span data-stu-id="6e24a-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="6e24a-468">Microsoft 安全公告 CVE-2018-0787：ASP.NET Core 特权提升漏洞</span><span class="sxs-lookup"><span data-stu-id="6e24a-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
