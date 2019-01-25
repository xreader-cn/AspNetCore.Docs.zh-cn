---
title: ASP.NET Core SignalR 简介
author: bradygaster
description: 了解如何 ASP.NET Core SignalR 库简化了将实时功能添加到应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836358"
---
# <a name="introduction-to-aspnet-core-signalr"></a>ASP.NET Core SignalR 简介

## <a name="what-is-signalr"></a>SignalR 是什么？

ASP.NET Core SignalR 是一个开源代码库，它简化了向应用添加实时 Web 功能的过程。 实时 Web 功能使服务器端代码能够即时将内容推送到客户端。

SignalR 的适用对象：

* 需要来自服务器的高频率更新的应用。 例如：游戏、社交网络、投票、拍卖、地图和 GPS 应用。
* 仪表板和监视应用。 示例包括公司仪表板、销售状态即时更新或行程警示。
* 协作应用。 协作应用的示例包括白板应用和团队会议软件。
* 需要通知的应用。 社交网络、电子邮件、聊天、游戏、行程警示以及许多其他应用都使用通知。

SignalR 提供了一个用于创建服务器到客户端[远程过程调用（RPC）](https://wikipedia.org/wiki/Remote_procedure_call)的 API。 RPC 通过服务器端 .NET Core 代码调用客户端上的 JavaScript 函数。

以下是 ASP.NET Core SignalR 的一些功能：

* 自动管理连接。
* 同时向所有连接的客户端发送消息。 例如，聊天室。
* 将消息发送到特定的客户端或客户端组。
* 扩展以处理增加的流量。

源代码托管在 [GitHub 上的 SignalR 存储库](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)中。

## <a name="transports"></a>传输

SignalR 支持几种方法用于处理实时通信：

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* 服务器发送事件
* 长轮询

SignalR 会从服务器和客户端支持的功能中自动选择最佳传输方法

## <a name="hubs"></a>中心

SignalR 使用*中心*在客户端和服务器之间进行通信。

“中心”是一种高级管道，允许客户端和服务器相互调用方法。 SignalR 自动处理跨计算机边界的调度，允许客户端和服务器相互调用方法。 可以将强类型参数传递给方法，从而启用模型绑定。 SignalR 提供两个内置中心协议：基于 JSON 的文本协议和基于 [MessagePack](https://msgpack.org/) 的二进制协议。  与 JSON 相比，MessagePack 创建的消息通常比较小。 旧版浏览器必须支持 [XHR 2](https://caniuse.com/#feat=xhr2) 才能提供 MessagePack 协议支持。

中心通过发送包含客户端方法的名称和参数的消息来调用客户端代码。 使用配置的协议对作为方法参数发送的对象进行反序列化。 客户端会尝试将方法名称与客户端代码中的方法匹配。 当客户端找到匹配项时，它会调用该方法并将反序列化的参数数据传递给它。

## <a name="additional-resources"></a>其他资源

* [开始使用 ASP.NET Core SignalR](xref:tutorials/signalr)
* [支持的平台](xref:signalr/supported-platforms)
* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
