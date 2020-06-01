---
<span data-ttu-id="65627-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-102">'Blazor'</span></span>
- <span data-ttu-id="65627-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-103">'Identity'</span></span>
- <span data-ttu-id="65627-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-105">'Razor'</span></span>
- <span data-ttu-id="65627-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="65627-107">配置 ASP.NET Core 以使用代理服务器和负载均衡器</span><span class="sxs-lookup"><span data-stu-id="65627-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="65627-108">作者：[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="65627-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65627-109">在 ASP.NET Core 推荐配置中，应用使用 IIS/ASP.NET Core 模块、Nginx 或 Apache 进行托管。</span><span class="sxs-lookup"><span data-stu-id="65627-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="65627-110">代理服务器、负载均衡器和其他网络设备通常会在请求到达应用之前隐藏有关请求的信息：</span><span class="sxs-lookup"><span data-stu-id="65627-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="65627-111">当通过 HTTP 代理 HTTPS 请求时，原方案 (HTTPS) 将丢失，并且必须在标头中转接。</span><span class="sxs-lookup"><span data-stu-id="65627-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="65627-112">由于应用收到来自代理的请求，而不是 Internet 或公司网络上请求的真实源，因此原始客户端 IP 地址也必须在标头中转接。</span><span class="sxs-lookup"><span data-stu-id="65627-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="65627-113">此信息在请求处理中可能很重要，例如在重定向、身份验证、链接生成、策略评估和客户端地理位置中。</span><span class="sxs-lookup"><span data-stu-id="65627-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="65627-114">转接头</span><span class="sxs-lookup"><span data-stu-id="65627-114">Forwarded headers</span></span>

<span data-ttu-id="65627-115">按照约定，代理转接 HTTP 标头中的信息。</span><span class="sxs-lookup"><span data-stu-id="65627-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="65627-116">Header</span><span class="sxs-lookup"><span data-stu-id="65627-116">Header</span></span> | <span data-ttu-id="65627-117">描述</span><span class="sxs-lookup"><span data-stu-id="65627-117">Description</span></span> |
| ---
<span data-ttu-id="65627-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-119">'Blazor'</span></span>
- <span data-ttu-id="65627-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-120">'Identity'</span></span>
- <span data-ttu-id="65627-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-122">'Razor'</span></span>
- <span data-ttu-id="65627-123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-123">'SignalR' uid:</span></span> 

<span data-ttu-id="65627-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-125">'Blazor'</span></span>
- <span data-ttu-id="65627-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-126">'Identity'</span></span>
- <span data-ttu-id="65627-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-128">'Razor'</span></span>
- <span data-ttu-id="65627-129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="65627-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-131">'Blazor'</span></span>
- <span data-ttu-id="65627-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-132">'Identity'</span></span>
- <span data-ttu-id="65627-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-134">'Razor'</span></span>
- <span data-ttu-id="65627-135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="65627-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-137">'Blazor'</span></span>
- <span data-ttu-id="65627-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-138">'Identity'</span></span>
- <span data-ttu-id="65627-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-140">'Razor'</span></span>
- <span data-ttu-id="65627-141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-141">'SignalR' uid:</span></span> 

<span data-ttu-id="65627-142">------ | | X-Forwarded-For | 保存代理链中关于发起请求的客户端和后续代理的信息。</span><span class="sxs-lookup"><span data-stu-id="65627-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="65627-143">该参数可能包含 IP 地址（以及可选端口号）。</span><span class="sxs-lookup"><span data-stu-id="65627-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="65627-144">在代理服务器链中，第一个参数表示最初发出请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="65627-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="65627-145">后续代理标识符以此类推。</span><span class="sxs-lookup"><span data-stu-id="65627-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="65627-146">链中的最后一个代理不在参数列表中。</span><span class="sxs-lookup"><span data-stu-id="65627-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="65627-147">最后一个代理的 IP 地址以及可选的端口号可用作传输层的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="65627-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="65627-148">| | X-Forwarded-Proto | 原方案的值 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="65627-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="65627-149">如果请求已遍历多个代理，则该值也可以是方案列表。</span><span class="sxs-lookup"><span data-stu-id="65627-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="65627-150">| | X-Forwarded-Host | 主机标头字段的原始值。</span><span class="sxs-lookup"><span data-stu-id="65627-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="65627-151">代理通常不会修改主机标头。</span><span class="sxs-lookup"><span data-stu-id="65627-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="65627-152">有关特权提升漏洞的信息，请参阅 [Microsoft 安全公告 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)，该漏洞影响代理未验证或将主机标头限制为已知正确值的系统。</span><span class="sxs-lookup"><span data-stu-id="65627-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="65627-153">来自 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 包的转接头中间件读取这些标头，并填充 <xref:Microsoft.AspNetCore.Http.HttpContext> 上的关联字段。</span><span class="sxs-lookup"><span data-stu-id="65627-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="65627-154">中间件更新：</span><span class="sxs-lookup"><span data-stu-id="65627-154">The middleware updates:</span></span>

* <span data-ttu-id="65627-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress)：使用 `X-Forwarded-For` 标头值设置。</span><span class="sxs-lookup"><span data-stu-id="65627-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="65627-156">影响中间件如何设置 `RemoteIpAddress` 的其他设置。</span><span class="sxs-lookup"><span data-stu-id="65627-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="65627-157">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65627-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="65627-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme)：使用 `X-Forwarded-Proto` 标头值设置。</span><span class="sxs-lookup"><span data-stu-id="65627-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="65627-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host)：使用 `X-Forwarded-Host` 标头值设置。</span><span class="sxs-lookup"><span data-stu-id="65627-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="65627-160">可以配置转接头中间件[默认设置](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65627-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="65627-161">默认设置为：</span><span class="sxs-lookup"><span data-stu-id="65627-161">The default settings are:</span></span>

