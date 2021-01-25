---
title: 何时对 ASP.NET Core Kestrel Web 服务器使用反向代理
author: rick-anderson
description: 了解何时在 Kestrel（跨平台 ASP.NET Core Web 服务器）前面使用反向代理。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253825"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>何时结合使用 Kestrel 和反向代理

可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。 反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。

Kestrel 用作边缘（面向 Internet）Web 服务器：

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](_static/kestrel-to-internet2.png)

Kestrel 用于反向代理配置：

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](_static/kestrel-to-internet.png)

无论配置是否使用反向代理服务器，都是受支持的托管配置。

如果在没有反向代理服务器的情况下将 Kestrel 用作边缘服务器，则不支持在多个进程间共享相同的 IP 和端口。 如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。 可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。

即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。

反向代理：

* 可以限制所承载的应用中的公开的公共外围应用。
* 提供额外的配置和防护层。
* 可以更好地与现有基础结构集成。
* 简化了负载均和和安全通信 (HTTPS) 配置。 仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。

> [!WARNING]
> 采用反向代理配置进行托管需要[主机筛选](xref:fundamentals/servers/kestrel/host-filtering)。

## <a name="additional-resources"></a>其他资源

<xref:host-and-deploy/proxy-load-balancer>

