---
title: ASP.NET Core 上 gRPC 的简介
author: juntaoluo
description: 了解使用 Kestrel 服务器和 ASP.NET Core 堆栈的 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 02/26/2019
uid: grpc/index
ms.openlocfilehash: dd1c42744bfda965df91ea1fcc0b71814317b969
ms.sourcegitcommit: a467828b5e4eaae291d961ffe2279a571900de23
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58142410"
---
# <a name="introduction-to-grpc-on-aspnet-core"></a>ASP.NET Core 上 gRPC 的简介

作者：[John Luo](https://github.com/juntaoluo)

[gRPC](https://grpc.io/docs/guides/) 是一种与语言无关的高性能远程过程调用 (RPC) 框架。 有关 gRPC 基础知识的详细信息，请参阅 [gRPC 文档页](https://grpc.io/docs/)。

gRPC 的主要优点是：
* 现代高性能轻量级 RPC 框架。
* 协定优先 API 开发，默认使用协议缓冲区，允许与语言无关的实现。
* 可用于多种语言的工具，以生成强类型服务器和客户端。
* 支持客户端、服务器和双向流式处理调用。
* 使用 Protobuf 二进制序列化减少对网络的使用。

这些优点使 gRPC 适用于：
* 效率至关重要的轻量级微服务。
* 需要多种语言用于开发的 Polyglot 系统。
* 需要处理流式处理请求或响应的点对点实时服务。

虽然 C# 实现目前在官方 [ gRPC 页](https://grpc.io/docs/quickstart/csharp.html)上可用，但当前实现依赖于用 C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)) 编写的本机库。 目前正在开展工作，以提供基于 Kestrel HTTP 服务器和完全托管的 ASP.NET Core 堆栈的新实现。 以下文档介绍了使用此新实现生成 gRPC 服务。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>