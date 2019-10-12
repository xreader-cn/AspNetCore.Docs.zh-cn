---
title: 比较 gRPC 服务和 HTTP API
author: jamesnk
description: 了解 gRPC 如何与 HTTP Api 进行比较以及其建议方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/25/2019
uid: grpc/comparison
ms.openlocfilehash: 5c3ea7a78401e6483425fa0774b3051b3d20f516
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2019
ms.locfileid: "72289033"
---
# <a name="compare-grpc-services-with-http-apis"></a>比较 gRPC 服务和 HTTP API

按[James 牛顿-k](https://twitter.com/jamesnk)

本文介绍[gRPC services](https://grpc.io/docs/guides/)如何与 HTTP api （包括 ASP.NET Core [web api](xref:web-api/index)）进行比较。 用于为你的应用程序提供 API 的技术是一个重要的选择，gRPC 与 HTTP Api 相比具有独特的优势。 本文讨论 gRPC 的优势和劣势，并建议使用 gRPC 通过其他技术的方案。

## <a name="high-level-comparison"></a>高级别比较

下表提供了 gRPC 和 HTTP Api 与 JSON 之间功能的高级比较。

| 功能          | gRPC                                               | 具有 JSON 的 HTTP Api           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| 协定         | 必需（*proto*）                                | 可选（OpenAPI）            |
| 传输        | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf （小，二进制）](#performance)           | JSON （大、可读）  |
| Prescriptiveness | [严格规范](#strict-specification)      | 松散. 任何 HTTP 都有效。      |
| 流式处理        | [客户端、服务器、双向](#streaming)       | 客户端、服务器                |
| 浏览器支持  | [否（需要 grpc-web）](#limited-browser-support) | 是                           |
| 安全性         | 传输（HTTPS）                                  | 传输（HTTPS）             |
| 客户端代码生成 | [是](#code-generation)                      | OpenAPI + 第三方工具 |

## <a name="grpc-strengths"></a>gRPC 强度

### <a name="performance"></a>性能

使用[Protobuf](https://developers.google.com/protocol-buffers/docs/overview)（一种高效的二进制消息格式）对 gRPC 消息进行序列化。 Protobuf 在服务器和客户端上的速度非常快。 Protobuf 序列化会导致小型消息负载，在有限的带宽方案（如移动应用）中非常重要。

gRPC 专用于 HTTP/2，这是一个主要的 HTTP 修订版，通过 HTTP 1.x 提供显著的性能优势：

* 二进制组帧和压缩。 HTTP/2 协议在发送和接收时既简洁又高效。
* 通过单个 TCP 连接对多个 HTTP/2 调用进行多路复用。 多路复用消除了[行头阻塞](https://en.wikipedia.org/wiki/Head-of-line_blocking)。

### <a name="code-generation"></a>代码生成

所有 gRPC 框架都提供对代码生成的一流支持。 GRPC 开发的核心文件是一个[ *proto*文件](https://developers.google.com/protocol-buffers/docs/proto3)，用于定义 gRPC 服务和消息的协定。 从此文件 gRPC 框架将生成服务基类、消息和完整客户端。

通过在服务器和客户端之间共享*proto*文件，可以从端到端生成消息和客户端代码。 客户端代码生成将消除客户端和服务器上的重复消息，并为您创建一个强类型的客户端。 无需编写客户端，就可以在具有多个服务的应用程序中节省大量的开发时间。

### <a name="strict-specification"></a>严格规范

不存在具有 JSON 的 HTTP API 的正式规范。 开发人员会争论 Url、HTTP 谓词和响应代码的最佳格式。

[GRPC 规范](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)规定了 gRPC 服务必须遵循的格式。 gRPC 消除了对开发人员时间的争论，因为 gRPC 在平台和实现中保持一致。

### <a name="streaming"></a>流式处理

HTTP/2 为生存期较长的实时通信流奠定了基础。 gRPC 为通过 HTTP/2 进行流式处理提供一流支持。

GRPC 服务支持所有流式处理组合：

* 一元（无流式处理）
* 服务器到客户端流式处理
* 客户端到服务器的流式处理
* 双向流式处理

### <a name="deadlinetimeouts-and-cancellation"></a>截止时间/超时和取消

gRPC 允许客户端指定它们等待 RPC 完成的时间。 [截止时间](https://grpc.io/blog/deadlines)发送到服务器，服务器可以决定在超过截止时间时要执行的操作。 例如，服务器可能会在超时时取消正在进行的 gRPC/HTTP/数据库请求。

通过子 gRPC 调用传播截止时间和取消有助于强制实施资源使用限制。

## <a name="grpc-recommended-scenarios"></a>gRPC 推荐方案

gRPC 适用于以下方案：

* **微服务**&ndash; gRPC 用于低延迟和高吞吐量通信。 gRPC 非常适合轻型微服务，其中效率非常重要。
* **点到点实时通信**&ndash; gRPC 具有对双向流式处理的极佳支持。 gRPC services 无需轮询即可实时推送消息。
* **Polyglot 环境**&ndash; gRPC 工具支持所有常用的开发语言，并为多语言环境选择 gRPC。
* **网络约束环境**&ndash; gRPC 消息使用 Protobuf （一种轻型消息格式）进行序列化。 GRPC 消息始终小于等效的 JSON 消息。

## <a name="grpc-weaknesses"></a>gRPC 弱点

### <a name="limited-browser-support"></a>受限制的浏览器支持

现在无法从浏览器直接调用 gRPC 服务。 gRPC 大量使用 HTTP/2 功能，并且没有浏览器提供 web 请求所需的控制级别，以支持 gRPC 客户端。 例如，浏览器不允许调用方要求使用 HTTP/2，或者提供对基础 HTTP/2 帧的访问。

[gRPC](https://grpc.io/docs/tutorials/basic/web.html)是 gRPC 团队提供的一项额外技术，可在浏览器中提供有限的 gRPC 支持。 gRPC 由两部分组成：支持所有新式浏览器的 JavaScript 客户端和服务器上的 gRPC Web 代理。 GRPC 客户端调用代理，代理将在 gRPC 请求转发到 gRPC 服务器。

并非所有 gRPC 的功能都受 gRPC 支持。 不支持客户端和双向流式处理，并且对服务器流的支持是有限的。

### <a name="not-human-readable"></a>用户不可读

HTTP API 请求以文本的形式发送，可由人读取和创建。

默认情况下，使用 Protobuf 对 gRPC 消息进行编码。 尽管 Protobuf 是发送和接收的高效，但其二进制格式不是用户可读的。 Protobuf 要求在*proto*文件中指定的消息接口说明正确地进行反序列化。 需要使用其他工具来分析线路上的 Protobuf 负载，并手动撰写请求。

诸如[server 反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)和[gRPC 命令行工具](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)等功能可帮助进行二进制 Protobuf 消息。 此外，Protobuf 消息支持[与 JSON 之间的转换](https://developers.google.com/protocol-buffers/docs/proto3#json)。 内置 JSON 转换提供了一种有效的方法，可在调试时将 Protobuf 消息转换为可读的形式。

## <a name="alternative-framework-scenarios"></a>备用框架方案

建议在以下情况中通过 gRPC 使用其他框架：

* 浏览器不完全支持**浏览器辅助功能 api** &ndash; gRPC。 gRPC 可以提供浏览器支持，但它具有局限性并引入了服务器代理。
* **广播实时通信**&ndash; gRPC 支持通过流式处理进行实时通信，但将消息广播到注册连接的概念并不存在。 例如，在聊天室方案中，应将新的聊天消息发送到聊天室中的所有客户端，而每个 gRPC 调用都需要分别将新的聊天消息流式传输到客户端。 [SignalR](xref:signalr/introduction)是此方案的有用框架。 SignalR 具有持续连接和广播消息的内置支持的概念。
* **进程间通信**&ndash; 进程必须托管 HTTP/2 服务器以接受传入的 gRPC 调用。 对于 Windows，进程间通信[管道](/dotnet/standard/io/pipe-operations)是一种快速、轻量的通信方法。

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
