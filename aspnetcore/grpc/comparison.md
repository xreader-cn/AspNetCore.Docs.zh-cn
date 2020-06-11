---
title: 比较 gRPC 服务和 HTTP API
author: jamesnk
description: 了解如何比较 gRPC 和 HTTP API 以及建议方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/comparison
ms.openlocfilehash: f622a1518781c255d36762dc651f975625dabf7c
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106125"
---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="5222d-103">比较 gRPC 服务和 HTTP API</span><span class="sxs-lookup"><span data-stu-id="5222d-103">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="5222d-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5222d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5222d-105">本文介绍如何将 [gRPC 服务](https://grpc.io/docs/guides/)与具有 JSON 的 HTTP API（包括 ASP.NET Core [Web API](xref:web-api/index)）进行比较。</span><span class="sxs-lookup"><span data-stu-id="5222d-105">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="5222d-106">用于为应用提供 API 的技术是一个重要选择，与 HTTP API 相比，gRPC 提供独特优势。</span><span class="sxs-lookup"><span data-stu-id="5222d-106">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="5222d-107">本文讨论 gRPC 的优点和缺点，并提供优先于其他技术选择使用 gRPC 的建议方案。</span><span class="sxs-lookup"><span data-stu-id="5222d-107">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="5222d-108">概括比较</span><span class="sxs-lookup"><span data-stu-id="5222d-108">High-level comparison</span></span>

<span data-ttu-id="5222d-109">下表对 gRPC 和具有 JSON 的 HTTP API 之间的功能进行了简单比较。</span><span class="sxs-lookup"><span data-stu-id="5222d-109">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="5222d-110">功能</span><span class="sxs-lookup"><span data-stu-id="5222d-110">Feature</span></span>          | <span data-ttu-id="5222d-111">gRPC</span><span class="sxs-lookup"><span data-stu-id="5222d-111">gRPC</span></span>                                               | <span data-ttu-id="5222d-112">具有 JSON 的 HTTP API</span><span class="sxs-lookup"><span data-stu-id="5222d-112">HTTP APIs with JSON</span></span>           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| <span data-ttu-id="5222d-113">协定</span><span class="sxs-lookup"><span data-stu-id="5222d-113">Contract</span></span>         | <span data-ttu-id="5222d-114">必需 ( *.proto*)</span><span class="sxs-lookup"><span data-stu-id="5222d-114">Required (*.proto*)</span></span>                                | <span data-ttu-id="5222d-115">可选 (OpenAPI)</span><span class="sxs-lookup"><span data-stu-id="5222d-115">Optional (OpenAPI)</span></span>            |
| <span data-ttu-id="5222d-116">协议</span><span class="sxs-lookup"><span data-stu-id="5222d-116">Protocol</span></span>         | <span data-ttu-id="5222d-117">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="5222d-117">HTTP/2</span></span>                                             | <span data-ttu-id="5222d-118">HTTP</span><span class="sxs-lookup"><span data-stu-id="5222d-118">HTTP</span></span>                          |
| <span data-ttu-id="5222d-119">Payload</span><span class="sxs-lookup"><span data-stu-id="5222d-119">Payload</span></span>          | [<span data-ttu-id="5222d-120">Protobuf（小型，二进制）</span><span class="sxs-lookup"><span data-stu-id="5222d-120">Protobuf (small, binary)</span></span>](#performance)           | <span data-ttu-id="5222d-121">JSON（大型，人工可读取）</span><span class="sxs-lookup"><span data-stu-id="5222d-121">JSON (large, human readable)</span></span>  |
| <span data-ttu-id="5222d-122">规定性</span><span class="sxs-lookup"><span data-stu-id="5222d-122">Prescriptiveness</span></span> | [<span data-ttu-id="5222d-123">严格规范</span><span class="sxs-lookup"><span data-stu-id="5222d-123">Strict specification</span></span>](#strict-specification)      | <span data-ttu-id="5222d-124">宽松。</span><span class="sxs-lookup"><span data-stu-id="5222d-124">Loose.</span></span> <span data-ttu-id="5222d-125">任何 HTTP 均有效。</span><span class="sxs-lookup"><span data-stu-id="5222d-125">Any HTTP is valid.</span></span>     |
| <span data-ttu-id="5222d-126">流式处理</span><span class="sxs-lookup"><span data-stu-id="5222d-126">Streaming</span></span>        | [<span data-ttu-id="5222d-127">客户端、服务器，双向</span><span class="sxs-lookup"><span data-stu-id="5222d-127">Client, server, bi-directional</span></span>](#streaming)       | <span data-ttu-id="5222d-128">客户端、服务器</span><span class="sxs-lookup"><span data-stu-id="5222d-128">Client, server</span></span>                |
| <span data-ttu-id="5222d-129">浏览器支持</span><span class="sxs-lookup"><span data-stu-id="5222d-129">Browser support</span></span>  | [<span data-ttu-id="5222d-130">无（需要 grpc-web）</span><span class="sxs-lookup"><span data-stu-id="5222d-130">No (requires grpc-web)</span></span>](#limited-browser-support) | <span data-ttu-id="5222d-131">是</span><span class="sxs-lookup"><span data-stu-id="5222d-131">Yes</span></span>                           |
| <span data-ttu-id="5222d-132">安全性</span><span class="sxs-lookup"><span data-stu-id="5222d-132">Security</span></span>         | <span data-ttu-id="5222d-133">传输 (TLS)</span><span class="sxs-lookup"><span data-stu-id="5222d-133">Transport (TLS)</span></span>                                    | <span data-ttu-id="5222d-134">传输 (TLS)</span><span class="sxs-lookup"><span data-stu-id="5222d-134">Transport (TLS)</span></span>               |
| <span data-ttu-id="5222d-135">客户端代码生成</span><span class="sxs-lookup"><span data-stu-id="5222d-135">Client code-generation</span></span> | [<span data-ttu-id="5222d-136">是</span><span class="sxs-lookup"><span data-stu-id="5222d-136">Yes</span></span>](#code-generation)                      | <span data-ttu-id="5222d-137">OpenAPI + 第三方工具</span><span class="sxs-lookup"><span data-stu-id="5222d-137">OpenAPI + third-party tooling</span></span> |

## <a name="grpc-strengths"></a><span data-ttu-id="5222d-138">gRPC 优点</span><span class="sxs-lookup"><span data-stu-id="5222d-138">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="5222d-139">性能</span><span class="sxs-lookup"><span data-stu-id="5222d-139">Performance</span></span>

<span data-ttu-id="5222d-140">gRPC 消息使用 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)（一种高效的二进制消息格式）进行序列化。</span><span class="sxs-lookup"><span data-stu-id="5222d-140">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="5222d-141">Protobuf 在服务器和客户端上可以非常快速地序列化。</span><span class="sxs-lookup"><span data-stu-id="5222d-141">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="5222d-142">Protobuf 序列化产生的有效负载较小，这在移动应用等带宽有限的方案中很重要。</span><span class="sxs-lookup"><span data-stu-id="5222d-142">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="5222d-143">gRPC 专为 HTTP/2（HTTP 的主要版本）而设计，与 HTTP 1.x 相比，HTTP/2 具有巨大性能优势：</span><span class="sxs-lookup"><span data-stu-id="5222d-143">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="5222d-144">二进制组帧和压缩。</span><span class="sxs-lookup"><span data-stu-id="5222d-144">Binary framing and compression.</span></span> <span data-ttu-id="5222d-145">HTTP/2 协议在发送和接收方面均紧凑且高效。</span><span class="sxs-lookup"><span data-stu-id="5222d-145">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="5222d-146">在单个 TCP 连接上多路复用多个 HTTP/2 调用。</span><span class="sxs-lookup"><span data-stu-id="5222d-146">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="5222d-147">多路复用可消除[队头阻塞](https://en.wikipedia.org/wiki/Head-of-line_blocking)。</span><span class="sxs-lookup"><span data-stu-id="5222d-147">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="5222d-148">HTTP/2 不是 gRPC 独占的。</span><span class="sxs-lookup"><span data-stu-id="5222d-148">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="5222d-149">许多请求类型（包括使用 JSON 的 HTTP API）都可以使用 HTTP/2，并受益于其性能改进。</span><span class="sxs-lookup"><span data-stu-id="5222d-149">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="5222d-150">代码生成</span><span class="sxs-lookup"><span data-stu-id="5222d-150">Code generation</span></span>

<span data-ttu-id="5222d-151">所有 gRPC 框架都为代码生成提供一流支持。</span><span class="sxs-lookup"><span data-stu-id="5222d-151">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="5222d-152">[.proto 文件](https://developers.google.com/protocol-buffers/docs/proto3)是 gRPC 开发的核心文件，它定义 gRPC 服务和消息的协定。</span><span class="sxs-lookup"><span data-stu-id="5222d-152">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="5222d-153">通过此文件，gRPC 框架编码生成服务基类、消息和完整的客户端。</span><span class="sxs-lookup"><span data-stu-id="5222d-153">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="5222d-154">通过在服务器和客户端之间共享 *.proto* 文件，可以端到端生成消息和客户端代码。</span><span class="sxs-lookup"><span data-stu-id="5222d-154">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="5222d-155">客户端的代码生成消除了客户端和服务器上的消息重复，并为你创建强类型客户端。</span><span class="sxs-lookup"><span data-stu-id="5222d-155">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="5222d-156">无需编写客户端可在具有许多服务的应用程序中节省大量开发时间。</span><span class="sxs-lookup"><span data-stu-id="5222d-156">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="5222d-157">严格规范</span><span class="sxs-lookup"><span data-stu-id="5222d-157">Strict specification</span></span>

<span data-ttu-id="5222d-158">具有 JSON 的 HTTP API 没有正式规范。</span><span class="sxs-lookup"><span data-stu-id="5222d-158">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="5222d-159">开发人员为 URL、HTTP 谓词和响应代码的最佳格式争论不休。</span><span class="sxs-lookup"><span data-stu-id="5222d-159">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="5222d-160">[gRPC 规范](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)对 gRPC 服务必须遵循的格式进行了规定。</span><span class="sxs-lookup"><span data-stu-id="5222d-160">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="5222d-161">gRPC 消除了争论并为开发人员节省了时间，因为 gRPC 在各个平台和实现中都是一致的。</span><span class="sxs-lookup"><span data-stu-id="5222d-161">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="5222d-162">流式处理</span><span class="sxs-lookup"><span data-stu-id="5222d-162">Streaming</span></span>

<span data-ttu-id="5222d-163">HTTP/2 为长期实时通信流提供基础。</span><span class="sxs-lookup"><span data-stu-id="5222d-163">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="5222d-164">gRPC 为通过 HTTP/2 进行流式传输提供一流支持。</span><span class="sxs-lookup"><span data-stu-id="5222d-164">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="5222d-165">gRPC 服务支持所有流式传输组合：</span><span class="sxs-lookup"><span data-stu-id="5222d-165">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="5222d-166">一元（无流式传输）</span><span class="sxs-lookup"><span data-stu-id="5222d-166">Unary (no streaming)</span></span>
* <span data-ttu-id="5222d-167">服务器到客户端流式传输</span><span class="sxs-lookup"><span data-stu-id="5222d-167">Server to client streaming</span></span>
* <span data-ttu-id="5222d-168">客户端到服务器流式传输</span><span class="sxs-lookup"><span data-stu-id="5222d-168">Client to server streaming</span></span>
* <span data-ttu-id="5222d-169">双向流式传输</span><span class="sxs-lookup"><span data-stu-id="5222d-169">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="5222d-170">截止时间/超时和取消</span><span class="sxs-lookup"><span data-stu-id="5222d-170">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="5222d-171">gRPC 允许客户端指定其愿意等待 RPC 完成的时间期限。</span><span class="sxs-lookup"><span data-stu-id="5222d-171">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="5222d-172">[截止时间](https://grpc.io/blog/deadlines)会发送到服务器，如果超过截止时间，服务器可以决定要执行的操作。</span><span class="sxs-lookup"><span data-stu-id="5222d-172">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="5222d-173">例如，服务器可能会在超时后取消正在进行的 gRPC/HTTP/数据库请求。</span><span class="sxs-lookup"><span data-stu-id="5222d-173">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="5222d-174">通过 gRPC 子调用传播截止时间和取消有助于强制执行资源使用限制。</span><span class="sxs-lookup"><span data-stu-id="5222d-174">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="5222d-175">gRPC 建议方案</span><span class="sxs-lookup"><span data-stu-id="5222d-175">gRPC recommended scenarios</span></span>

<span data-ttu-id="5222d-176">gRPC 非常适合以下方案：</span><span class="sxs-lookup"><span data-stu-id="5222d-176">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="5222d-177">微服务：gRPC 设计用于低延迟和高吞吐量通信。</span><span class="sxs-lookup"><span data-stu-id="5222d-177">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="5222d-178">gRPC 对于效率至关重要的轻量级微服务非常有用。</span><span class="sxs-lookup"><span data-stu-id="5222d-178">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="5222d-179">点对点实时通信：gRPC 对双向流式传输提供出色的支持。</span><span class="sxs-lookup"><span data-stu-id="5222d-179">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="5222d-180">gRPC 服务可以实时推送消息而无需轮询。</span><span class="sxs-lookup"><span data-stu-id="5222d-180">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="5222d-181">多语言环境：gRPC 工具支持所有常用的开发语言，因此，gRPC 是多语言环境的理想选择。</span><span class="sxs-lookup"><span data-stu-id="5222d-181">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="5222d-182">网络受限环境：gRPC 消息使用 Protobuf（一种轻量级消息格式）进行序列化。</span><span class="sxs-lookup"><span data-stu-id="5222d-182">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="5222d-183">gRPC 消息始终小于等效的 JSON 消息。</span><span class="sxs-lookup"><span data-stu-id="5222d-183">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="5222d-184">gRPC 弱点</span><span class="sxs-lookup"><span data-stu-id="5222d-184">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="5222d-185">浏览器支持受限</span><span class="sxs-lookup"><span data-stu-id="5222d-185">Limited browser support</span></span>

<span data-ttu-id="5222d-186">当前无法通过浏览器直接调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="5222d-186">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="5222d-187">gRPC 大量使用 HTTP/2 功能，且没有浏览器在 Web 请求中提供支持 gRPC 客户端所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="5222d-187">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="5222d-188">例如，浏览器不允许调用方要求使用 HTTP/2，也不提供对 HTTP/2 基础框架的访问。</span><span class="sxs-lookup"><span data-stu-id="5222d-188">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="5222d-189">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) 是 gRPC 团队的另一项技术，可在浏览器中提供有限的 gRPC 支持。</span><span class="sxs-lookup"><span data-stu-id="5222d-189">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="5222d-190">gRPC-Web 由两部分组成：支持所有现代浏览器的 JavaScript 客户端，以及服务器上的 gRPC-Web 代理。</span><span class="sxs-lookup"><span data-stu-id="5222d-190">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="5222d-191">gRPC-Web 客户端调用代理，代理将根据 gRPC 请求转发到 gRPC 服务器。</span><span class="sxs-lookup"><span data-stu-id="5222d-191">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="5222d-192">gRPC-Web 并不支持所有 gRPC 功能。</span><span class="sxs-lookup"><span data-stu-id="5222d-192">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="5222d-193">不支持客户端和双向流式传输，并且对服务器流式传输的支持有限。</span><span class="sxs-lookup"><span data-stu-id="5222d-193">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="5222d-194">.NET Core 对 gRPC-Web 提供实验性支持。</span><span class="sxs-lookup"><span data-stu-id="5222d-194">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="5222d-195">有关详细信息，请访问 <xref:grpc/browser>。</span><span class="sxs-lookup"><span data-stu-id="5222d-195">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="5222d-196">非人工可读取</span><span class="sxs-lookup"><span data-stu-id="5222d-196">Not human readable</span></span>

<span data-ttu-id="5222d-197">HTTP API 请求以文本形式发送，并且可进行人工读取和创建。</span><span class="sxs-lookup"><span data-stu-id="5222d-197">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="5222d-198">默认情况下，gRPC 消息使用 Protobuf 进行编码。</span><span class="sxs-lookup"><span data-stu-id="5222d-198">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="5222d-199">尽管 Protobuf 可以高效地发送和接收，但其二进制格式非人工可读取。</span><span class="sxs-lookup"><span data-stu-id="5222d-199">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="5222d-200">Protobuf 要求在 *.proto* 文件中指定消息接口描述来正确地反序列化。</span><span class="sxs-lookup"><span data-stu-id="5222d-200">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="5222d-201">需要使用其他工具来分析网络上的 Protobuf 有效负载以及手动撰写请求。</span><span class="sxs-lookup"><span data-stu-id="5222d-201">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="5222d-202">[服务器反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)和 [gRPC 命令行工具](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)等功能可帮助使用二进制 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="5222d-202">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="5222d-203">此外，Protobuf 消息支持[与 JSON 之间的转换](https://developers.google.com/protocol-buffers/docs/proto3#json)。</span><span class="sxs-lookup"><span data-stu-id="5222d-203">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="5222d-204">内置的 JSON 转换提供在调试时将 Protobuf 消息与人工可读取格式互相转换的高效方法。</span><span class="sxs-lookup"><span data-stu-id="5222d-204">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="5222d-205">备用框架方案</span><span class="sxs-lookup"><span data-stu-id="5222d-205">Alternative framework scenarios</span></span>

<span data-ttu-id="5222d-206">在以下方案中，建议使用其他框架取代 gRPC：</span><span class="sxs-lookup"><span data-stu-id="5222d-206">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="5222d-207">浏览器可访问的 API：gRPC 在浏览器中未受到完全支持。</span><span class="sxs-lookup"><span data-stu-id="5222d-207">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="5222d-208">gRPC-Web 可以提供浏览器支持，但它具有局限性并引入了服务器代理。</span><span class="sxs-lookup"><span data-stu-id="5222d-208">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="5222d-209">广播实时通信：gRPC 支持通过流式传输进行实时通信，但不存在将消息广播到注册连接的概念。</span><span class="sxs-lookup"><span data-stu-id="5222d-209">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="5222d-210">例如，在聊天室方案中，应将新的聊天消息发送到聊天室中的所有客户端，这要求每个 gRPC 调用将新的聊天消息单独流式传输到客户端。</span><span class="sxs-lookup"><span data-stu-id="5222d-210">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="5222d-211">[SignalR](xref:signalr/introduction) 是适用于此方案的框架。</span><span class="sxs-lookup"><span data-stu-id="5222d-211">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="5222d-212"> 具有持久性连接的概念，并内置对广播消息的支持。</span><span class="sxs-lookup"><span data-stu-id="5222d-212"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="5222d-213">进程间通信：进程必须托管 HTTP/2 服务器才能接受传入的 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="5222d-213">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="5222d-214">对于 Windows，进程间通信[管道](/dotnet/standard/io/pipe-operations)是一种快速、轻便的通信方法。</span><span class="sxs-lookup"><span data-stu-id="5222d-214">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5222d-215">其他资源</span><span class="sxs-lookup"><span data-stu-id="5222d-215">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
