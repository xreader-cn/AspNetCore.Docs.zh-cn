---
title: 'ASP.NET Core :::no-loc(SignalR)::: 连接故障排除'
author: bradygaster
description: 'ASP.NET Core :::no-loc(SignalR)::: 连接故障排除。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059606"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="10c48-103">排查连接错误</span><span class="sxs-lookup"><span data-stu-id="10c48-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="10c48-104">本部分提供有关在尝试建立与 ASP.NET Core 集线器的连接时可能出现的错误的帮助 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="10c48-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core :::no-loc(SignalR)::: hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="10c48-105">响应代码404</span><span class="sxs-lookup"><span data-stu-id="10c48-105">Response code 404</span></span>

<span data-ttu-id="10c48-106">使用 Websocket 和时 `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="10c48-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="10c48-107">如果在没有粘滞会话的情况下使用多台服务器，则可以在一台服务器上启动连接，然后再切换到另一台服务器。</span><span class="sxs-lookup"><span data-stu-id="10c48-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="10c48-108">其他服务器无法识别上一个连接。</span><span class="sxs-lookup"><span data-stu-id="10c48-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="10c48-109">验证客户端是否正在连接到正确的终结点。</span><span class="sxs-lookup"><span data-stu-id="10c48-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="10c48-110">例如，服务器承载于 `http://127.0.0.1:5000/hub/myHub` 并且客户端正在尝试连接到 `http://127.0.0.1:5000/myHub` 。</span><span class="sxs-lookup"><span data-stu-id="10c48-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="10c48-111">如果连接使用该 ID，并且在协商后将请求发送到服务器的时间过长，则服务器会执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="10c48-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="10c48-112">删除 ID。</span><span class="sxs-lookup"><span data-stu-id="10c48-112">Deletes the ID.</span></span>
  * <span data-ttu-id="10c48-113">返回404。</span><span class="sxs-lookup"><span data-stu-id="10c48-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="10c48-114">响应代码400或503</span><span class="sxs-lookup"><span data-stu-id="10c48-114">Response code 400 or 503</span></span>

<span data-ttu-id="10c48-115">对于以下错误：</span><span class="sxs-lookup"><span data-stu-id="10c48-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="10c48-116">此错误通常是由仅使用 Websocket 传输的客户端引起的，但该服务器上未启用 Websocket 协议。</span><span class="sxs-lookup"><span data-stu-id="10c48-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="10c48-117">响应代码307</span><span class="sxs-lookup"><span data-stu-id="10c48-117">Response code 307</span></span>

<span data-ttu-id="10c48-118">使用 Websocket 和时 `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="10c48-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="10c48-119">协商请求期间也会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="10c48-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="10c48-120">常见原因：</span><span class="sxs-lookup"><span data-stu-id="10c48-120">Common cause:</span></span>
* <span data-ttu-id="10c48-121">应用配置为通过调用来强制使用 `UseHttpsRedirection` https `Startup` ，或通过 URL 重写规则强制使用 https。</span><span class="sxs-lookup"><span data-stu-id="10c48-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="10c48-122">可能的解决方案：</span><span class="sxs-lookup"><span data-stu-id="10c48-122">Possible solution:</span></span>
* <span data-ttu-id="10c48-123">将客户端上的 URL 从 "http" 更改为 "https"。</span><span class="sxs-lookup"><span data-stu-id="10c48-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="10c48-124">响应代码405</span><span class="sxs-lookup"><span data-stu-id="10c48-124">Response code 405</span></span>

<span data-ttu-id="10c48-125">Http 状态代码 405-不允许的方法</span><span class="sxs-lookup"><span data-stu-id="10c48-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="10c48-126">应用未启用[CORS](xref:signalr/security#cross-origin-resource-sharing)</span><span class="sxs-lookup"><span data-stu-id="10c48-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="10c48-127">响应代码为0</span><span class="sxs-lookup"><span data-stu-id="10c48-127">Response code 0</span></span>

<span data-ttu-id="10c48-128">Http 状态代码 0-通常为 [CORS](xref:signalr/security#cross-origin-resource-sharing) 问题，不提供状态代码</span><span class="sxs-lookup"><span data-stu-id="10c48-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="10c48-129">将预期来源添加到 `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="10c48-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="10c48-130">添加 `.AllowCredentials()` 到你的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="10c48-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="10c48-131">不能将 `.AllowAnyOrigin()` 或 `.WithOrigins("*")` 与此选项一起使用</span><span class="sxs-lookup"><span data-stu-id="10c48-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="10c48-132">响应代码413</span><span class="sxs-lookup"><span data-stu-id="10c48-132">Response code 413</span></span>

<span data-ttu-id="10c48-133">Http 状态代码 413-有效负载太大</span><span class="sxs-lookup"><span data-stu-id="10c48-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="10c48-134">这通常是由于访问令牌超过4k 而导致的。</span><span class="sxs-lookup"><span data-stu-id="10c48-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="10c48-135">如果使用 Azure :::no-loc(SignalR)::: 服务，请通过自定义通过服务发送的声明来减小令牌大小，方法是：</span><span class="sxs-lookup"><span data-stu-id="10c48-135">If using the Azure :::no-loc(SignalR)::: Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzure:::no-loc(SignalR):::(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="10c48-136">暂时性网络故障</span><span class="sxs-lookup"><span data-stu-id="10c48-136">Transient network failures</span></span>

<span data-ttu-id="10c48-137">暂时性网络故障可能会关闭 :::no-loc(SignalR)::: 连接。</span><span class="sxs-lookup"><span data-stu-id="10c48-137">Transient network failures may close the :::no-loc(SignalR)::: connection.</span></span> <span data-ttu-id="10c48-138">服务器可以将关闭的连接解释为正常的客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="10c48-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="10c48-139">若要获取有关在这些情况下客户端断开连接的原因的详细信息 [，请从客户端和服务器收集日志](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="10c48-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>