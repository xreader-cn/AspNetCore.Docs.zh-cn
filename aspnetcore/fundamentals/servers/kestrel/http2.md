---
title: 对 ASP.NET Core Kestrel Web 服务器使用 HTTP/2
author: rick-anderson
description: 了解如何对 Kestrel（跨平台 ASP.NET Core Web 服务器）使用 HTTP/2。
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253831"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="2cf8f-103">对 ASP.NET Core Kestrel Web 服务器使用 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2cf8f-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="2cf8f-104">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="2cf8f-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2cf8f-105">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="2cf8f-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="2cf8f-106">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2cf8f-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2cf8f-107">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="2cf8f-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2cf8f-108">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="2cf8f-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2cf8f-109">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="2cf8f-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2cf8f-110">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="2cf8f-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2cf8f-111">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="2cf8f-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2cf8f-112">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="2cf8f-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2cf8f-113">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="2cf8f-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2cf8f-114">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="2cf8f-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2cf8f-115">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="2cf8f-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="2cf8f-116">从 .NET Core 3.0 开始，HTTP/2 默认处于启用状态。</span><span class="sxs-lookup"><span data-stu-id="2cf8f-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="2cf8f-117">有关配置的详细信息，请参阅 [Kestrel HTTP/2 限制](xref:fundamentals/servers/kestrel/options#http2-limits)和 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="2cf8f-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="2cf8f-118">高级 HTTP/2 功能</span><span class="sxs-lookup"><span data-stu-id="2cf8f-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="2cf8f-119">Kestrel 中的其他 HTTP/2 功能支持 gRPC，包括对响应尾部和发送重置帧的支持。</span><span class="sxs-lookup"><span data-stu-id="2cf8f-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="2cf8f-120">预告片</span><span class="sxs-lookup"><span data-stu-id="2cf8f-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="2cf8f-121">重置</span><span class="sxs-lookup"><span data-stu-id="2cf8f-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
