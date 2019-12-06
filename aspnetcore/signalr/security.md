---
title: ASP.NET Core 中的安全注意事项 SignalR
author: bradygaster
description: 了解如何在 ASP.NET Core SignalR中使用身份验证和授权。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: f443fe0fbaaa1facd09edc0878c048772895ecff
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881186"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="59c31-103">ASP.NET Core 中的安全注意事项 SignalR</span><span class="sxs-lookup"><span data-stu-id="59c31-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="59c31-104">作者： [Andrew Stanton](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="59c31-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="59c31-105">本文提供了有关保护 SignalR的信息。</span><span class="sxs-lookup"><span data-stu-id="59c31-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="59c31-106">跨域资源共享</span><span class="sxs-lookup"><span data-stu-id="59c31-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="59c31-107">[跨源资源共享（CORS）](https://www.w3.org/TR/cors/)可用于允许在浏览器中进行跨域 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="59c31-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="59c31-108">如果 JavaScript 代码托管在 SignalR 应用的另一个域中，则必须启用[CORS 中间件](xref:security/cors)才能使 JavaScript 连接到 SignalR 应用。</span><span class="sxs-lookup"><span data-stu-id="59c31-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="59c31-109">仅允许来自你信任或控制的域的跨域请求。</span><span class="sxs-lookup"><span data-stu-id="59c31-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="59c31-110">例如：</span><span class="sxs-lookup"><span data-stu-id="59c31-110">For example:</span></span>

* <span data-ttu-id="59c31-111">网站托管在 `http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="59c31-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="59c31-112">SignalR 应用承载于 `http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="59c31-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="59c31-113">应在 SignalR 应用程序中配置 CORS，以只允许源 `www.example.com`。</span><span class="sxs-lookup"><span data-stu-id="59c31-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="59c31-114">有关配置 CORS 的详细信息，请参阅[启用跨域请求（CORS）](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="59c31-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="59c31-115">**需要**以下 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="59c31-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="59c31-116">允许特定的预期来源。</span><span class="sxs-lookup"><span data-stu-id="59c31-116">Allow the specific expected origins.</span></span> <span data-ttu-id="59c31-117">允许任何来源是可行的，但不安全或**不**推荐使用。</span><span class="sxs-lookup"><span data-stu-id="59c31-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="59c31-118">必须允许 `GET` 和 `POST` 的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="59c31-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="59c31-119">必须启用凭据，即使在未使用身份验证时也是如此。</span><span class="sxs-lookup"><span data-stu-id="59c31-119">Credentials must be enabled, even when authentication is not used.</span></span>

<span data-ttu-id="59c31-120">例如，以下 CORS 策略允许 `https://example.com` 上承载的 SignalR 浏览器客户端访问 `https://signalr.example.com`上承载的 SignalR 应用程序：</span><span class="sxs-lookup"><span data-stu-id="59c31-120">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

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
> SignalR<span data-ttu-id="59c31-121"> 与 Azure App Service 中的内置 CORS 功能不兼容。</span><span class="sxs-lookup"><span data-stu-id="59c31-121"> is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="59c31-122">WebSocket 源限制</span><span class="sxs-lookup"><span data-stu-id="59c31-122">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="59c31-123">CORS 提供的保护不适用于 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="59c31-123">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="59c31-124">对于 Websocket 上的源限制，请阅读[websocket 源限制](xref:fundamentals/websockets#websocket-origin-restriction)。</span><span class="sxs-lookup"><span data-stu-id="59c31-124">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="59c31-125">CORS 提供的保护不适用于 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="59c31-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="59c31-126">浏览器不会：</span><span class="sxs-lookup"><span data-stu-id="59c31-126">Browsers do **not**:</span></span>

* <span data-ttu-id="59c31-127">执行 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="59c31-127">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="59c31-128">在发出 WebSocket 请求时，遵守 `Access-Control` 标头中指定的限制。</span><span class="sxs-lookup"><span data-stu-id="59c31-128">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="59c31-129">但是，浏览器在发出 WebSocket 请求时会发送 `Origin` 标头。</span><span class="sxs-lookup"><span data-stu-id="59c31-129">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="59c31-130">应将应用程序配置为验证这些标头，以确保只允许来自预期来源的 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="59c31-130">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="59c31-131">在 ASP.NET Core 2.1 及更高版本中，可以使用在 `UseSignalR`之前放置的自定义中间件和 `Configure`中的**身份验证中间**件来实现标题验证：</span><span class="sxs-lookup"><span data-stu-id="59c31-131">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="59c31-132">与 `Referer` 标头一样，`Origin` 标头由客户端控制，并可以伪造。</span><span class="sxs-lookup"><span data-stu-id="59c31-132">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="59c31-133">这些标头**不**应用作身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="59c31-133">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="access-token-logging"></a><span data-ttu-id="59c31-134">访问令牌日志记录</span><span class="sxs-lookup"><span data-stu-id="59c31-134">Access token logging</span></span>

<span data-ttu-id="59c31-135">使用 Websocket 或服务器发送事件时，浏览器客户端会在查询字符串中发送访问令牌。</span><span class="sxs-lookup"><span data-stu-id="59c31-135">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="59c31-136">通过查询字符串接收访问令牌通常与使用标准 `Authorization` 标题的安全性相同。</span><span class="sxs-lookup"><span data-stu-id="59c31-136">Receiving the access token via query string is generally as secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="59c31-137">应始终使用 HTTPS 来确保客户端与服务器之间的安全端到端连接。</span><span class="sxs-lookup"><span data-stu-id="59c31-137">You should always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="59c31-138">许多 web 服务器都记录每个请求的 URL，包括查询字符串。</span><span class="sxs-lookup"><span data-stu-id="59c31-138">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="59c31-139">记录 Url 可能会记录访问令牌。</span><span class="sxs-lookup"><span data-stu-id="59c31-139">Logging the URLs may log the access token.</span></span> <span data-ttu-id="59c31-140">默认情况下，ASP.NET Core 记录每个请求的 URL，其中将包括查询字符串。</span><span class="sxs-lookup"><span data-stu-id="59c31-140">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="59c31-141">例如：</span><span class="sxs-lookup"><span data-stu-id="59c31-141">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="59c31-142">如果你对将此数据与服务器日志进行日志记录有关，你可以通过将 `Microsoft.AspNetCore.Hosting` 记录器配置到 `Warning` 级别或更高级别来完全禁用此日志记录（这些消息在 `Info` 级别写入）。</span><span class="sxs-lookup"><span data-stu-id="59c31-142">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="59c31-143">有关详细信息，请参阅有关[日志筛选](xref:fundamentals/logging/index#log-filtering)的文档。</span><span class="sxs-lookup"><span data-stu-id="59c31-143">See the documentation on [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="59c31-144">如果仍想记录某些请求信息，可以[编写中间件](xref:fundamentals/middleware/write)来记录所需的数据，并筛选出 `access_token` 查询字符串值（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="59c31-144">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="59c31-145">异常</span><span class="sxs-lookup"><span data-stu-id="59c31-145">Exceptions</span></span>

<span data-ttu-id="59c31-146">异常消息通常被视为不应泄露给客户端的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="59c31-146">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="59c31-147">默认情况下，SignalR 不会将集线器方法引发的异常的详细信息发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="59c31-147">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="59c31-148">相反，客户端将收到一条指示出错的一般消息。</span><span class="sxs-lookup"><span data-stu-id="59c31-148">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="59c31-149">向客户端发送的异常消息可以通过[EnableDetailedErrors](xref:signalr/configuration#configure-server-options)重写（例如，在开发或测试中）。</span><span class="sxs-lookup"><span data-stu-id="59c31-149">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="59c31-150">不应在生产应用程序中向客户端公开异常消息。</span><span class="sxs-lookup"><span data-stu-id="59c31-150">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="59c31-151">缓冲区管理</span><span class="sxs-lookup"><span data-stu-id="59c31-151">Buffer management</span></span>

SignalR<span data-ttu-id="59c31-152"> 使用每个连接的缓冲区来管理传入消息和传出消息。</span><span class="sxs-lookup"><span data-stu-id="59c31-152"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="59c31-153">默认情况下，SignalR 会将这些缓冲区限制为 32 KB。</span><span class="sxs-lookup"><span data-stu-id="59c31-153">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="59c31-154">客户端或服务器可以发送的最大消息为 32 KB。</span><span class="sxs-lookup"><span data-stu-id="59c31-154">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="59c31-155">消息连接使用的最大内存为 32 KB。</span><span class="sxs-lookup"><span data-stu-id="59c31-155">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="59c31-156">如果消息始终小于 32 KB，则可以减少限制：</span><span class="sxs-lookup"><span data-stu-id="59c31-156">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="59c31-157">禁止客户端发送更大的消息。</span><span class="sxs-lookup"><span data-stu-id="59c31-157">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="59c31-158">服务器绝不会需要分配大型缓冲区来接受消息。</span><span class="sxs-lookup"><span data-stu-id="59c31-158">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="59c31-159">如果消息大小超过 32 KB，则可以增加限制。</span><span class="sxs-lookup"><span data-stu-id="59c31-159">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="59c31-160">增加此限制意味着：</span><span class="sxs-lookup"><span data-stu-id="59c31-160">Increasing this limit means:</span></span>

* <span data-ttu-id="59c31-161">客户端可能会导致服务器分配大的内存缓冲区。</span><span class="sxs-lookup"><span data-stu-id="59c31-161">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="59c31-162">大缓冲区的服务器分配可能会减少并发连接的数量。</span><span class="sxs-lookup"><span data-stu-id="59c31-162">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="59c31-163">传入消息和传出消息有限制，可以在 `MapHub`中配置的[HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options)对象上进行配置：</span><span class="sxs-lookup"><span data-stu-id="59c31-163">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="59c31-164">`ApplicationMaxBufferSize` 表示从客户端缓冲的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="59c31-164">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="59c31-165">如果客户端尝试发送比此限制更大的消息，则可能会关闭该连接。</span><span class="sxs-lookup"><span data-stu-id="59c31-165">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="59c31-166">`TransportMaxBufferSize` 表示服务器可以发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="59c31-166">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="59c31-167">如果服务器尝试发送的消息（包括从集线器方法返回的值）大于此限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="59c31-167">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="59c31-168">将限制设置为 `0` 会禁用限制。</span><span class="sxs-lookup"><span data-stu-id="59c31-168">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="59c31-169">通过删除该限制，客户端可以发送任意大小的消息。</span><span class="sxs-lookup"><span data-stu-id="59c31-169">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="59c31-170">发送大消息的恶意客户端可能会导致分配额外的内存。</span><span class="sxs-lookup"><span data-stu-id="59c31-170">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="59c31-171">过度使用内存会显著减少并发连接数。</span><span class="sxs-lookup"><span data-stu-id="59c31-171">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
