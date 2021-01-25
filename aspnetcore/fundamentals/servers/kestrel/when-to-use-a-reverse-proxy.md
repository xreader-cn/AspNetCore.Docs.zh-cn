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
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="c9768-103">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="c9768-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="c9768-104">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="c9768-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="c9768-105">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c9768-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="c9768-106">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="c9768-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](_static/kestrel-to-internet2.png)

<span data-ttu-id="c9768-108">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="c9768-108">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](_static/kestrel-to-internet.png)

<span data-ttu-id="c9768-110">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="c9768-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="c9768-111">如果在没有反向代理服务器的情况下将 Kestrel 用作边缘服务器，则不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="c9768-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="c9768-112">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="c9768-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="c9768-113">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="c9768-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="c9768-114">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="c9768-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="c9768-115">反向代理：</span><span class="sxs-lookup"><span data-stu-id="c9768-115">A reverse proxy:</span></span>

* <span data-ttu-id="c9768-116">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="c9768-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="c9768-117">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="c9768-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="c9768-118">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="c9768-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="c9768-119">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="c9768-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="c9768-120">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="c9768-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="c9768-121">采用反向代理配置进行托管需要[主机筛选](xref:fundamentals/servers/kestrel/host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="c9768-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9768-122">其他资源</span><span class="sxs-lookup"><span data-stu-id="c9768-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

