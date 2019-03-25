---
title: 配置 ASP.NET Core 以使用代理服务器和负载均衡器
author: guardrex
description: 了解在代理服务器和负载均衡器后方托管的应用程序的配置，这通常会隐藏重要的请求信息。
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2018
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 3ac67f0cb0c7b472e7192f684b1a8fc9685794ce
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264897"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>配置 ASP.NET Core 以使用代理服务器和负载均衡器

作者：[Luke Latham](https://github.com/guardrex)、[Chris Ross](https://github.com/Tratcher)

在 ASP.NET Core 推荐配置中，应用使用 IIS/ASP.NET Core 模块、Nginx 或 Apache 进行托管。 代理服务器、负载均衡器和其他网络设备通常会在请求到达应用之前隐藏有关请求的信息：

* 当通过 HTTP 代理 HTTPS 请求时，原方案 (HTTPS) 将丢失，并且必须在标头中转接。
* 由于应用收到来自代理的请求，而不是 Internet 或公司网络上请求的真实源，因此原始客户端 IP 地址也必须在标头中转接。

此信息在请求处理中可能很重要，例如在重定向、身份验证、链接生成、策略评估和客户端地理位置中。

## <a name="forwarded-headers"></a>转接头

按照约定，代理转接 HTTP 标头中的信息。

| Header | 说明 |
| ------ | ----------- |
| X-Forwarded-For | 保存代理链中关于发起请求的客户端和后续代理的信息。 该参数可能包含 IP 地址（以及可选端口号）。 在代理服务器链中，第一个参数表示最初发出请求的客户端。 后续代理标识符以此类推。 链中的最后一个代理不在参数列表中。 最后一个代理的 IP 地址以及可选的端口号可用作传输层的远程 IP 地址。 |
| X-Forwarded-Proto | 原方案的值 (HTTP/HTTPS)。 如果请求已遍历多个代理，则该值也可以是方案列表。 |
| X-Forwarded-Host | 主机标头字段的原始值。 代理通常不会修改主机标头。 有关特权提升漏洞的信息，请参阅 [Microsoft 安全公告 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)，该漏洞影响代理未验证或将主机标头限制为已知正确值的系统。 |

来自 [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 包的转接标头中间件读取这些标头，并填充 [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext) 上的关联字段。

中间件更新：

* [HttpContext.Connection.RemoteIpAddress](/dotnet/api/microsoft.aspnetcore.http.connectioninfo.remoteipaddress) &ndash; 使用 `X-Forwarded-For` 标头值进行设置。 影响中间件如何设置 `RemoteIpAddress` 的其他设置。 有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。
* [HttpContext.Request.Scheme](/dotnet/api/microsoft.aspnetcore.http.httprequest.scheme) &ndash; 使用 `X-Forwarded-Proto` 标头值进行设置。
* [HttpContext.Request.Host](/dotnet/api/microsoft.aspnetcore.http.httprequest.host) &ndash; 使用 `X-Forwarded-Host` 标头值进行设置。

可以配置转接头中间件[默认设置](#forwarded-headers-middleware-options)。 默认设置为：

* 应用和请求源之间只有一个代理。
* 仅将环回地址配置为已知代理和已知网络。
* 转接头被命名为 `X-Forwarded-For` 和 `X-Forwarded-Proto`。

并非所有网络设备均可在没有其他配置的情况下添加 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。 如果代理请求在到达应用时未包含这些标头，请查阅设备制造商提供的指导。 如果设备使用 `X-Forwarded-For` 和 `X-Forwarded-Proto` 以外的其他标头，请设置 [ForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedforheadername) 和 [ForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedprotoheadername) 选项，使其与设备所用的标头名称相匹配。 有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)和[配置使用不同标头名称的代理](#configuration-for-a-proxy-that-uses-different-header-names)。

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS/IIS Express 和 ASP.NET Core 模块

当应用在 IIS 和 ASP.NET Core 模块后方运行时，转接头中间件由 IIS 集成中间件默认启用。 由于转接头的信任问题（例如，[IP 欺骗](https://www.iplocation.net/ip-spoofing)），转接头中间件被激活为首先在中间件管道中运行，并具有特定于 ASP.NET Core 模块的受限配置。 中间件配置为转接 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，并且被限制到单个本地 localhost 代理。 如果需要其他配置，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)。

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>其他代理服务器和负载均衡器方案

除使用 IIS 集成中间件之外，不会默认启用转接头中间件。 必须启用应用的转接头中间件才能处理带有 [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) 的转接头。 启用中间件后，如果没有为中间件指定 [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions)，那么默认的 [ForwardedHeadersOptions.ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) 是 [ForwardedHeaders.None](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders)。

使用 [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 配置中间件，以转接 `Startup.ConfigureServices` 中的 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头。 调用其他中间件之前，调用 `Startup.Configure` 中的 [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) 方法：

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
> 如果 [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 没有在 `Startup.ConfigureServices` 中指定或没有通过 [UseForwardedHeaders(IApplicationBuilder, ForwardedHeadersOptions)](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_ForwardedHeadersExtensions_UseForwardedHeaders_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_ForwardedHeadersOptions_) 直接指向扩展方法，则要转接的默认标头为 [ForwardedHeaders.None](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders)。 [ForwardedHeadersOptions.ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) 属性必须配置为标头才能转接。

## <a name="nginx-configuration"></a>Nginx 配置

要转发 `X-Forwarded-For` 和 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-nginx#configure-nginx>。 有关详细信息，请参阅 [NGINX：使用转发标头](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)。

## <a name="apache-configuration"></a>Apache 配置

`X-Forwarded-For` 将会自动添加（请参阅 [Apache 模块 mod_proxy：反向代理请求标头](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)）。 要了解如何转发 `X-Forwarded-Proto` 标头，请参阅 <xref:host-and-deploy/linux-apache#configure-apache>。

## <a name="forwarded-headers-middleware-options"></a>转接头中间件选项

[ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 控制转接头中间件的行为。 以下示例更改了默认值：

* 将转接头中的条目数量限制为 `2`。
* 添加已知的代理地址 `127.0.10.1`。
* 将转接头名称从默认的 `X-Forwarded-For` 更改为 `X-Forwarded-For-My-Custom-Header-Name`。

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

::: moniker range=">= aspnetcore-2.1"

| 选项 | 说明 |
| ------ | ----------- |
| AllowedHosts | 通过 `X-Forwarded-Host` 标头将主机限制为提供的值。<ul><li>使用 ordinal-ignore-case 比较值。</li><li>必须排除端口号。</li><li>如果列表为空，则允许使用所有主机。</li><li>顶级通配符 `*` 允许所有非空主机。</li><li>子域通配符允许使用，但不匹配根域。 例如，`*.contoso.com` 匹配子域 `foo.contoso.com`，但不匹配根域 `contoso.com`。</li><li>允许使用 Unicode 主机名，但应转换为 [Punycode](https://tools.ietf.org/html/rfc3492) 进行匹配。</li><li>[IPv6 地址](https://tools.ietf.org/html/rfc4291)必须包括边界方括号，而且必须位于[常规窗体](https://tools.ietf.org/html/rfc4291#section-2.2)（例如，`[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`）中。 IPv6 地址并非专门用于检查不同格式之间的逻辑相等性，也不执行标准化。</li><li>未能限制允许的主机可能会允许攻击者访问该服务生成的欺骗链接。</li></ul>默认值为一个空 [IList\<string>](/dotnet/api/system.collections.generic.ilist-1)。 |
| [ForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedforheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedforheadername) 指定的标头。 如果代理/转发器不使用 `X-Forwarded-For` 标头，但使用一些其他标头来转发信息，则使用此选项。<br><br>默认值为 `X-Forwarded-For`。 |
| [ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) | 标识应处理的转发器。 对于应用的字段的列表，请参阅 [ForwardedHeaders 枚举](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders)。 分配给此属性的典型值为 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。<br><br>默认值是 [ForwardedHeaders.None](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders)。 |
| [ForwardedHostHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedhostheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedHostHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedhostheadername) 指定的标头。 如果代理/转发器不使用 `X-Forwarded-Host` 标头，但使用一些其他标头来转发信息，则使用此选项。<br><br>默认值为 `X-Forwarded-Host`。 |
| [ForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedprotoheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedprotoheadername) 指定的标头。 如果代理/转发器不使用 `X-Forwarded-Proto` 标头，但使用一些其他标头来转发信息，则使用此选项。<br><br>默认值为 `X-Forwarded-Proto`。 |
| [ForwardLimit](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardlimit) | 限制所处理的标头中的条目数。 设置为 `null` 以禁用此限制，但仅应在已配置 `KnownProxies` 或 `KnownNetworks` 的情况下执行此操作。<br><br>默认值为 1。 |
| [KnownNetworks](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.knownnetworks) | 从中接受转接头的已知网络的地址范围。 使用无类别域际路由选择 (CIDR) 表示法提供 IP 范围。<br><br>如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。 请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。 通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。 有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。<br><br>默认值是 [IList](/dotnet/api/system.collections.generic.ilist-1)\<[IPNetwork](/dotnet/api/microsoft.aspnetcore.httpoverrides.ipnetwork)>，包含 `IPAddress.Loopback` 的单个条目。 |
| [KnownProxies](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.knownproxies) | 从中接受转接头的已知代理的地址。 使用 `KnownProxies` 指定精确的 IP 地址匹配。<br><br>如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。 请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。 通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。 有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。<br><br>默认值是 [IList](/dotnet/api/system.collections.generic.ilist-1)\<[IPAddress](/dotnet/api/system.net.ipaddress)>，包含 `IPAddress.IPv6Loopback` 的单个条目。 |
| [OriginalForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalforheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalForHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalforheadername) 指定的标头。<br><br>默认值为 `X-Original-For`。 |
| [OriginalHostHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalhostheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalHostHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalhostheadername) 指定的标头。<br><br>默认值为 `X-Original-Host`。 |
| [OriginalProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalprotoheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalprotoheadername) 指定的标头。<br><br>默认值为 `X-Original-Proto`。 |
| [RequireHeaderSymmetry](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.requireheadersymmetry) | 要求正在处理的 [ForwardedHeadersOptions.ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) 之间的标头值数量保持同步。<br><br>ASP.NET Core 1.x 默认值是 `true`。 ASP.NET Core 2.0 默认值是 `false`。 |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| 选项 | 说明 |
| ------ | ----------- |
| [ForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedforheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedforheadername) 指定的标头。 如果代理/转发器不使用 `X-Forwarded-For` 标头，但使用一些其他标头来转发信息，则使用此选项。<br><br>默认值为 `X-Forwarded-For`。 |
| [ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) | 标识应处理的转发器。 对于应用的字段的列表，请参阅 [ForwardedHeaders 枚举](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders)。 分配给此属性的典型值为 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`。<br><br>默认值是 [ForwardedHeaders.None](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders)。 |
| [ForwardedHostHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedhostheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedHostHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedhostheadername) 指定的标头。 如果代理/转发器不使用 `X-Forwarded-Host` 标头，但使用一些其他标头来转发信息，则使用此选项。<br><br>默认值为 `X-Forwarded-Host`。 |
| [ForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedprotoheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedprotoheadername) 指定的标头。 如果代理/转发器不使用 `X-Forwarded-Proto` 标头，但使用一些其他标头来转发信息，则使用此选项。<br><br>默认值为 `X-Forwarded-Proto`。 |
| [ForwardLimit](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardlimit) | 限制所处理的标头中的条目数。 设置为 `null` 以禁用此限制，但仅应在已配置 `KnownProxies` 或 `KnownNetworks` 的情况下执行此操作。<br><br>默认值为 1。 |
| [KnownNetworks](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.knownnetworks) | 从中接受转接头的已知网络的地址范围。 使用无类别域际路由选择 (CIDR) 表示法提供 IP 范围。<br><br>如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。 请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。 通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。 有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。<br><br>默认值是 [IList](/dotnet/api/system.collections.generic.ilist-1)\<[IPNetwork](/dotnet/api/microsoft.aspnetcore.httpoverrides.ipnetwork)>，包含 `IPAddress.Loopback` 的单个条目。 |
| [KnownProxies](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.knownproxies) | 从中接受转接头的已知代理的地址。 使用 `KnownProxies` 指定精确的 IP 地址匹配。<br><br>如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1`）。 请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。 通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。 有关详细信息，请参阅[对表示为 IPv6 地址的 IPv4 地址进行配置](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)部分。<br><br>默认值是 [IList](/dotnet/api/system.collections.generic.ilist-1)\<[IPAddress](/dotnet/api/system.net.ipaddress)>，包含 `IPAddress.IPv6Loopback` 的单个条目。 |
| [OriginalForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalforheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalForHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalforheadername) 指定的标头。<br><br>默认值为 `X-Original-For`。 |
| [OriginalHostHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalhostheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalHostHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalhostheadername) 指定的标头。<br><br>默认值为 `X-Original-Host`。 |
| [OriginalProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalprotoheadername) | 使用由此属性指定的标头，而不是由 [ForwardedHeadersDefaults.XOriginalProtoHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalprotoheadername) 指定的标头。<br><br>默认值为 `X-Original-Proto`。 |
| [RequireHeaderSymmetry](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.requireheadersymmetry) | 要求正在处理的 [ForwardedHeadersOptions.ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) 之间的标头值数量保持同步。<br><br>ASP.NET Core 1.x 默认值是 `true`。 ASP.NET Core 2.0 默认值是 `false`。 |

::: moniker-end

## <a name="scenarios-and-use-cases"></a>方案与使用案例

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>无法添加转接头并且所有请求都安全的情况

在某些情况下，可能无法将转接头添加到代理到应用的请求中。 如果代理强制将所有公共外部请求执行为 HTTPS，则在使用任何类型的中间件之前，可以在 `Startup.Configure` 中手动设置该方案：

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

此代码可以通过环境变量或者开发环境或过渡环境中的其他配置设置来禁用。

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>处理改变请求路径的基路径和代理

一些代理可完整地传递路径，但是会删除应用基路径以便路由可正常工作。 [UsePathBaseExtensions.UsePathBase](/dotnet/api/microsoft.aspnetcore.builder.usepathbaseextensions.usepathbase) 中间件将路径拆分为 [HttpRequest.Path](/dotnet/api/microsoft.aspnetcore.http.httprequest.path)，将应用基路径拆分为 [HttpRequest.PathBase](/dotnet/api/microsoft.aspnetcore.http.httprequest.pathbase)。

如果 `/foo` 是作为 `/foo/api/1` 传递的代理路径的应用基路径，则中间件使用以下命令将 `Request.PathBase` 设置为 `/foo`，将 `Request.Path` 设置为 `/api/1`：

```csharp
app.UsePathBase("/foo");
```

当再次反向调用中间件时，将重新应用原始路径和基路径。 要详细了解中间件处理顺序，请参阅 <xref:fundamentals/middleware/index>。

如果代理剪裁路径（例如，将 `/foo/api/1` 转接到 `/api/1`），请通过设置请求的 [PathBase](/dotnet/api/microsoft.aspnetcore.http.httprequest.pathbase) 属性来修复重定向和链接：

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

如果代理正在添加路径数据，请使用 [StartsWithSegments(PathString, PathString)](/dotnet/api/microsoft.aspnetcore.http.pathstring.startswithsegments#Microsoft_AspNetCore_Http_PathString_StartsWithSegments_Microsoft_AspNetCore_Http_PathString_Microsoft_AspNetCore_Http_PathString__) 并分配给 [Path](/dotnet/api/microsoft.aspnetcore.http.httprequest.path) 属性，从而放弃部分路径以修复重定向和链接：

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>配置使用不同标头名称的代理

如果代理不使用名为 `X-Forwarded-For` 和 `X-Forwarded-Proto` 的标头来转发代理地址/端口和原始架构信息，则设置 [ForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedforheadername) 和 [ForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedprotoheadername) 选项，使其与代理所用的标头名称相匹配：

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>对表示为 IPv6 地址的 IPv4 地址进行配置

如果服务器使用双模式套接字，则采用 IPv6 格式提供 IPv4 地址（例如，IPv4 格式的 `10.0.0.1` 以 IPv6 格式表示为 `::ffff:10.0.0.1` 或 `::ffff:a00:1`）。 请参阅 [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)。 通过查看 [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*) 来确定是否需要采用此格式。

在以下示例中，提供转接头的网络地址以 IPv6 格式添加到 `KnownNetworks` 列表中。

IPv4 地址：`10.11.12.1/8`

转换后的 IPv6 地址：`::ffff:10.11.12.1`  
转换后的前缀长度：104

也可以提供十六进制格式的地址（`10.11.12.1` 以 IPv6 格式表示为 `::ffff:0a0b:0c01`）。 将 IPv4 地址转换为 IPv6 时，将 96 添加到 CIDR 前缀长度（示例中的 `8`）以说明其他 `::ffff:` IPv6 前缀 (8 + 96 = 104)。 

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

## <a name="troubleshoot"></a>疑难解答

如果未按预期转接标头，请启用[日志记录](xref:fundamentals/logging/index)。 如果日志没有提供足够的信息来解决问题，请枚举服务器收到的请求标头。 使用内联中间件将请求标头写入应用程序响应或记录标头。 调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> 后，立即放置以下任意代码示例。

若要将标头写入应用程序的响应，请使用以下终端内联中间件：

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

还可以通过使用以下内联中间件写入日志而不是响应正文。 这样可以使站点在调试时正常运行。

```csharp
var logger = _loggerFactory.CreateLogger<Startup>();

app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    logger.LogDebug("Request Method: {METHOD}", context.Request.Method);
    logger.LogDebug("Request Scheme: {SCHEME}", context.Request.Scheme);
    logger.LogDebug("Request Path: {PATH}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        logger.LogDebug("Header: {KEY}: {VALUE}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    logger.LogDebug("Request RemoteIp: {REMOTE_IP_ADDRESS}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

处理时，`X-Forwarded-{For|Proto|Host}` 值将移至 `X-Original-{For|Proto|Host}`。 如果给定标头中有多个值，请注意转接头中间件按照从右向左的相反顺序处理标头。 默认 `ForwardLimit` 为 1（一），因此只会处理标头最右侧的值，除非增加 `ForwardLimit` 的值。

在处理转接头之前，请求的原始远程 IP 必须与 `KnownProxies` 或 `KnownNetworks` 列表中的条目匹配。 这通过不接受来自不受信任的代理的转发器来限制标头欺骗。 检测到未知代理时，日志记录会指出代理的地址：

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

在上述示例中，10.0.0.100 是代理服务器。 如果该服务器是受信任的代理，请将服务器的 IP 地址添加到 `Startup.ConfigureServices` 中的 `KnownProxies`（或将受信任的网络添加到 `KnownNetworks`）。 有关详细信息，请参阅[转接头中间件选项](#forwarded-headers-middleware-options)部分。

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> 仅允许受信任的代理和网络转接头。 否则，可能会受到 [IP 欺骗](https://www.iplocation.net/ip-spoofing)攻击。

## <a name="additional-resources"></a>其他资源

* <xref:host-and-deploy/web-farm>
* [Microsoft 安全公告 CVE-2018-0787：ASP.NET Core 特权提升漏洞](https://github.com/aspnet/Announcements/issues/295)
