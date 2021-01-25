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
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="4c5cf-103">对 ASP.NET Core Kestrel Web 服务器使用请求排出</span><span class="sxs-lookup"><span data-stu-id="4c5cf-103">Request draining with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="4c5cf-104">打开 HTTP 连接非常耗时。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-104">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="4c5cf-105">对于 HTTPS 而言，这也是资源密集型。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-105">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="4c5cf-106">因此，Kestrel 会尝试按 HTTP/1.1 协议重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-106">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="4c5cf-107">请求正文必须完全使用才能允许重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-107">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="4c5cf-108">应用不会始终使用请求正文，例如 HTTP POST 请求，其中服务器返回重定向或 404 响应。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-108">The app doesn't always consume the request body, such as HTTP POST requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="4c5cf-109">在 HTTP POST 重定向的情况下：</span><span class="sxs-lookup"><span data-stu-id="4c5cf-109">In the HTTP POST redirect case:</span></span>

* <span data-ttu-id="4c5cf-110">客户端可能已发送部分 POST 数据。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-110">The client may already have sent part of the POST data.</span></span>
* <span data-ttu-id="4c5cf-111">服务器写入 301 响应。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-111">The server writes the 301 response.</span></span>
* <span data-ttu-id="4c5cf-112">在完全读取上一个请求正文中的 POST 数据之前，不能将连接用于新请求。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-112">The connection can't be used for a new request until the POST data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="4c5cf-113">Kestrel 尝试排出请求正文。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-113">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="4c5cf-114">排出请求正文意味着读取和丢弃数据，而不处理数据。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-114">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="4c5cf-115">排出过程会在允许重新使用连接与排出任何剩余数据所用的时间之间进行权衡：</span><span class="sxs-lookup"><span data-stu-id="4c5cf-115">The draining process makes a tradeoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="4c5cf-116">排出超时为五秒，该值不可配置。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-116">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="4c5cf-117">如果 `Content-Length` 或 `Transfer-Encoding` 标头指定的所有数据在超时之前都未被读取，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-117">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="4c5cf-118">有时，你可能想要在写入响应之前或之后立即终止请求。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-118">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="4c5cf-119">例如，客户端可能具有限制性的数据上限。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-119">For example, clients may have restrictive data caps.</span></span> <span data-ttu-id="4c5cf-120">限制上传的数据可能是一个优先事项。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-120">Limiting uploaded data might be a priority.</span></span> <span data-ttu-id="4c5cf-121">在这种情况下，若要终止请求，请从控制器、Razor 页面或中间件调用 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-121">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="4c5cf-122">在调用 `Abort` 时，存在一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="4c5cf-122">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="4c5cf-123">创建新连接可能会很慢且成本高昂。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-123">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="4c5cf-124">在连接关闭之前无法保证客户端已读取响应。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-124">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="4c5cf-125">应极少调用 `Abort`，并且应将此操作留给严重错误情况（而不是常见错误情况）。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-125">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="4c5cf-126">只有在需要解决特定问题时才调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-126">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="4c5cf-127">例如，如果恶意客户端正在尝试 POST 数据或在客户端代码中存在导致大量或多个请求的 bug，则调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-127">For example, call `Abort` if malicious clients are trying to POST data or when there's a bug in client code that causes large or several requests.</span></span>
  * <span data-ttu-id="4c5cf-128">不要为常见错误情况（如 HTTP 404（找不到））调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-128">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="4c5cf-129">调用 `Abort` 之前调用 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) 可确保服务器已完成写入响应。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-129">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="4c5cf-130">但是，客户端行为是不可预测的，在连接中止前它们可能未读取响应。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-130">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="4c5cf-131">对于 HTTP/2，此过程又有所不同，因为协议支持在不关闭连接的情况下中止单个请求流。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-131">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="4c5cf-132">五秒排出超时不适用。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-132">The five-second drain timeout doesn't apply.</span></span> <span data-ttu-id="4c5cf-133">如果在完成响应后存在任何未读的请求正文数据，则服务器会发送 HTTP/2 RST 帧。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-133">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="4c5cf-134">其他请求正文数据帧将被忽略。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-134">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="4c5cf-135">如果可能，客户端最好使用 [Expect:100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 请求标头，然后等到服务器响应后再开始发送请求正文。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-135">If possible, it's better for clients to use the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="4c5cf-136">这样，客户端便有机会在发送不需要的数据之前检查响应和中止。</span><span class="sxs-lookup"><span data-stu-id="4c5cf-136">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>
