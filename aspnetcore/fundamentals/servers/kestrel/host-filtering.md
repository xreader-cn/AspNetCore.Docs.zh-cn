---
title: 对 ASP.NET Core Kestrel Web 服务器使用主机筛选
author: rick-anderson
description: 了解如何对 Kestrel（跨平台 ASP.NET Core Web 服务器）使用主机筛选。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel/host-filtering
ms.openlocfilehash: d55c211f05a77f6acabedef2ff62a621d9a1844e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253834"
---
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a>对 ASP.NET Core Kestrel Web 服务器使用主机筛选

尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。 主机 `localhost` 是一个特殊情况，用于绑定至环回地址。 除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。 不验证 `Host` 标头。

解决方法是，使用主机筛选中间件。 主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包为 ASP.NET Core 应用隐式提供。 由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 添加中间件：

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

默认情况下，主机筛选中间件处于禁用状态。 要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。 此值是以分号分隔的不带端口号的主机名列表：

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。 转接头中间件和主机筛选中间件具有适合不同方案的相似功能。 如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。 将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。
>
> 有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。
