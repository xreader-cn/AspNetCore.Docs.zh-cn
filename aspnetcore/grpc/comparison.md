---
title: 比较 gRPC 服务和 HTTP API
author: jamesnk
description: 了解如何使用 HTTP Api 和它所具有的 gRPC 比较建议方案。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/31/2019
uid: grpc/comparison
ms.openlocfilehash: 8f4cefe1dedcf4cfd9650e73e6a1ba30dbbfeffa
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087402"
---
# <a name="comparing-grpc-services-with-http-apis"></a>比较 gRPC 服务和 HTTP API

通过[James Newton-king](https://twitter.com/jamesnk)

本文介绍如何[gRPC 服务](https://grpc.io/docs/guides/)与 HTTP Api 进行比较 (包括 ASP.NET Core [Web Api](xref:web-api/index))。 用于提供一个用于您的应用程序 API 的技术是一个重要的选择，和 gRPC 提供相比 HTTP Api 的独特优势。 本文讨论了 gRPC 的优缺点，并建议使用 gRPC 通过其他技术的方案。

#### <a name="overview"></a>概述

|    功能             |    gRPC                                                 |    HTTP Api 使用 JSON                       |
|------------------------|---------------------------------------------------------|----------------------------------------------|
|    协定            |    所需 (`*.proto`)                                 |    可选 (OpenAPI)                        |
|    传输           |    HTTP/2                                               |    HTTP                                      |
|    Payload             |    [Protobuf （小、 二进制文件）](#performance)             |    JSON （大，用户可读）              |
|    Prescriptiveness    |    [严格的规范](#strict-specification)        |    松散。 任何 HTTP 是有效                  |
|    流式处理           |    [客户端、 服务器、 双向](#streaming)         |    客户端、 服务器                            |
|    浏览器支持     |    [否 （需要 grpc web）](#limited-browser-support)   |    是                                       |
|    安全性            |    传输 (HTTPS)                                    |    传输 (HTTPS)                         |
|    客户端代码生成     |    [是](#code-generation)                              |    OpenAPI 和第三方工具             |

## <a name="grpc-strengths"></a>gRPC 优势

### <a name="performance"></a>性能

gRPC 消息使用序列化[Protobuf](https://developers.google.com/protocol-buffers/docs/overview)，高效二进制消息格式。 Protobuf 序列化非常快速地在服务器和客户端上。 Protobuf 序列化结果中较小的消息有效负载，在带宽有限的方案中很重要，如移动应用。

gRPC 专为 HTTP/2，通过 HTTP 提供显著的性能优势的 HTTP 的主要修订版本 1.x:

* 二进制组帧和压缩。 HTTP/2 协议是紧凑、 高效，同时在发送和接收。
* 多路复用，多个 HTTP/2 调用通过单个 TCP 连接。 多路复用消除[head 线端阻塞](https://en.wikipedia.org/wiki/Head-of-line_blocking)。

### <a name="code-generation"></a>代码生成

所有 gRPC 框架都提供对代码生成的一流支持。 GRPC 开发的核心文件是[`*.proto`文件](https://developers.google.com/protocol-buffers/docs/proto3)，用于定义 gRPC 服务和消息协定。 从代码框架将此文件 gRPC 生成服务的基类、 消息和完整的客户端。

通过共享`*.proto`到最终可以结束从生成服务器和客户端、 消息和客户端代码之间的文件。 客户端的代码生成消除重复的客户端和服务器上的消息，并创建为您的强类型化客户端。 无需编写一个客户端在与许多服务的应用程序中节省了大量的开发时间。

### <a name="strict-specification"></a>严格的规范

使用 JSON 的 HTTP API 的正式规范不存在。 开发人员争论的最佳格式的 Url、 HTTP 谓词和响应代码。

[GRPC 规范](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)是 gRPC 服务必须遵循的格式规范。 gRPC 消除辩论并节省开发人员的时间，因为 gPRC 是一致的跨平台和实现。

### <a name="streaming"></a>流式处理

HTTP/2 为生存期较长的实时通信流提供了基础。 gRPC 提供了用于流式处理通过 HTTP/2 的一流支持。

GRPC 服务支持所有流式处理组合：

* 一元 （没有流式处理）
* 服务器到客户端流式处理
* 客户端到服务器流式处理
* 双向流式处理

### <a name="deadlinetimeouts-and-cancellation"></a>截止时间中的超时和取消

gRPC 允许客户端指定时间长度的等待 RPC 完成。 [截止时间](https://grpc.io/blog/deadlines)发送到服务器，并在服务器可以决定它超出了在截止时间时要采取的操作。 例如，服务器可能会取消正在进行中 gRPC/HTTP/数据库请求超时。

传播的截止时间和通过子 gRPC 取消调用可帮助强制实施资源使用情况限制。

## <a name="grpc-recommended-scenarios"></a>gRPC 建议方案

gRPC 非常适合以下方案：

* **微服务** &ndash; gRPC 是设计的低延迟和高吞吐量的通信。 gRPC 非常轻型的微服务的效率至关重要。
* **点到点实时通信** &ndash; gRPC 提供有力的支持的双向流式处理。 gRPC 服务可以将推送消息实时而无需轮询。
* **Polygot 环境** &ndash; gRPC 工具支持所有常用的开发语言，使 gRPC 多语言环境的不错选择。
* **网络受限制的环境** &ndash; gRPC 消息用 Protobuf，轻型消息格式进行序列化。 GRPC 消息始终是小于等效的 JSON 消息。

## <a name="grpc-weaknesses"></a>gRPC 漏洞

### <a name="limited-browser-support"></a>有限的浏览器支持

它不可能直接立即从浏览器调用 gRPC 服务。 gRPC 很大程度使用 HTTP/2 功能，并浏览器不提供对 web 请求，以支持 gRPC 客户端所需的控制度。 例如，浏览器不允许调用方需要使用 HTTP/2，或提供对基础 HTTP/2 框架的访问。

[gRPC Web](https://grpc.io/docs/tutorials/basic/web.html)是 gRPC 团队提供有限的 gRPC 支持在浏览器中的其他技术。 gRPC Web 由两部分组成： 在服务器支持所有新型浏览器和 gRPC Web 代理的 JavaScript 客户端。 GRPC Web 客户端调用代理，代理将转发到 gRPC 服务器 gRPC 请求。

并非所有 gRPC 的功能支持 gRPC Web。 客户端和双向流式处理不受支持，并且没有对服务器流式处理的支持有限。

### <a name="not-human-readable"></a>没有用户可读

HTTP API 请求以文本格式发送和可以读取和创建用户。

默认情况下，可将 gRPC 消息编码与 Protobuf。 Protobuf 高效发送和接收时，其二进制格式不是人类可读。 Protobuf 要求中指定的消息的接口描述`*.proto`文件来正确地反序列化。 其他工具则需要分析 Protobuf 在网络上的有效负载并手动编写请求。

等功能[server 反射](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)并[gRPC 命令行工具](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)存在来帮助进行二进制 Protobuf 消息。 此外，Protobuf 消息支持[转换到和从 JSON](https://developers.google.com/protocol-buffers/docs/proto3#json)。 内置的 JSON 转换提供了用于 Protobuf 消息之间来回转换可读的形式进行调试时的有效方法。

## <a name="alternative-framework-scenarios"></a>替代框架方案

其他框架，建议在以下方案中 gRPC 优先：

* **浏览器访问 Api** &ndash; gRPC 不完全支持在浏览器中。 gRPC Web 可以提供浏览器支持，但它具有限制条件，引入了服务器代理。
* **广播实时通信** &ndash; gRPC 支持通过流式处理、 实时通信，但这一概念的广播消息，查看已注册的连接到不存在。 例如在聊天室方案中，应将新聊天消息发送到聊天室中的所有客户端，每个 gRPC 调用需要单独流式传输到客户端的新聊天消息。 [SignalR](xref:signalr/introduction)是一个用于此方案中有用的框架。 SignalR 提供持久连接以及内置的广播消息支持的概念。
* **进程间通信**&ndash;进程必须承载 HTTP/2 服务器接受 gRPC 的传入呼叫。 对于 Windows，进程间通信[管道](/dotnet/standard/io/pipe-operations)是轻量级的通信方法。

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
