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
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>对 ASP.NET Core Kestrel Web 服务器使用 HTTP/2

如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：

* 操作系统&dagger;
  * Windows Server 2016/Windows 10 或更高版本&Dagger;
  * 具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）
* 目标框架：.NET Core 2.2 或更高版本
* [应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接
* TLS 1.2 或更高版本的连接

macOS 的未来版本将支持 &dagger;HTTP/2。
&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。 支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。 可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。

如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) 会报告 `HTTP/2`。

从 .NET Core 3.0 开始，HTTP/2 默认处于启用状态。 有关配置的详细信息，请参阅 [Kestrel HTTP/2 限制](xref:fundamentals/servers/kestrel/options#http2-limits)和 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 部分。

## <a name="advanced-http2-features"></a>高级 HTTP/2 功能

Kestrel 中的其他 HTTP/2 功能支持 gRPC，包括对响应尾部和发送重置帧的支持。

### <a name="trailers"></a>预告片

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>重置

[!INCLUDE[](~/includes/reset.md)]