* <span data-ttu-id="65627-162">应用和请求源之间只有一个代理。</span><span class="sxs-lookup"><span data-stu-id="65627-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="65627-163">仅将环回地址配置为已知代理和已知网络。</span><span class="sxs-lookup"><span data-stu-id="65627-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="65627-164">转接头被命名为 `X-Forwarded-For` 和 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65627-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="65627-165">并非所有网络设备均可在没有其他配置的情况下添加 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。</span><span class="sxs-lookup"><span data-stu-id="65627-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="65627-166">如果代理请求在到达应用时未包含这些标头，请查阅设备制造商提供的指导。</span><span class="sxs-lookup"><span data-stu-id="65627-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="65627-167">如果设备使用 `X-Forwarded-For` 和 `X-Forwarded-Proto` 以外的其他标头名称，请设置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 和 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 选项，使其与设备所用的标头名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="65627-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="65627-168">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)和[配置使用不同标头名称的代理](#configuration-for-a-proxy-that-uses-different-header-names)。</span><span class="sxs-lookup"><span data-stu-id="65627-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="65627-169">IIS/IIS Express 和 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="65627-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="65627-170">当应用托管在 IIS 和 ASP.NET Core 模块后方的[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)时，转接头中间件由 [IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)默认启用。</span><span class="sxs-lookup"><span data-stu-id="65627-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="65627-171">由于转接头的信任问题（例如，[IP 欺骗](https://www.iplocation.net/ip-spoofing)），转接头中间件被激活为首先在中间件管道中运行，并具有特定于 ASP.NET Core 模块的受限配置。</span><span class="sxs-lookup"><span data-stu-id="65627-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="65627-172">中间件配置为转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，并且被限制到单个本地 localhost 代理。</span><span class="sxs-lookup"><span data-stu-id="65627-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="65627-173">如果需要其他配置，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65627-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65627-174">其他代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="65627-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65627-175">除在[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)托管时使用 [IIS 集成](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)之外，不会默认启用转接头中间件。</span><span class="sxs-lookup"><span data-stu-id="65627-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="65627-176">必须启用应用的转接头中间件才能处理带有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的转接头。</span><span class="sxs-lookup"><span data-stu-id="65627-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="65627-177">启用中间件后，如果没有为中间件指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，那么默认的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65627-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="65627-178">为中间件配置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 以转接 `Startup.ConfigureServices` 中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。</span><span class="sxs-lookup"><span data-stu-id="65627-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65627-179">调用其他中间件之前，先调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 方法：</span><span class="sxs-lookup"><span data-stu-id="65627-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="65627-180">如果没有在 `Startup.ConfigureServices` 中指定任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，或未使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 直接指定到扩展方法，则要转接的默认标头为 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65627-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="65627-181">必须为 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 属性配置要转接的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="65627-182">Nginx 配置</span><span class="sxs-lookup"><span data-stu-id="65627-182">Nginx configuration</span></span>

<span data-ttu-id="65627-183">要转发 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-nginx#configure-nginx>。</span><span class="sxs-lookup"><span data-stu-id="65627-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="65627-184">有关详细信息，请参阅 [NGINX：使用转发标头](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。</span><span class="sxs-lookup"><span data-stu-id="65627-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="65627-185">Apache 配置</span><span class="sxs-lookup"><span data-stu-id="65627-185">Apache configuration</span></span>

<span data-ttu-id="65627-186">`X-Forwarded-For` 将会自动添加（请参阅 [Apache 模块 mod_proxy：反向代理请求标头](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)）。</span><span class="sxs-lookup"><span data-stu-id="65627-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="65627-187">要了解如何转发 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-apache#configure-apache>。</span><span class="sxs-lookup"><span data-stu-id="65627-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="65627-188">转接头中间件选项</span><span class="sxs-lookup"><span data-stu-id="65627-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="65627-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 控制转接头中间件的行为。</span><span class="sxs-lookup"><span data-stu-id="65627-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="65627-190">以下示例更改了默认值：</span><span class="sxs-lookup"><span data-stu-id="65627-190">The following example changes the default values:</span></span>

* <span data-ttu-id="65627-191">将转接头中的条目数量限制为 `2`。</span><span class="sxs-lookup"><span data-stu-id="65627-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="65627-192">添加已知的代理地址 `127.0.10.1`。</span><span class="sxs-lookup"><span data-stu-id="65627-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="65627-193">将转接头名称从默认的 `X-Forwarded-For` 更改为 `X-Forwarded-For-My-Custom-Header-Name`。</span><span class="sxs-lookup"><span data-stu-id="65627-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="65627-194">选项</span><span class="sxs-lookup"><span data-stu-id="65627-194">Option</span></span> | <span data-ttu-id="65627-195">描述</span><span class="sxs-lookup"><span data-stu-id="65627-195">Description</span></span> |
| ---
<span data-ttu-id="65627-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-197">'Blazor'</span></span>
- <span data-ttu-id="65627-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-198">'Identity'</span></span>
- <span data-ttu-id="65627-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-200">'Razor'</span></span>
- <span data-ttu-id="65627-201">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-201">'SignalR' uid:</span></span> 

<span data-ttu-id="65627-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-203">'Blazor'</span></span>
- <span data-ttu-id="65627-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-204">'Identity'</span></span>
- <span data-ttu-id="65627-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-206">'Razor'</span></span>
- <span data-ttu-id="65627-207">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="65627-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-209">'Blazor'</span></span>
- <span data-ttu-id="65627-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-210">'Identity'</span></span>
- <span data-ttu-id="65627-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-212">'Razor'</span></span>
- <span data-ttu-id="65627-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="65627-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-215">'Blazor'</span></span>
- <span data-ttu-id="65627-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-216">'Identity'</span></span>
- <span data-ttu-id="65627-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-218">'Razor'</span></span>
- <span data-ttu-id="65627-219">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-219">'SignalR' uid:</span></span> 

<span data-ttu-id="65627-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | 通过 `X-Forwarded-Host` 标头将主机限制为提供的值。</span><span class="sxs-lookup"><span data-stu-id="65627-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="65627-221">使用 ordinal-ignore-case 比较值。</span><span class="sxs-lookup"><span data-stu-id="65627-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="65627-222">必须排除端口号。</span><span class="sxs-lookup"><span data-stu-id="65627-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="65627-223">如果列表为空，则允许使用所有主机。</span><span class="sxs-lookup"><span data-stu-id="65627-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="65627-224">顶级通配符 `*` 允许所有非空主机。</span><span class="sxs-lookup"><span data-stu-id="65627-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="65627-225">子域通配符允许使用，但不匹配根域。</span><span class="sxs-lookup"><span data-stu-id="65627-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="65627-226">例如，`*.contoso.com` 匹配子域 `foo.contoso.com`，但不匹配根域 `contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="65627-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="65627-227">允许使用 Unicode 主机名，但应转换为 [Punycode](https://tools.ietf.org/html/rfc3492) 进行匹配。</span><span class="sxs-lookup"><span data-stu-id="65627-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="65627-228">[IPv6 地址](https://tools.ietf.org/html/rfc4291)必须包括边界方括号，而且必须位于[常规窗体](https://tools.ietf.org/html/rfc4291#section-2.2)（例如，`[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`）中。</span><span class="sxs-lookup"><span data-stu-id="65627-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="65627-229">IPv6 地址并非专门用于检查不同格式之间的逻辑相等性，也不执行标准化。</span><span class="sxs-lookup"><span data-stu-id="65627-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="65627-230">未能限制允许的主机可能会允许攻击者访问该服务生成的欺骗链接。</span><span class="sxs-lookup"><span data-stu-id="65627-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="65627-231">默认值为空的 `IList<string>`。</span><span class="sxs-lookup"><span data-stu-id="65627-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="65627-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="65627-233">如果代理/转发器不使用 `X-Forwarded-For` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="65627-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65627-234">默认值为 `X-Forwarded-For`。</span><span class="sxs-lookup"><span data-stu-id="65627-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="65627-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | 标识应处理的转发器。</span><span class="sxs-lookup"><span data-stu-id="65627-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="65627-236">对于应用的字段的列表，请参阅 [ForwardedHeaders 枚举](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65627-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="65627-237">分配给此属性的典型值为 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。</span><span class="sxs-lookup"><span data-stu-id="65627-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="65627-238">默认值是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65627-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="65627-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="65627-240">如果代理/转发器不使用 `X-Forwarded-Host` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="65627-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65627-241">默认值为 `X-Forwarded-Host`。</span><span class="sxs-lookup"><span data-stu-id="65627-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="65627-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="65627-243">如果代理/转发器不使用 `X-Forwarded-Proto` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="65627-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65627-244">默认值为 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65627-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="65627-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | 限制所处理的标头中的条目数。</span><span class="sxs-lookup"><span data-stu-id="65627-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="65627-246">设置为 `null` 以禁用此限制，但仅应在已配置 `KnownProxies` 或 `KnownNetworks` 的情况下执行此操作。</span><span class="sxs-lookup"><span data-stu-id="65627-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="65627-247">设置非 `null` 值是一种预防措施（但不是保证），防止错误配置的代理和恶意请求从网络的侧通道到达。</span><span class="sxs-lookup"><span data-stu-id="65627-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="65627-248">转接头中间件按照从右向左的相反顺序处理标头。</span><span class="sxs-lookup"><span data-stu-id="65627-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="65627-249">如果使用默认值 (`1`)，则只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。</span><span class="sxs-lookup"><span data-stu-id="65627-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="65627-250">默认值为 `1`。</span><span class="sxs-lookup"><span data-stu-id="65627-250">The default is `1`.</span></span> <span data-ttu-id="65627-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | 从中接受转接头的已知网络的地址范围。</span><span class="sxs-lookup"><span data-stu-id="65627-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="65627-252">使用无类别域际路由选择 (CIDR) 表示法提供 IP 范围。</span><span class="sxs-lookup"><span data-stu-id="65627-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="65627-253">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。</span><span class="sxs-lookup"><span data-stu-id="65627-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="65627-254">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65627-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65627-255">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="65627-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="65627-256">有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。</span><span class="sxs-lookup"><span data-stu-id="65627-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="65627-257">默认值是 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>，其中包含 `IPAddress.Loopback` 的单个条目。</span><span class="sxs-lookup"><span data-stu-id="65627-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="65627-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | 从中接受转接头的已知代理的地址。</span><span class="sxs-lookup"><span data-stu-id="65627-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="65627-259">使用 `KnownProxies` 指定精确的 IP 地址匹配。</span><span class="sxs-lookup"><span data-stu-id="65627-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="65627-260">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。</span><span class="sxs-lookup"><span data-stu-id="65627-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="65627-261">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65627-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65627-262">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="65627-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="65627-263">有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。</span><span class="sxs-lookup"><span data-stu-id="65627-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="65627-264">默认值是 `IList`\<<xref:System.Net.IPAddress>>，其中包含 `IPAddress.IPv6Loopback` 的单个条目。</span><span class="sxs-lookup"><span data-stu-id="65627-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="65627-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="65627-266">默认值为 `X-Original-For`。</span><span class="sxs-lookup"><span data-stu-id="65627-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="65627-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="65627-268">默认值为 `X-Original-Host`。</span><span class="sxs-lookup"><span data-stu-id="65627-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="65627-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="65627-270">默认值为 `X-Original-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65627-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="65627-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | 要求正在处理的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 之间的标头值数量保持同步。</span><span class="sxs-lookup"><span data-stu-id="65627-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="65627-272">ASP.NET Core 1.x 默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="65627-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="65627-273">ASP.NET Core 2.0 默认值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="65627-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="65627-274">方案与使用案例</span><span class="sxs-lookup"><span data-stu-id="65627-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="65627-275">无法添加转接头并且所有请求都安全的情况</span><span class="sxs-lookup"><span data-stu-id="65627-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="65627-276">在某些情况下，可能无法将转接头添加到代理到应用的请求中。</span><span class="sxs-lookup"><span data-stu-id="65627-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="65627-277">如果代理强制将所有公共外部请求执行为 HTTPS，则在使用任何类型的中间件之前，可以在 `Startup.Configure` 中手动设置该方案：</span><span class="sxs-lookup"><span data-stu-id="65627-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="65627-278">此代码可以通过环境变量或者开发环境或过渡环境中的其他配置设置来禁用。</span><span class="sxs-lookup"><span data-stu-id="65627-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="65627-279">处理改变请求路径的基路径和代理</span><span class="sxs-lookup"><span data-stu-id="65627-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="65627-280">一些代理可完整地传递路径，但是会删除应用基路径以便路由可正常工作。</span><span class="sxs-lookup"><span data-stu-id="65627-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="65627-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 中间件将路径拆分为 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)，将应用基路径拆分为 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)。</span><span class="sxs-lookup"><span data-stu-id="65627-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="65627-282">如果 `/foo` 是作为 `/foo/api/1` 传递的代理路径的应用基路径，则中间件使用以下命令将 `Request.PathBase` 设置为 `/foo`，将 `Request.Path` 设置为 `/api/1`：</span><span class="sxs-lookup"><span data-stu-id="65627-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="65627-283">当再次反向调用中间件时，将重新应用原始路径和基路径。</span><span class="sxs-lookup"><span data-stu-id="65627-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="65627-284">要详细了解中间件处理顺序，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="65627-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="65627-285">如果代理剪裁路径（例如，将 `/foo/api/1` 转接到 `/api/1`），请通过设置请求的 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 属性来修复重定向和链接：</span><span class="sxs-lookup"><span data-stu-id="65627-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="65627-286">如果代理要添加路径数据，请使用 <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> 并分配给 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 属性，从而放弃部分路径以修复重定向和链接：</span><span class="sxs-lookup"><span data-stu-id="65627-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="65627-287">配置使用不同标头名称的代理</span><span class="sxs-lookup"><span data-stu-id="65627-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="65627-288">如果代理不使用名为 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的标头来转发代理地址/端口和原始架构信息，则设置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 和 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 选项，使其与代理所用的标头名称相匹配：</span><span class="sxs-lookup"><span data-stu-id="65627-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="65627-289">对表示为 IPv6 地址的 IPv4 地址进行配置</span><span class="sxs-lookup"><span data-stu-id="65627-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="65627-290">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1` 或 `::ffff:a00:1`）。</span><span class="sxs-lookup"><span data-stu-id="65627-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="65627-291">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65627-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65627-292">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="65627-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="65627-293">在以下示例中，提供转接头的网络地址以 IPv6 格式添加到 `KnownNetworks` 列表中。</span><span class="sxs-lookup"><span data-stu-id="65627-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="65627-294">IPv4 地址：`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="65627-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="65627-295">转换后的 IPv6 地址：`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="65627-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="65627-296">转换后的前缀长度：104</span><span class="sxs-lookup"><span data-stu-id="65627-296">Converted prefix length: 104</span></span>

<span data-ttu-id="65627-297">也可以提供十六进制格式的地址（`10.11.12.1` 以 IPv6 格式表示为 `::ffff:0a0b:0c01`）。</span><span class="sxs-lookup"><span data-stu-id="65627-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="65627-298">将 IPv4 地址转换为 IPv6 时，将 96 添加到 CIDR 前缀长度（示例中的 `8`）以说明其他 `::ffff:` IPv6 前缀 (8 + 96 = 104)。</span><span class="sxs-lookup"><span data-stu-id="65627-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="65627-299">转发 Linux 和非 IIS 反向代理的方案</span><span class="sxs-lookup"><span data-stu-id="65627-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="65627-300">如果将站点部署到 Azure Linux 应用服务、Azure Linux 虚拟机 (VM)，或者部署到除 IIS 之外的任何其他反向代理之后，调用 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 和 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 的应用都会使站点进入无限循环。</span><span class="sxs-lookup"><span data-stu-id="65627-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="65627-301">反向代理终止 TLS，并且 Kestrel 未发现正确的请求方案。</span><span class="sxs-lookup"><span data-stu-id="65627-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="65627-302">由于 OAuth 和 OIDC 生成了错误的重定向，因此它们在此配置中也会出现故障。</span><span class="sxs-lookup"><span data-stu-id="65627-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="65627-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 在 IIS 之后运行时会添加和配置转接头中间件，但 Linux（Apache 或 Nginx 集成）没有匹配的自动配置。</span><span class="sxs-lookup"><span data-stu-id="65627-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="65627-304">要从非 IIS 方案中的代理中转发方案，请添加并配置转接头中间件。</span><span class="sxs-lookup"><span data-stu-id="65627-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="65627-305">在 `Startup.ConfigureServices` 中，使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="65627-305">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="65627-306">转发证书</span><span class="sxs-lookup"><span data-stu-id="65627-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="65627-307">Azure</span><span class="sxs-lookup"><span data-stu-id="65627-307">Azure</span></span>

<span data-ttu-id="65627-308">若要为证书转发配置 Azure 应用服务，请参阅[为 Azure 应用服务配置 TLS 相互身份验证](/azure/app-service/app-service-web-configure-tls-mutual-auth)。</span><span class="sxs-lookup"><span data-stu-id="65627-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="65627-309">以下指南与配置 ASP.NET Core 应用相关。</span><span class="sxs-lookup"><span data-stu-id="65627-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="65627-310">在 `Startup.Configure` 中，在调用 `app.UseAuthentication();` 前添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="65627-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="65627-311">配置证书转发中间件，以指定 Azure 使用的标头名称。</span><span class="sxs-lookup"><span data-stu-id="65627-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="65627-312">在 `Startup.ConfigureServices` 中，添加以下代码来配置中间件从中生成证书的标头：</span><span class="sxs-lookup"><span data-stu-id="65627-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="65627-313">其他 Web 代理</span><span class="sxs-lookup"><span data-stu-id="65627-313">Other web proxies</span></span>

<span data-ttu-id="65627-314">如果使用的代理不是 IIS 或 Azure 应用服务的应用程序请求路由 (ARR)，请配置代理，以便转发其在 HTTP 标头中收到的证书。</span><span class="sxs-lookup"><span data-stu-id="65627-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="65627-315">在 `Startup.Configure` 中，在调用 `app.UseAuthentication();` 前添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="65627-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="65627-316">配置证书转发中间件，以指定标头名称。</span><span class="sxs-lookup"><span data-stu-id="65627-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="65627-317">在 `Startup.ConfigureServices` 中，添加以下代码来配置中间件从中生成证书的标头：</span><span class="sxs-lookup"><span data-stu-id="65627-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="65627-318">如果代理不在对证书进行 base64 编码（与 Nginx 的情况一样），请设置 `HeaderConverter` 选项。</span><span class="sxs-lookup"><span data-stu-id="65627-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="65627-319">请看下面 `Startup.ConfigureServices` 中的示例：</span><span class="sxs-lookup"><span data-stu-id="65627-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="65627-320">疑难解答</span><span class="sxs-lookup"><span data-stu-id="65627-320">Troubleshoot</span></span>

<span data-ttu-id="65627-321">如果未按预期转接标头，请启用[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="65627-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="65627-322">如果日志没有提供足够的信息来解决问题，请枚举服务器收到的请求标头。</span><span class="sxs-lookup"><span data-stu-id="65627-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="65627-323">使用内联中间件将请求标头写入应用程序响应或记录标头。</span><span class="sxs-lookup"><span data-stu-id="65627-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="65627-324">要将标头写入应用的响应，请在 `Startup.Configure` 中调用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 后立即放置以下终端内联中间件：</span><span class="sxs-lookup"><span data-stu-id="65627-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="65627-325">可以写入日志，而不是响应正文。</span><span class="sxs-lookup"><span data-stu-id="65627-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="65627-326">借助写入日志，站点可在调试时正常运行。</span><span class="sxs-lookup"><span data-stu-id="65627-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="65627-327">要写入日志而不是响应正文，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="65627-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="65627-328">将 `ILogger<Startup>` 注入到 `Startup` 类中，如[在启动时创建日志](xref:fundamentals/logging/index#create-logs-in-startup)中所述。</span><span class="sxs-lookup"><span data-stu-id="65627-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="65627-329">在 `Startup.Configure` 中调用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 之后，立即放置以下内联中间件。</span><span class="sxs-lookup"><span data-stu-id="65627-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="65627-330">处理时，`X-Forwarded-{For|Proto|Host}` 值将移至 `X-Original-{For|Proto|Host}`。</span><span class="sxs-lookup"><span data-stu-id="65627-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="65627-331">如果给定标头中有多个值，则转接头中间件按照从右向左的相反顺序处理标头。</span><span class="sxs-lookup"><span data-stu-id="65627-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="65627-332">默认 `ForwardLimit` 为 `1`（一），因此只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。</span><span class="sxs-lookup"><span data-stu-id="65627-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="65627-333">在处理转接头之前，请求的原始远程 IP 必须与 `KnownProxies` 或 `KnownNetworks` 列表中的条目匹配。</span><span class="sxs-lookup"><span data-stu-id="65627-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="65627-334">这通过不接受来自不受信任的代理的转发器来限制标头欺骗。</span><span class="sxs-lookup"><span data-stu-id="65627-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="65627-335">检测到未知代理时，日志记录会指出代理的地址：</span><span class="sxs-lookup"><span data-stu-id="65627-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="65627-336">在上述示例中，10.0.0.100 是代理服务器。</span><span class="sxs-lookup"><span data-stu-id="65627-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="65627-337">如果该服务器是受信任的代理，请将服务器的 IP 地址添加到 `Startup.ConfigureServices` 中的 `KnownProxies`（或将受信任的网络添加到 `KnownNetworks`）。</span><span class="sxs-lookup"><span data-stu-id="65627-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65627-338">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)部分。</span><span class="sxs-lookup"><span data-stu-id="65627-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="65627-339">仅允许受信任的代理和网络转接头。</span><span class="sxs-lookup"><span data-stu-id="65627-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="65627-340">否则，可能会受到 [IP 欺骗](https://www.iplocation.net/ip-spoofing)攻击。</span><span class="sxs-lookup"><span data-stu-id="65627-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65627-341">其他资源</span><span class="sxs-lookup"><span data-stu-id="65627-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="65627-342">Microsoft 安全公告 CVE-2018-0787：ASP.NET Core 特权提升漏洞</span><span class="sxs-lookup"><span data-stu-id="65627-342">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="65627-343">在 ASP.NET Core 推荐配置中，应用使用 IIS/ASP.NET Core 模块、Nginx 或 Apache 进行托管。</span><span class="sxs-lookup"><span data-stu-id="65627-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="65627-344">代理服务器、负载均衡器和其他网络设备通常会在请求到达应用之前隐藏有关请求的信息：</span><span class="sxs-lookup"><span data-stu-id="65627-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="65627-345">当通过 HTTP 代理 HTTPS 请求时，原方案 (HTTPS) 将丢失，并且必须在标头中转接。</span><span class="sxs-lookup"><span data-stu-id="65627-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="65627-346">由于应用收到来自代理的请求，而不是 Internet 或公司网络上请求的真实源，因此原始客户端 IP 地址也必须在标头中转接。</span><span class="sxs-lookup"><span data-stu-id="65627-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="65627-347">此信息在请求处理中可能很重要，例如在重定向、身份验证、链接生成、策略评估和客户端地理位置中。</span><span class="sxs-lookup"><span data-stu-id="65627-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="65627-348">转接头</span><span class="sxs-lookup"><span data-stu-id="65627-348">Forwarded headers</span></span>

<span data-ttu-id="65627-349">按照约定，代理转接 HTTP 标头中的信息。</span><span class="sxs-lookup"><span data-stu-id="65627-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="65627-350">Header</span><span class="sxs-lookup"><span data-stu-id="65627-350">Header</span></span> | <span data-ttu-id="65627-351">描述</span><span class="sxs-lookup"><span data-stu-id="65627-351">Description</span></span> |
| ---
<span data-ttu-id="65627-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-353">'Blazor'</span></span>
- <span data-ttu-id="65627-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-354">'Identity'</span></span>
- <span data-ttu-id="65627-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-356">'Razor'</span></span>
- <span data-ttu-id="65627-357">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-357">'SignalR' uid:</span></span> 

<span data-ttu-id="65627-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-359">'Blazor'</span></span>
- <span data-ttu-id="65627-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-360">'Identity'</span></span>
- <span data-ttu-id="65627-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-362">'Razor'</span></span>
- <span data-ttu-id="65627-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="65627-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-365">'Blazor'</span></span>
- <span data-ttu-id="65627-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-366">'Identity'</span></span>
- <span data-ttu-id="65627-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-368">'Razor'</span></span>
- <span data-ttu-id="65627-369">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="65627-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-371">'Blazor'</span></span>
- <span data-ttu-id="65627-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-372">'Identity'</span></span>
- <span data-ttu-id="65627-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-374">'Razor'</span></span>
- <span data-ttu-id="65627-375">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-375">'SignalR' uid:</span></span> 

<span data-ttu-id="65627-376">------ | | X-Forwarded-For | 保存代理链中关于发起请求的客户端和后续代理的信息。</span><span class="sxs-lookup"><span data-stu-id="65627-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="65627-377">该参数可能包含 IP 地址（以及可选端口号）。</span><span class="sxs-lookup"><span data-stu-id="65627-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="65627-378">在代理服务器链中，第一个参数表示最初发出请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="65627-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="65627-379">后续代理标识符以此类推。</span><span class="sxs-lookup"><span data-stu-id="65627-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="65627-380">链中的最后一个代理不在参数列表中。</span><span class="sxs-lookup"><span data-stu-id="65627-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="65627-381">最后一个代理的 IP 地址以及可选的端口号可用作传输层的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="65627-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="65627-382">| | X-Forwarded-Proto | 原方案的值 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="65627-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="65627-383">如果请求已遍历多个代理，则该值也可以是方案列表。</span><span class="sxs-lookup"><span data-stu-id="65627-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="65627-384">| | X-Forwarded-Host | 主机标头字段的原始值。</span><span class="sxs-lookup"><span data-stu-id="65627-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="65627-385">代理通常不会修改主机标头。</span><span class="sxs-lookup"><span data-stu-id="65627-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="65627-386">有关特权提升漏洞的信息，请参阅 [Microsoft 安全公告 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)，该漏洞影响代理未验证或将主机标头限制为已知正确值的系统。</span><span class="sxs-lookup"><span data-stu-id="65627-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="65627-387">来自 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 包的转接头中间件读取这些标头，并填充 <xref:Microsoft.AspNetCore.Http.HttpContext> 上的关联字段。</span><span class="sxs-lookup"><span data-stu-id="65627-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="65627-388">中间件更新：</span><span class="sxs-lookup"><span data-stu-id="65627-388">The middleware updates:</span></span>

* <span data-ttu-id="65627-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress)：使用 `X-Forwarded-For` 标头值设置。</span><span class="sxs-lookup"><span data-stu-id="65627-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="65627-390">影响中间件如何设置 `RemoteIpAddress` 的其他设置。</span><span class="sxs-lookup"><span data-stu-id="65627-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="65627-391">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65627-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="65627-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme)：使用 `X-Forwarded-Proto` 标头值设置。</span><span class="sxs-lookup"><span data-stu-id="65627-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="65627-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host)：使用 `X-Forwarded-Host` 标头值设置。</span><span class="sxs-lookup"><span data-stu-id="65627-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="65627-394">可以配置转接头中间件[默认设置](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65627-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="65627-395">默认设置为：</span><span class="sxs-lookup"><span data-stu-id="65627-395">The default settings are:</span></span>

* <span data-ttu-id="65627-396">应用和请求源之间只有一个代理。</span><span class="sxs-lookup"><span data-stu-id="65627-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="65627-397">仅将环回地址配置为已知代理和已知网络。</span><span class="sxs-lookup"><span data-stu-id="65627-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="65627-398">转接头被命名为 `X-Forwarded-For` 和 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65627-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="65627-399">并非所有网络设备均可在没有其他配置的情况下添加 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。</span><span class="sxs-lookup"><span data-stu-id="65627-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="65627-400">如果代理请求在到达应用时未包含这些标头，请查阅设备制造商提供的指导。</span><span class="sxs-lookup"><span data-stu-id="65627-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="65627-401">如果设备使用 `X-Forwarded-For` 和 `X-Forwarded-Proto` 以外的其他标头名称，请设置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 和 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 选项，使其与设备所用的标头名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="65627-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="65627-402">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)和[配置使用不同标头名称的代理](#configuration-for-a-proxy-that-uses-different-header-names)。</span><span class="sxs-lookup"><span data-stu-id="65627-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="65627-403">IIS/IIS Express 和 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="65627-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="65627-404">当应用托管在 IIS 和 ASP.NET Core 模块后方的[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)时，转接头中间件由 [IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)默认启用。</span><span class="sxs-lookup"><span data-stu-id="65627-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="65627-405">由于转接头的信任问题（例如，[IP 欺骗](https://www.iplocation.net/ip-spoofing)），转接头中间件被激活为首先在中间件管道中运行，并具有特定于 ASP.NET Core 模块的受限配置。</span><span class="sxs-lookup"><span data-stu-id="65627-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="65627-406">中间件配置为转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，并且被限制到单个本地 localhost 代理。</span><span class="sxs-lookup"><span data-stu-id="65627-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="65627-407">如果需要其他配置，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="65627-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="65627-408">其他代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="65627-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="65627-409">除在[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)托管时使用 [IIS 集成](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)之外，不会默认启用转接头中间件。</span><span class="sxs-lookup"><span data-stu-id="65627-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="65627-410">必须启用应用的转接头中间件才能处理带有 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 的转接头。</span><span class="sxs-lookup"><span data-stu-id="65627-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="65627-411">启用中间件后，如果没有为中间件指定 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，那么默认的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65627-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="65627-412">为中间件配置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 以转接 `Startup.ConfigureServices` 中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。</span><span class="sxs-lookup"><span data-stu-id="65627-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65627-413">调用其他中间件之前，先调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 方法：</span><span class="sxs-lookup"><span data-stu-id="65627-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="65627-414">如果没有在 `Startup.ConfigureServices` 中指定任何 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>，或未使用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 直接指定到扩展方法，则要转接的默认标头为 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65627-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="65627-415">必须为 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 属性配置要转接的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="65627-416">Nginx 配置</span><span class="sxs-lookup"><span data-stu-id="65627-416">Nginx configuration</span></span>

<span data-ttu-id="65627-417">要转发 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-nginx#configure-nginx>。</span><span class="sxs-lookup"><span data-stu-id="65627-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="65627-418">有关详细信息，请参阅 [NGINX：使用转发标头](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。</span><span class="sxs-lookup"><span data-stu-id="65627-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="65627-419">Apache 配置</span><span class="sxs-lookup"><span data-stu-id="65627-419">Apache configuration</span></span>

<span data-ttu-id="65627-420">`X-Forwarded-For` 将会自动添加（请参阅 [Apache 模块 mod_proxy：反向代理请求标头](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)）。</span><span class="sxs-lookup"><span data-stu-id="65627-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="65627-421">要了解如何转发 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-apache#configure-apache>。</span><span class="sxs-lookup"><span data-stu-id="65627-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="65627-422">转接头中间件选项</span><span class="sxs-lookup"><span data-stu-id="65627-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="65627-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> 控制转接头中间件的行为。</span><span class="sxs-lookup"><span data-stu-id="65627-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="65627-424">以下示例更改了默认值：</span><span class="sxs-lookup"><span data-stu-id="65627-424">The following example changes the default values:</span></span>

* <span data-ttu-id="65627-425">将转接头中的条目数量限制为 `2`。</span><span class="sxs-lookup"><span data-stu-id="65627-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="65627-426">添加已知的代理地址 `127.0.10.1`。</span><span class="sxs-lookup"><span data-stu-id="65627-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="65627-427">将转接头名称从默认的 `X-Forwarded-For` 更改为 `X-Forwarded-For-My-Custom-Header-Name`。</span><span class="sxs-lookup"><span data-stu-id="65627-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="65627-428">选项</span><span class="sxs-lookup"><span data-stu-id="65627-428">Option</span></span> | <span data-ttu-id="65627-429">描述</span><span class="sxs-lookup"><span data-stu-id="65627-429">Description</span></span> |
| ---
<span data-ttu-id="65627-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-431">'Blazor'</span></span>
- <span data-ttu-id="65627-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-432">'Identity'</span></span>
- <span data-ttu-id="65627-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-434">'Razor'</span></span>
- <span data-ttu-id="65627-435">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-435">'SignalR' uid:</span></span> 

<span data-ttu-id="65627-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-437">'Blazor'</span></span>
- <span data-ttu-id="65627-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-438">'Identity'</span></span>
- <span data-ttu-id="65627-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-440">'Razor'</span></span>
- <span data-ttu-id="65627-441">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="65627-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-443">'Blazor'</span></span>
- <span data-ttu-id="65627-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-444">'Identity'</span></span>
- <span data-ttu-id="65627-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-446">'Razor'</span></span>
- <span data-ttu-id="65627-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="65627-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="65627-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="65627-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="65627-449">'Blazor'</span></span>
- <span data-ttu-id="65627-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="65627-450">'Identity'</span></span>
- <span data-ttu-id="65627-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="65627-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="65627-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="65627-452">'Razor'</span></span>
- <span data-ttu-id="65627-453">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="65627-453">'SignalR' uid:</span></span> 

<span data-ttu-id="65627-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | 通过 `X-Forwarded-Host` 标头将主机限制为提供的值。</span><span class="sxs-lookup"><span data-stu-id="65627-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="65627-455">使用 ordinal-ignore-case 比较值。</span><span class="sxs-lookup"><span data-stu-id="65627-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="65627-456">必须排除端口号。</span><span class="sxs-lookup"><span data-stu-id="65627-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="65627-457">如果列表为空，则允许使用所有主机。</span><span class="sxs-lookup"><span data-stu-id="65627-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="65627-458">顶级通配符 `*` 允许所有非空主机。</span><span class="sxs-lookup"><span data-stu-id="65627-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="65627-459">子域通配符允许使用，但不匹配根域。</span><span class="sxs-lookup"><span data-stu-id="65627-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="65627-460">例如，`*.contoso.com` 匹配子域 `foo.contoso.com`，但不匹配根域 `contoso.com`。</span><span class="sxs-lookup"><span data-stu-id="65627-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="65627-461">允许使用 Unicode 主机名，但应转换为 [Punycode](https://tools.ietf.org/html/rfc3492) 进行匹配。</span><span class="sxs-lookup"><span data-stu-id="65627-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="65627-462">[IPv6 地址](https://tools.ietf.org/html/rfc4291)必须包括边界方括号，而且必须位于[常规窗体](https://tools.ietf.org/html/rfc4291#section-2.2)（例如，`[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`）中。</span><span class="sxs-lookup"><span data-stu-id="65627-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="65627-463">IPv6 地址并非专门用于检查不同格式之间的逻辑相等性，也不执行标准化。</span><span class="sxs-lookup"><span data-stu-id="65627-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="65627-464">未能限制允许的主机可能会允许攻击者访问该服务生成的欺骗链接。</span><span class="sxs-lookup"><span data-stu-id="65627-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="65627-465">默认值为空的 `IList<string>`。</span><span class="sxs-lookup"><span data-stu-id="65627-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="65627-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="65627-467">如果代理/转发器不使用 `X-Forwarded-For` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="65627-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65627-468">默认值为 `X-Forwarded-For`。</span><span class="sxs-lookup"><span data-stu-id="65627-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="65627-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | 标识应处理的转发器。</span><span class="sxs-lookup"><span data-stu-id="65627-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="65627-470">对于应用的字段的列表，请参阅 [ForwardedHeaders 枚举](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65627-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="65627-471">分配给此属性的典型值为 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。</span><span class="sxs-lookup"><span data-stu-id="65627-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="65627-472">默认值是 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)。</span><span class="sxs-lookup"><span data-stu-id="65627-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="65627-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="65627-474">如果代理/转发器不使用 `X-Forwarded-Host` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="65627-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65627-475">默认值为 `X-Forwarded-Host`。</span><span class="sxs-lookup"><span data-stu-id="65627-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="65627-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="65627-477">如果代理/转发器不使用 `X-Forwarded-Proto` 标头，但使用一些其他标头来转发信息，则使用此选项。</span><span class="sxs-lookup"><span data-stu-id="65627-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="65627-478">默认值为 `X-Forwarded-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65627-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="65627-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | 限制所处理的标头中的条目数。</span><span class="sxs-lookup"><span data-stu-id="65627-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="65627-480">设置为 `null` 以禁用此限制，但仅应在已配置 `KnownProxies` 或 `KnownNetworks` 的情况下执行此操作。</span><span class="sxs-lookup"><span data-stu-id="65627-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="65627-481">设置非 `null` 值是一种预防措施（但不是保证），防止错误配置的代理和恶意请求从网络的侧通道到达。</span><span class="sxs-lookup"><span data-stu-id="65627-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="65627-482">转接头中间件按照从右向左的相反顺序处理标头。</span><span class="sxs-lookup"><span data-stu-id="65627-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="65627-483">如果使用默认值 (`1`)，则只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。</span><span class="sxs-lookup"><span data-stu-id="65627-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="65627-484">默认值为 `1`。</span><span class="sxs-lookup"><span data-stu-id="65627-484">The default is `1`.</span></span> <span data-ttu-id="65627-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | 从中接受转接头的已知网络的地址范围。</span><span class="sxs-lookup"><span data-stu-id="65627-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="65627-486">使用无类别域际路由选择 (CIDR) 表示法提供 IP 范围。</span><span class="sxs-lookup"><span data-stu-id="65627-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="65627-487">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。</span><span class="sxs-lookup"><span data-stu-id="65627-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="65627-488">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65627-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65627-489">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="65627-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="65627-490">有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。</span><span class="sxs-lookup"><span data-stu-id="65627-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="65627-491">默认值是 `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>>，其中包含 `IPAddress.Loopback` 的单个条目。</span><span class="sxs-lookup"><span data-stu-id="65627-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="65627-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | 从中接受转接头的已知代理的地址。</span><span class="sxs-lookup"><span data-stu-id="65627-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="65627-493">使用 `KnownProxies` 指定精确的 IP 地址匹配。</span><span class="sxs-lookup"><span data-stu-id="65627-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="65627-494">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。</span><span class="sxs-lookup"><span data-stu-id="65627-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="65627-495">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65627-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65627-496">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="65627-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="65627-497">有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。</span><span class="sxs-lookup"><span data-stu-id="65627-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="65627-498">默认值是 `IList`\<<xref:System.Net.IPAddress>>，其中包含 `IPAddress.IPv6Loopback` 的单个条目。</span><span class="sxs-lookup"><span data-stu-id="65627-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="65627-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="65627-500">默认值为 `X-Original-For`。</span><span class="sxs-lookup"><span data-stu-id="65627-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="65627-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="65627-502">默认值为 `X-Original-Host`。</span><span class="sxs-lookup"><span data-stu-id="65627-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="65627-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) 指定的标头。</span><span class="sxs-lookup"><span data-stu-id="65627-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="65627-504">默认值为 `X-Original-Proto`。</span><span class="sxs-lookup"><span data-stu-id="65627-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="65627-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | 要求正在处理的 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 之间的标头值数量保持同步。</span><span class="sxs-lookup"><span data-stu-id="65627-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="65627-506">ASP.NET Core 1.x 默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="65627-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="65627-507">ASP.NET Core 2.0 默认值是 `false`。</span><span class="sxs-lookup"><span data-stu-id="65627-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="65627-508">方案与使用案例</span><span class="sxs-lookup"><span data-stu-id="65627-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="65627-509">无法添加转接头并且所有请求都安全的情况</span><span class="sxs-lookup"><span data-stu-id="65627-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="65627-510">在某些情况下，可能无法将转接头添加到代理到应用的请求中。</span><span class="sxs-lookup"><span data-stu-id="65627-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="65627-511">如果代理强制将所有公共外部请求执行为 HTTPS，则在使用任何类型的中间件之前，可以在 `Startup.Configure` 中手动设置该方案：</span><span class="sxs-lookup"><span data-stu-id="65627-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="65627-512">此代码可以通过环境变量或者开发环境或过渡环境中的其他配置设置来禁用。</span><span class="sxs-lookup"><span data-stu-id="65627-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="65627-513">处理改变请求路径的基路径和代理</span><span class="sxs-lookup"><span data-stu-id="65627-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="65627-514">一些代理可完整地传递路径，但是会删除应用基路径以便路由可正常工作。</span><span class="sxs-lookup"><span data-stu-id="65627-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="65627-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 中间件将路径拆分为 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)，将应用基路径拆分为 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)。</span><span class="sxs-lookup"><span data-stu-id="65627-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="65627-516">如果 `/foo` 是作为 `/foo/api/1` 传递的代理路径的应用基路径，则中间件使用以下命令将 `Request.PathBase` 设置为 `/foo`，将 `Request.Path` 设置为 `/api/1`：</span><span class="sxs-lookup"><span data-stu-id="65627-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="65627-517">当再次反向调用中间件时，将重新应用原始路径和基路径。</span><span class="sxs-lookup"><span data-stu-id="65627-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="65627-518">要详细了解中间件处理顺序，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="65627-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="65627-519">如果代理剪裁路径（例如，将 `/foo/api/1` 转接到 `/api/1`），请通过设置请求的 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 属性来修复重定向和链接：</span><span class="sxs-lookup"><span data-stu-id="65627-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="65627-520">如果代理要添加路径数据，请使用 <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> 并分配给 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 属性，从而放弃部分路径以修复重定向和链接：</span><span class="sxs-lookup"><span data-stu-id="65627-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="65627-521">配置使用不同标头名称的代理</span><span class="sxs-lookup"><span data-stu-id="65627-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="65627-522">如果代理不使用名为 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的标头来转发代理地址/端口和原始架构信息，则设置 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 和 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 选项，使其与代理所用的标头名称相匹配：</span><span class="sxs-lookup"><span data-stu-id="65627-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="65627-523">对表示为 IPv6 地址的 IPv4 地址进行配置</span><span class="sxs-lookup"><span data-stu-id="65627-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="65627-524">如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1` 或 `::ffff:a00:1`）。</span><span class="sxs-lookup"><span data-stu-id="65627-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="65627-525">请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。</span><span class="sxs-lookup"><span data-stu-id="65627-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="65627-526">通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。</span><span class="sxs-lookup"><span data-stu-id="65627-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="65627-527">在以下示例中，提供转接头的网络地址以 IPv6 格式添加到 `KnownNetworks` 列表中。</span><span class="sxs-lookup"><span data-stu-id="65627-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="65627-528">IPv4 地址：`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="65627-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="65627-529">转换后的 IPv6 地址：`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="65627-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="65627-530">转换后的前缀长度：104</span><span class="sxs-lookup"><span data-stu-id="65627-530">Converted prefix length: 104</span></span>

<span data-ttu-id="65627-531">也可以提供十六进制格式的地址（`10.11.12.1` 以 IPv6 格式表示为 `::ffff:0a0b:0c01`）。</span><span class="sxs-lookup"><span data-stu-id="65627-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="65627-532">将 IPv4 地址转换为 IPv6 时，将 96 添加到 CIDR 前缀长度（示例中的 `8`）以说明其他 `::ffff:` IPv6 前缀 (8 + 96 = 104)。</span><span class="sxs-lookup"><span data-stu-id="65627-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="65627-533">转发 Linux 和非 IIS 反向代理的方案</span><span class="sxs-lookup"><span data-stu-id="65627-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="65627-534">如果将站点部署到 Azure Linux 应用服务、Azure Linux 虚拟机 (VM)，或者部署到除 IIS 之外的任何其他反向代理之后，调用 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 和 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 的应用都会使站点进入无限循环。</span><span class="sxs-lookup"><span data-stu-id="65627-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="65627-535">反向代理终止 TLS，并且 Kestrel 未发现正确的请求方案。</span><span class="sxs-lookup"><span data-stu-id="65627-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="65627-536">由于 OAuth 和 OIDC 生成了错误的重定向，因此它们在此配置中也会出现故障。</span><span class="sxs-lookup"><span data-stu-id="65627-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="65627-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 在 IIS 之后运行时会添加和配置转接头中间件，但 Linux（Apache 或 Nginx 集成）没有匹配的自动配置。</span><span class="sxs-lookup"><span data-stu-id="65627-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="65627-538">要从非 IIS 方案中的代理中转发方案，请添加并配置转接头中间件。</span><span class="sxs-lookup"><span data-stu-id="65627-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="65627-539">在 `Startup.ConfigureServices` 中，使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="65627-539">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="65627-540">疑难解答</span><span class="sxs-lookup"><span data-stu-id="65627-540">Troubleshoot</span></span>

<span data-ttu-id="65627-541">如果未按预期转接标头，请启用[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="65627-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="65627-542">如果日志没有提供足够的信息来解决问题，请枚举服务器收到的请求标头。</span><span class="sxs-lookup"><span data-stu-id="65627-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="65627-543">使用内联中间件将请求标头写入应用程序响应或记录标头。</span><span class="sxs-lookup"><span data-stu-id="65627-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="65627-544">要将标头写入应用的响应，请在 `Startup.Configure` 中调用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 后立即放置以下终端内联中间件：</span><span class="sxs-lookup"><span data-stu-id="65627-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="65627-545">可以写入日志，而不是响应正文。</span><span class="sxs-lookup"><span data-stu-id="65627-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="65627-546">借助写入日志，站点可在调试时正常运行。</span><span class="sxs-lookup"><span data-stu-id="65627-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="65627-547">要写入日志而不是响应正文，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="65627-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="65627-548">将 `ILogger<Startup>` 注入到 `Startup` 类中，如[在启动时创建日志](xref:fundamentals/logging/index#create-logs-in-startup)中所述。</span><span class="sxs-lookup"><span data-stu-id="65627-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="65627-549">在 `Startup.Configure` 中调用 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 之后，立即放置以下内联中间件。</span><span class="sxs-lookup"><span data-stu-id="65627-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="65627-550">处理时，`X-Forwarded-{For|Proto|Host}` 值将移至 `X-Original-{For|Proto|Host}`。</span><span class="sxs-lookup"><span data-stu-id="65627-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="65627-551">如果给定标头中有多个值，则转接头中间件按照从右向左的相反顺序处理标头。</span><span class="sxs-lookup"><span data-stu-id="65627-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="65627-552">默认 `ForwardLimit` 为 `1`（一），因此只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。</span><span class="sxs-lookup"><span data-stu-id="65627-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="65627-553">在处理转接头之前，请求的原始远程 IP 必须与 `KnownProxies` 或 `KnownNetworks` 列表中的条目匹配。</span><span class="sxs-lookup"><span data-stu-id="65627-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="65627-554">这通过不接受来自不受信任的代理的转发器来限制标头欺骗。</span><span class="sxs-lookup"><span data-stu-id="65627-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="65627-555">检测到未知代理时，日志记录会指出代理的地址：</span><span class="sxs-lookup"><span data-stu-id="65627-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="65627-556">在上述示例中，10.0.0.100 是代理服务器。</span><span class="sxs-lookup"><span data-stu-id="65627-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="65627-557">如果该服务器是受信任的代理，请将服务器的 IP 地址添加到 `Startup.ConfigureServices` 中的 `KnownProxies`（或将受信任的网络添加到 `KnownNetworks`）。</span><span class="sxs-lookup"><span data-stu-id="65627-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="65627-558">有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)部分。</span><span class="sxs-lookup"><span data-stu-id="65627-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="65627-559">仅允许受信任的代理和网络转接头。</span><span class="sxs-lookup"><span data-stu-id="65627-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="65627-560">否则，可能会受到 [IP 欺骗](https://www.iplocation.net/ip-spoofing)攻击。</span><span class="sxs-lookup"><span data-stu-id="65627-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65627-561">其他资源</span><span class="sxs-lookup"><span data-stu-id="65627-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="65627-562">Microsoft 安全公告 CVE-2018-0787：ASP.NET Core 特权提升漏洞</span><span class="sxs-lookup"><span data-stu-id="65627-562">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
