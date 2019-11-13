---
title: ASP.NET Core 中的安全注意事项 SignalR
author: bradygaster
description: 了解如何在 ASP.NET Core SignalR中使用身份验证和授权。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: c5a34ae67bdfb8f7fd92c00f18973b66b685a99c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963904"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a>ASP.NET Core 中的安全注意事项 SignalR

作者： [Andrew Stanton](https://twitter.com/anurse)

本文提供了有关保护 SignalR的信息。

## <a name="cross-origin-resource-sharing"></a>跨域资源共享

[跨源资源共享（CORS）](https://www.w3.org/TR/cors/)可用于允许在浏览器中进行跨域 SignalR 连接。 如果 JavaScript 代码托管在 SignalR 应用的另一个域中，则必须启用[CORS 中间件](xref:security/cors)才能使 JavaScript 连接到 SignalR 应用。 仅允许来自你信任或控制的域的跨域请求。 例如:

* 网站托管在 `http://www.example.com`
* SignalR 应用承载于 `http://signalr.example.com`

应在 SignalR 应用程序中配置 CORS，以只允许源 `www.example.com`。

有关配置 CORS 的详细信息，请参阅[启用跨域请求（CORS）](xref:security/cors)。 SignalR**需要**以下 CORS 策略：

* 允许特定的预期来源。 允许任何来源是可行的，但不安全或**不**推荐使用。
* 必须允许 `GET` 和 `POST` 的 HTTP 方法。
* 必须启用凭据，即使在未使用身份验证时也是如此。

例如，以下 CORS 策略允许 `https://example.com` 上承载的 SignalR 浏览器客户端访问 `https://signalr.example.com`上承载的 SignalR 应用程序：

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> SignalR 与 Azure App Service 中的内置 CORS 功能不兼容。

## <a name="websocket-origin-restriction"></a>WebSocket 源限制

::: moniker range=">= aspnetcore-2.2"

CORS 提供的保护不适用于 WebSocket。 对于 Websocket 上的源限制，请阅读[websocket 源限制](xref:fundamentals/websockets#websocket-origin-restriction)。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS 提供的保护不适用于 WebSocket。 浏览器不会：

* 执行 CORS 预检请求。
* 在发出 WebSocket 请求时，遵守 `Access-Control` 标头中指定的限制。

但是，浏览器在发出 WebSocket 请求时会发送 `Origin` 标头。 应将应用程序配置为验证这些标头，以确保只允许来自预期来源的 WebSocket。

在 ASP.NET Core 2.1 及更高版本中，可以使用在 `UseSignalR`之前放置的自定义中间件和 `Configure`中的**身份验证中间**件来实现标题验证：

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> 与 `Referer` 标头一样，`Origin` 标头由客户端控制，并可以伪造。 这些标头**不**应用作身份验证机制。

::: moniker-end

## <a name="access-token-logging"></a>访问令牌日志记录

使用 Websocket 或服务器发送事件时，浏览器客户端会在查询字符串中发送访问令牌。 通过查询字符串接收访问令牌通常与使用标准 `Authorization` 标题的安全性相同。 应始终使用 HTTPS 来确保客户端与服务器之间的安全端到端连接。 许多 web 服务器都记录每个请求的 URL，包括查询字符串。 记录 Url 可能会记录访问令牌。 默认情况下，ASP.NET Core 记录每个请求的 URL，其中将包括查询字符串。 例如:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

如果你对将此数据与服务器日志进行日志记录有关，你可以通过将 `Microsoft.AspNetCore.Hosting` 记录器配置到 `Warning` 级别或更高级别来完全禁用此日志记录（这些消息在 `Info` 级别写入）。 有关详细信息，请参阅有关[日志筛选](xref:fundamentals/logging/index#log-filtering)的文档。 如果仍想记录某些请求信息，可以[编写中间件](xref:fundamentals/middleware/write)来记录所需的数据，并筛选出 `access_token` 查询字符串值（如果存在）。

## <a name="exceptions"></a>异常

异常消息通常被视为不应泄露给客户端的敏感数据。 默认情况下，SignalR 不会将集线器方法引发的异常的详细信息发送到客户端。 相反，客户端将收到一条指示出错的一般消息。 可以通过[`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options)重写到客户端的异常消息传递（例如，在开发或测试中）。 不应在生产应用程序中向客户端公开异常消息。

## <a name="buffer-management"></a>缓冲区管理

SignalR 使用每个连接的缓冲区来管理传入消息和传出消息。 默认情况下，SignalR 会将这些缓冲区限制为 32 KB。 客户端或服务器可以发送的最大消息为 32 KB。 消息连接使用的最大内存为 32 KB。 如果消息始终小于 32 KB，则可以减少限制：

* 禁止客户端发送更大的消息。
* 服务器绝不会需要分配大型缓冲区来接受消息。

如果消息大小超过 32 KB，则可以增加限制。 增加此限制意味着：

* 客户端可能会导致服务器分配大的内存缓冲区。
* 大缓冲区的服务器分配可能会减少并发连接的数量。

传入消息和传出消息有限制，可在 `MapHub`中配置的[`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options)对象上进行配置：

* `ApplicationMaxBufferSize` 表示从客户端缓冲的最大字节数。 如果客户端尝试发送比此限制更大的消息，则可能会关闭该连接。
* `TransportMaxBufferSize` 表示服务器可以发送的最大字节数。 如果服务器尝试发送的消息（包括从集线器方法返回的值）大于此限制，则会引发异常。

将限制设置为 `0` 会禁用限制。 通过删除该限制，客户端可以发送任意大小的消息。 发送大消息的恶意客户端可能会导致分配额外的内存。 过度使用内存会显著减少并发连接数。
