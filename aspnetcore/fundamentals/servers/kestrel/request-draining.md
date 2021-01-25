---
title: 对 ASP.NET Core Kestrel Web 服务器使用请求排出
author: rick-anderson
description: 了解如何对 Kestrel（跨平台 ASP.NET Core Web 服务器）使用请求排出。
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253829"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>对 ASP.NET Core Kestrel Web 服务器使用请求排出

打开 HTTP 连接非常耗时。 对于 HTTPS 而言，这也是资源密集型。 因此，Kestrel 会尝试按 HTTP/1.1 协议重新使用连接。 请求正文必须完全使用才能允许重新使用连接。 应用不会始终使用请求正文，例如 HTTP POST 请求，其中服务器返回重定向或 404 响应。 在 HTTP POST 重定向的情况下：

* 客户端可能已发送部分 POST 数据。
* 服务器写入 301 响应。
* 在完全读取上一个请求正文中的 POST 数据之前，不能将连接用于新请求。
* Kestrel 尝试排出请求正文。 排出请求正文意味着读取和丢弃数据，而不处理数据。

排出过程会在允许重新使用连接与排出任何剩余数据所用的时间之间进行权衡：

* 排出超时为五秒，该值不可配置。
* 如果 `Content-Length` 或 `Transfer-Encoding` 标头指定的所有数据在超时之前都未被读取，则连接将关闭。

有时，你可能想要在写入响应之前或之后立即终止请求。 例如，客户端可能具有限制性的数据上限。 限制上传的数据可能是一个优先事项。 在这种情况下，若要终止请求，请从控制器、Razor 页面或中间件调用 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)。

在调用 `Abort` 时，存在一些注意事项：

* 创建新连接可能会很慢且成本高昂。
* 在连接关闭之前无法保证客户端已读取响应。
* 应极少调用 `Abort`，并且应将此操作留给严重错误情况（而不是常见错误情况）。
  * 只有在需要解决特定问题时才调用 `Abort`。 例如，如果恶意客户端正在尝试 POST 数据或在客户端代码中存在导致大量或多个请求的 bug，则调用 `Abort`。
  * 不要为常见错误情况（如 HTTP 404（找不到））调用 `Abort`。

调用 `Abort` 之前调用 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) 可确保服务器已完成写入响应。 但是，客户端行为是不可预测的，在连接中止前它们可能未读取响应。

对于 HTTP/2，此过程又有所不同，因为协议支持在不关闭连接的情况下中止单个请求流。 五秒排出超时不适用。 如果在完成响应后存在任何未读的请求正文数据，则服务器会发送 HTTP/2 RST 帧。 其他请求正文数据帧将被忽略。

如果可能，客户端最好使用 [Expect:100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 请求标头，然后等到服务器响应后再开始发送请求正文。 这样，客户端便有机会在发送不需要的数据之前检查响应和中止。
