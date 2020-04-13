---
title: 比较 gRPC 服务和 HTTP API
author: jamesnk
description: 了解如何比较 gRPC 和 HTTP API 以及建议方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 2dff64f1f2d67b8a1e676acf6cf131b684099750
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405871"
---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="1e18f-103">比较 gRPC 服务和 HTTP API</span><span class="sxs-lookup"><span data-stu-id="1e18f-103">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="1e18f-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1e18f-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="1e18f-105">本文介绍如何将 [gRPC 服务](https://grpc.io/docs/guides/)与 HTTP API（包括 ASP.NET Core [Web API](xref:web-api/index)）进行比较。</span><span class="sxs-lookup"><span data-stu-id="1e18f-105">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="1e18f-106">用于为应用提供 API 的技术是一个重要选择，与 HTTP API 相比，gRPC 提供独特优势。</span><span class="sxs-lookup"><span data-stu-id="1e18f-106">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="1e18f-107">本文讨论 gRPC 的优点和缺点，并提供优先于其他技术选择使用 gRPC 的建议方案。</span><span class="sxs-lookup"><span data-stu-id="1e18f-107">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="1e18f-108">概括比较</span><span class="sxs-lookup"><span data-stu-id="1e18f-108">High-level comparison</span></span>

<span data-ttu-id="1e18f-109">下表对 gRPC 和具有 JSON 的 HTTP API 之间的功能进行了简单比较。</span><span class="sxs-lookup"><span data-stu-id="1e18f-109">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="1e18f-110">功能</span><span class="sxs-lookup"><span data-stu-id="1e18f-110">Feature</span></span>          | <span data-ttu-id="1e18f-111">gRPC</span><span class="sxs-lookup"><span data-stu-id="1e18f-111">gRPC</span></span>                                               | <span data-ttu-id="1e18f-112">具有 JSON 的 HTTP API</span><span class="sxs-lookup"><span data-stu-id="1e18f-112">HTTP APIs with JSON</span></span>           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| <span data-ttu-id="1e18f-113">协定</span><span class="sxs-lookup"><span data-stu-id="1e18f-113">Contract</span></span>         | <span data-ttu-id="1e18f-114">必需 ( *.proto*)</span><span class="sxs-lookup"><span data-stu-id="1e18f-114">Required (*.proto*)</span></span>                                | <span data-ttu-id="1e18f-115">可选 (OpenAPI)</span><span class="sxs-lookup"><span data-stu-id="1e18f-115">Optional (OpenAPI)</span></span>            |
| <span data-ttu-id="1e18f-116">协议</span><span class="sxs-lookup"><span data-stu-id="1e18f-116">Protocol</span></span>         | <span data-ttu-id="1e18f-117">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="1e18f-117">HTTP/2</span></span>                                             | <span data-ttu-id="1e18f-118">HTTP</span><span class="sxs-lookup"><span data-stu-id="1e18f-118">HTTP</span></span>                          |
| <span data-ttu-id="1e18f-119">Payload</span><span class="sxs-lookup"><span data-stu-id="1e18f-119">Payload</span></span>          | [<span data-ttu-id="1e18f-120">Protobuf（小型，二进制）</span><span class="sxs-lookup"><span data-stu-id="1e18f-120">Protobuf (small, binary)</span></span>](#performance)           | <span data-ttu-id="1e18f-121">JSON（大型，人工可读取）</span><span class="sxs-lookup"><span data-stu-id="1e18f-121">JSON (large, human readable)</span></span>  |
| <span data-ttu-id="1e18f-122">规定性</span><span class="sxs-lookup"><span data-stu-id="1e18f-122">Prescriptiveness</span></span> | [<span data-ttu-id="1e18f-123">严格规范</span><span class="sxs-lookup"><span data-stu-id="1e18f-123">Strict specification</span></span>](#strict-specification)      | <span data-ttu-id="1e18f-124">宽松。</span><span class="sxs-lookup"><span data-stu-id="1e18f-124">Loose.</span></span> <span data-ttu-id="1e18f-125">任何 HTTP 均有效。</span><span class="sxs-lookup"><span data-stu-id="1e18f-125">Any HTTP is valid.</span></span>     |
| <span data-ttu-id="1e18f-126">流式处理</span><span class="sxs-lookup"><span data-stu-id="1e18f-126">Streaming</span></span>        | [<span data-ttu-id="1e18f-127">客户端、服务器，双向</span><span class="sxs-lookup"><span data-stu-id="1e18f-127">Client, server, bi-directional</span></span>](#streaming)       | <span data-ttu-id="1e18f-128">客户端、服务器</span><span class="sxs-lookup"><span data-stu-id="1e18f-128">Client, server</span></span>                |
| <span data-ttu-id="1e18f-129">浏览器支持</span><span class="sxs-lookup"><span data-stu-id="1e18f-129">Browser support</span></span>  | [<span data-ttu-id="1e18f-130">无（需要 grpc-web）</span><span class="sxs-lookup"><span data-stu-id="1e18f-130">No (requires grpc-web)</span></span>](#limited-browser-support) | <span data-ttu-id="1e18f-131">是</span><span class="sxs-lookup"><span data-stu-id="1e18f-131">Yes</span></span>                           |
| <span data-ttu-id="1e18f-132">安全性</span><span class="sxs-lookup"><span data-stu-id="1e18f-132">Security</span></span>         | <span data-ttu-id="1e18f-133">传输 (TLS)</span><span class="sxs-lookup"><span data-stu-id="1e18f-133">Transport (TLS)</span></span>                                    | <span data-ttu-id="1e18f-134">传输 (TLS)</span><span class="sxs-lookup"><span data-stu-id="1e18f-134">Transport (TLS)</span></span>               |
| <span data-ttu-id="1e18f-135">客户端代码生成</span><span class="sxs-lookup"><span data-stu-id="1e18f-135">Client code-generation</span></span> | [<span data-ttu-id="1e18f-136">是</span><span class="sxs-lookup"><span data-stu-id="1e18f-136">Yes</span></span>](#code-generation)                      | <span data-ttu-id="1e18f-137">OpenAPI + 第三方工具</span><span class="sxs-lookup"><span data-stu-id="1e18f-137">OpenAPI + third-party tooling</span></span> |

## <a name="grpc-strengths"></a><span data-ttu-id="1e18f-138">gRPC 优点</span><span class="sxs-lookup"><span data-stu-id="1e18f-138">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="1e18f-139">性能</span><span class="sxs-lookup"><span data-stu-id="1e18f-139">Performance</span></span>

<span data-ttu-id="1e18f-140">gRPC 消息使用 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)（一种高效的二进制消息格式）进行序列化。</span><span class="sxs-lookup"><span data-stu-id="1e18f-140">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="1e18f-141">Protobuf 在服务器和客户端上可以非常快速地序列化。</span><span class="sxs-lookup"><span data-stu-id="1e18f-141">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="1e18f-142">Protobuf 序列化产生的有效负载较小，这在移动应用等带宽有限的方案中很重要。</span><span class="sxs-lookup"><span data-stu-id="1e18f-142">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="1e18f-143">gRPC 专为 HTTP/2（HTTP 的主要版本）而设计，与 HTTP 1.x 相比，HTTP/2 具有巨大性能优势：</span><span class="sxs-lookup"><span data-stu-id="1e18f-143">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="1e18f-144">二进制组帧和压缩。</span><span class="sxs-lookup"><span data-stu-id="1e18f-144">Binary framing and compression.</span></span> <span data-ttu-id="1e18f-145">HTTP/2 协议在发送和接收方面均紧凑且高效。</span><span class="sxs-lookup"><span data-stu-id="1e18f-145">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="1e18f-146">在单个 TCP 连接上多路复用多个 HTTP/2 调用。</span><span class="sxs-lookup"><span data-stu-id="1e18f-146">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="1e18f-147">多路复用可消除[队头阻塞](https://en.wikipedia.org/wiki/Head-of-line_blocking)。</span><span class="sxs-lookup"><span data-stu-id="1e18f-147">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

### <a name="code-generation"></a><span data-ttu-id="1e18f-148">代码生成</span><span class="sxs-lookup"><span data-stu-id="1e18f-148">Code generation</span></span>

<span data-ttu-id="1e18f-149">所有 gRPC 框架都为代码生成提供一流支持。</span><span class="sxs-lookup"><span data-stu-id="1e18f-149">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="1e18f-150">[.proto 文件](https://developers.google.com/protocol-buffers/docs/proto3)是 gRPC 开发的核心文件，它定义 gRPC 服务和消息的协定。</span><span class="sxs-lookup"><span data-stu-id="1e18f-150">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="1e18f-151">通过此文件，gRPC 框架编码生成服务基类、消息和完整的客户端。</span><span class="sxs-lookup"><span data-stu-id="1e18f-151">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="1e18f-152">通过在服务器和客户端之间共享 *.proto* 文件，可以端到端生成消息和客户端代码。</span><span class="sxs-lookup"><span data-stu-id="1e18f-152">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="1e18f-153">客户端的代码生成消除了客户端和服务器上的消息重复，并为你创建强类型客户端。</span><span class="sxs-lookup"><span data-stu-id="1e18f-153">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="1e18f-154">无需编写客户端可在具有许多服务的应用程序中节省大量开发时间。</span><span class="sxs-lookup"><span data-stu-id="1e18f-154">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="1e18f-155">严格规范</span><span class="sxs-lookup"><span data-stu-id="1e18f-155">Strict specification</span></span>

<span data-ttu-id="1e18f-156">具有 JSON 的 HTTP API 没有正式规范。</span><span class="sxs-lookup"><span data-stu-id="1e18f-156">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="1e18f-157">开发人员为 URL、HTTP 谓词和响应代码的最佳格式争论不休。</span><span class="sxs-lookup"><span data-stu-id="1e18f-157">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="1e18f-158">[gRPC 规范](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)对 gRPC 服务必须遵循的格式进行了规定。</span><span class="sxs-lookup"><span data-stu-id="1e18f-158">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="1e18f-159">gRPC 消除了争论并为开发人员节省了时间，因为 gRPC 在各个平台和实现中都是一致的。</span><span class="sxs-lookup"><span data-stu-id="1e18f-159">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="1e18f-160">流式处理</span><span class="sxs-lookup"><span data-stu-id="1e18f-160">Streaming</span></span>

<span data-ttu-id="1e18f-161">HTTP/2 为长期实时通信流提供基础。</span><span class="sxs-lookup"><span data-stu-id="1e18f-161">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="1e18f-162">gRPC 为通过 HTTP/2 进行流式传输提供一流支持。</span><span class="sxs-lookup"><span data-stu-id="1e18f-162">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="1e18f-163">gRPC 服务支持所有流式传输组合：</span><span class="sxs-lookup"><span data-stu-id="1e18f-163">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="1e18f-164">一元（无流式传输）</span><span class="sxs-lookup"><span data-stu-id="1e18f-164">Unary (no streaming)</span></span>
* <span data-ttu-id="1e18f-165">服务器到客户端流式传输</span><span class="sxs-lookup"><span data-stu-id="1e18f-165">Server to client streaming</span></span>
* <span data-ttu-id="1e18f-166">客户端到服务器流式传输</span><span class="sxs-lookup"><span data-stu-id="1e18f-166">Client to server streaming</span></span>
* <span data-ttu-id="1e18f-167">双向流式传输</span><span class="sxs-lookup"><span data-stu-id="1e18f-167">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="1e18f-168">截止时间/超时和取消</span><span class="sxs-lookup"><span data-stu-id="1e18f-168">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="1e18f-169">gRPC 允许客户端指定其愿意等待 RPC 完成的时间期限。</span><span class="sxs-lookup"><span data-stu-id="1e18f-169">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="1e18f-170">[截止时间](https://grpc.io/blog/deadlines)会发送到服务器，如果超过截止时间，服务器可以决定要执行的操作。</span><span class="sxs-lookup"><span data-stu-id="1e18f-170">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="1e18f-171">例如，服务器可能会在超时后取消正在进行的 gRPC/HTTP/数据库请求。</span><span class="sxs-lookup"><span data-stu-id="1e18f-171">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="1e18f-172">通过 gRPC 子调用传播截止时间和取消有助于强制执行资源使用限制。</span><span class="sxs-lookup"><span data-stu-id="1e18f-172">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="1e18f-173">gRPC 建议方案</span><span class="sxs-lookup"><span data-stu-id="1e18f-173">gRPC recommended scenarios</span></span>

<span data-ttu-id="1e18f-174">gRPC 非常适合以下方案：</span><span class="sxs-lookup"><span data-stu-id="1e18f-174">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="1e18f-175">**微服务** &ndash; gRPC 设计用于低延迟和高吞吐量通信。</span><span class="sxs-lookup"><span data-stu-id="1e18f-175">**Microservices** &ndash; gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="1e18f-176">gRPC 对于效率至关重要的轻量级微服务非常有用。</span><span class="sxs-lookup"><span data-stu-id="1e18f-176">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="1e18f-177">**点对点实时通信** &ndash; gRPC 对双向流式传输提供出色的支持。</span><span class="sxs-lookup"><span data-stu-id="1e18f-177">**Point-to-point real-time communication** &ndash; gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="1e18f-178">gRPC 服务可以实时推送消息而无需轮询。</span><span class="sxs-lookup"><span data-stu-id="1e18f-178">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="1e18f-179">**多语言环境** &ndash; gRPC 工具支持所有常用的开发语言，因此，gRPC 是多语言环境的理想选择。</span><span class="sxs-lookup"><span data-stu-id="1e18f-179">**Polyglot environments** &ndash; gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="1e18f-180">**网络受限环境** &ndash; gRPC 消息使用 Protobuf（一种轻量级消息格式）进行序列化。</span><span class="sxs-lookup"><span data-stu-id="1e18f-180">**Network constrained environments** &ndash; gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="1e18f-181">gRPC 消息始终小于等效的 JSON 消息。</span><span class="sxs-lookup"><span data-stu-id="1e18f-181">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="1e18f-182">gRPC 弱点</span><span class="sxs-lookup"><span data-stu-id="1e18f-182">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="1e18f-183">浏览器支持受限</span><span class="sxs-lookup"><span data-stu-id="1e18f-183">Limited browser support</span></span>

<span data-ttu-id="1e18f-184">当前无法通过浏览器直接调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="1e18f-184">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="1e18f-185">gRPC 大量使用 HTTP/2 功能，且没有浏览器在 Web 请求中提供支持 gRPC 客户端所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="1e18f-185">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="1e18f-186">例如，浏览器不允许调用方要求使用 HTTP/2，也不提供对 HTTP/2 基础框架的访问。</span><span class="sxs-lookup"><span data-stu-id="1e18f-186">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="1e18f-187">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) 是 gRPC 团队的另一项技术，可在浏览器中提供有限的 gRPC 支持。</span><span class="sxs-lookup"><span data-stu-id="1e18f-187">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="1e18f-188">gRPC-Web 由两部分组成：支持所有现代浏览器的 JavaScript 客户端，以及服务器上的 gRPC-Web 代理。</span><span class="sxs-lookup"><span data-stu-id="1e18f-188">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="1e18f-189">gRPC-Web 客户端调用代理，代理将根据 gRPC 请求转发到 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="1e18f-189">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="1e18f-190">gRPC-Web 并不支持所有 gRPC 功能。</span><span class="sxs-lookup"><span data-stu-id="1e18f-190">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="1e18f-191">不支持客户端和双向流式传输，并且对服务器流式传输的支持有限。</span><span class="sxs-lookup"><span data-stu-id="1e18f-191">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="1e18f-192">.NET Core 对 gRPC-Web 提供实验性支持。</span><span class="sxs-lookup"><span data-stu-id="1e18f-192">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="1e18f-193">有关详细信息，请访问 <xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="1e18f-193">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="1e18f-194">非人工可读取</span><span class="sxs-lookup"><span data-stu-id="1e18f-194">Not human readable</span></span>

<span data-ttu-id="1e18f-195">HTTP API 请求以文本形式发送，并且可进行人工读取和创建。</span><span class="sxs-lookup"><span data-stu-id="1e18f-195">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="1e18f-196">默认情况下，gRPC 消息使用 Protobuf 进行编码。</span><span class="sxs-lookup"><span data-stu-id="1e18f-196">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="1e18f-197">尽管 Protobuf 可以高效地发送和接收，但其二进制格式非人工可读取。</span><span class="sxs-lookup"><span data-stu-id="1e18f-197">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="1e18f-198">Protobuf 要求在 *.proto* 文件中指定消息接口描述来正确地反序列化。</span><span class="sxs-lookup"><span data-stu-id="1e18f-198">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="1e18f-199">需要使用其他工具来分析网络上的 Protobuf 有效负载以及手动撰写请求。</span><span class="sxs-lookup"><span data-stu-id="1e18f-199">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="1e18f-200">[服务器反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)和 [gRPC 命令行工具](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)等功能可帮助使用二进制 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="1e18f-200">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="1e18f-201">此外，Protobuf 消息支持[与 JSON 之间的转换](https://developers.google.com/protocol-buffers/docs/proto3#json)。</span><span class="sxs-lookup"><span data-stu-id="1e18f-201">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="1e18f-202">内置的 JSON 转换提供在调试时将 Protobuf 消息与人工可读取格式互相转换的高效方法。</span><span class="sxs-lookup"><span data-stu-id="1e18f-202">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="1e18f-203">备用框架方案</span><span class="sxs-lookup"><span data-stu-id="1e18f-203">Alternative framework scenarios</span></span>

<span data-ttu-id="1e18f-204">在以下方案中，建议使用其他框架取代 gRPC：</span><span class="sxs-lookup"><span data-stu-id="1e18f-204">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="1e18f-205">**浏览器可访问的 API** &ndash; gRPC 在浏览器中未受到完全支持。</span><span class="sxs-lookup"><span data-stu-id="1e18f-205">**Browser accessible APIs** &ndash; gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="1e18f-206">gRPC-Web 可以提供浏览器支持，但它具有局限性并引入了服务器代理。</span><span class="sxs-lookup"><span data-stu-id="1e18f-206">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="1e18f-207">**广播实时通信** &ndash; gRPC 支持通过流式传输进行实时通信，但不存在将消息广播到注册连接的概念。</span><span class="sxs-lookup"><span data-stu-id="1e18f-207">**Broadcast real-time communication** &ndash; gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="1e18f-208">例如，在聊天室方案中，应将新的聊天消息发送到聊天室中的所有客户端，这要求每个 gRPC 调用将新的聊天消息单独流式传输到客户端。</span><span class="sxs-lookup"><span data-stu-id="1e18f-208">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="1e18f-209">[SignalR](xref:signalr/introduction) 是适用于此方案的框架。</span><span class="sxs-lookup"><span data-stu-id="1e18f-209">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="1e18f-210"> 具有持久性连接的概念，并内置对广播消息的支持。</span><span class="sxs-lookup"><span data-stu-id="1e18f-210"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="1e18f-211">**进程间通信** &ndash; 进程必须托管 HTTP/2 服务器才能接受传入的 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="1e18f-211">**Inter-process communication** &ndash; A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="1e18f-212">对于 Windows，进程间通信[管道](/dotnet/standard/io/pipe-operations)是一种快速、轻便的通信方法。</span><span class="sxs-lookup"><span data-stu-id="1e18f-212">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e18f-213">其他资源</span><span class="sxs-lookup"><span data-stu-id="1e18f-213">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
