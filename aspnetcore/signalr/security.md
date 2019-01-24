---
title: ASP.NET Core SignalR 中的安全注意事项
author: bradygaster
description: 了解如何在 ASP.NET Core SignalR 中使用身份验证和授权。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: 52cfac6be8e61572acdf0b19dab574b607314d97
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836059"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>ASP.NET Core SignalR 中的安全注意事项

通过[Andrew Stanton-nurse](https://twitter.com/anurse)

本文提供有关保护 SignalR 的信息。

## <a name="cross-origin-resource-sharing"></a>跨域资源共享

[跨域资源共享 (CORS)](https://www.w3.org/TR/cors/)可用于在浏览器中允许跨域 SignalR 连接。 如果在不同的域从 SignalR 应用程序中托管的 JavaScript 代码[CORS 中间件](xref:security/cors)必须启用才能允许 JavaScript 才能连接到 SignalR 应用程序。 允许仅从您信任的域或控件的跨域请求。 例如：

* 上托管你的站点 `http://www.example.com`
* 上托管您的 SignalR 应用程序 `http://signalr.example.com`

应为只允许原点的 SignalR 应用中配置 CORS `www.example.com`。

配置 CORS 的详细信息，请参阅[启用跨域请求 (CORS)](xref:security/cors)。 SignalR**需要**以下 CORS 策略：

* 允许特定的预期的来源。 允许任何源，但**不**安全或建议。
* HTTP 方法`GET`和`POST`必须允许。
* 必须启用凭据，即使不使用身份验证。

例如，以下的 CORS 策略允许 SignalR 浏览器客户端上托管`https://example.com`访问上托管的 SignalR 应用`https://signalr.example.com`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

> [!NOTE]
> SignalR 是不与 Azure 应用服务中内置的 CORS 功能兼容。

## <a name="websocket-origin-restriction"></a>WebSocket Origin Restriction

::: moniker range=">= aspnetcore-2.2"

CORS 提供的保护不适用于 WebSocket。 有关源限制对 Websocket，请阅读[Websocket 原点限制](xref:fundamentals/websockets#websocket-origin-restriction)。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS 提供的保护不适用于 WebSocket。 浏览器不会：

* 执行 CORS 预检请求。
* 在发出 WebSocket 请求时，遵守 `Access-Control` 标头中指定的限制。

但是，浏览器在发出 WebSocket 请求时会发送 `Origin` 标头。 应将应用程序配置为验证这些标头，以确保只允许来自预期来源的 WebSocket。

ASP.NET Core 2.1 及更高版本，可以使用放置自定义中间件实现标头验证**之前`UseSignalR`，和身份验证中间件**中`Configure`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> 与 `Referer` 标头一样，`Origin` 标头由客户端控制，并可以伪造。 这些标头应**不**用作身份验证机制。

::: moniker-end

## <a name="access-token-logging"></a>访问令牌的日志记录

使用 Websocket 或服务器发送事件时，浏览器客户端将查询字符串中发送的访问令牌。 接收访问令牌通过查询字符串就象通常那样使用标准安全`Authorization`标头。 应始终使用 HTTPS 以确保客户端和服务器之间的端到端安全连接。 许多 web 服务器日志每个请求，包括查询字符串的 URL。 日志记录 Url 可能记录的访问令牌。 ASP.NET Core 日志默认情况下，其中会包括查询字符串的每个请求的 URL。 例如：

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

如果您有关于此数据与服务器日志的日志记录的相关问题，可以通过配置来完全禁用此日志记录`Microsoft.AspNetCore.Hosting`记录器`Warning`级别或更高版本 (这些消息将写入在`Info`级别)。 在查看文档[日志筛选](xref:fundamentals/logging/index#log-filtering)有关详细信息。 如果你仍想要记录特定请求的信息，可以[编写中间件](xref:fundamentals/middleware/index#write-middleware)来记录的数据需要并筛选出`access_token`查询字符串值 （如果存在）。

## <a name="exceptions"></a>Exceptions

异常消息通常被视为不应泄露给客户端的敏感数据。 默认情况下，SignalR 不会发送到客户端集线器方法引发的异常的详细信息。 相反，客户端收到一条指示发生了错误的常规消息。 异常消息传递到客户端可以重写 （例如在开发或测试） 使用[ `EnableDetailedErrors` ](xref:signalr/configuration#configure-server-options)。 不应在生产应用中的客户端公开异常消息。

## <a name="buffer-management"></a>缓冲区管理

SignalR 使用每个连接缓冲区来管理传入和传出消息。 默认情况下，SignalR 限制为 32 KB 这些缓冲区。 客户端或服务器可以发送的最大消息为 32 KB。 消息的连接所占用的最大内存为 32 KB。 如果你的消息始终小于 32 KB，可以减少了限制，其中：

* 防止客户端发送大消息。
* 服务器永远不需要分配大型缓冲区接受的消息。

如果你的消息大小大于 32 KB，则可以增加限制。 增加此限制意味着：

* 客户端可能导致服务器分配较大内存缓冲区。
* 大型缓冲区的服务器分配可能会降低并发连接数。

限制传入和传出消息，同时可以根据[ `HttpConnectionDispatcherOptions` ](xref:signalr/configuration#configure-server-options)对象中配置`MapHub`:

* `ApplicationMaxBufferSize` 表示最大字节数从客户端的服务器缓冲区。 如果客户端尝试发送一条消息大小超过此限制，可能会关闭连接。
* `TransportMaxBufferSize` 表示最大服务器都可以发送的字节数。 如果服务器尝试发送一条消息 （包括从中心的方法返回的值） 大于此限制，将引发异常。

将限制设置为`0`禁用限制。 删除限制允许客户端发送任意大小的消息。 恶意客户端发送大消息可能会导致过多内存，并将其分配。 过多内存使用情况可以显著减少并发连接数。
