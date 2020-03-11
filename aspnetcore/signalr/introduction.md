---
title: ASP.NET Core SignalR 简介
author: bradygaster
description: 了解 ASP.NET Core SignalR 库如何简化将实时功能添加到应用程序。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 635431abf9263c2dff261aea47e6f8324061763f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653364"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR 简介

## <a name="what-is-opno-locsignalr"></a>什么是 SignalR？

ASP.NET Core SignalR 是一个开源库，它简化了向应用程序添加实时 web 功能的功能。 实时 Web 功能使服务器端代码能够即时将内容推送到客户端。

适用于 SignalR的候选项：

* 需要从服务器进行高频率更新的应用。 示例包括游戏、社交网络、投票、拍卖、地图和 GPS 应用。
* 仪表板和监视应用。 示例包括公司仪表板、即时销售更新或旅行警报。
* 协作应用。 协作应用的示例包括白板应用和团队会议软件。
* 需要通知的应用。 社交网络、电子邮件、聊天、游戏、旅行警报和很多其他应用都需使用通知。

SignalR 提供用于创建服务器到客户端[远程过程调用（RPC）](https://wikipedia.org/wiki/Remote_procedure_call)的 API。 RPC 通过服务器端 .NET Core 代码调用客户端上的 JavaScript 函数。

下面是 ASP.NET Core SignalR 的一些功能：

* 自动管理连接。
* 同时向所有连接的客户端发送消息。 例如，聊天室。
* 向特定客户端或客户端组发送消息。
* 可缩放以处理不断增加的流量。

源托管在[GitHub 上的SignalR 存储库](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)中。

## <a name="transports"></a>传输

SignalR 支持以下用于处理实时通信的技术（按正常回退的顺序）：

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* 服务器发送的事件
* 长轮询

SignalR 会自动选择服务器和客户端功能内的最佳传输方法。

## <a name="hubs"></a>中心

SignalR 使用*集线器*在客户端和服务器之间进行通信。

“中心”是一种高级管道，允许客户端和服务器相互调用方法。 SignalR 会自动处理计算机边界中的调度，从而允许客户端在服务器上调用方法，反之亦然。 可以将强类型参数传递给方法，从而启用模型绑定。 SignalR 提供了两种内置的集线器协议：基于 JSON 的文本协议和基于[MessagePack](https://msgpack.org/)的二进制协议。  与 JSON 相比，MessagePack 创建的消息通常比较小。 较早的浏览器必须支持[XHR 级别 2](https://caniuse.com/#feat=xhr2) ，才能提供 MessagePack 协议支持。

中心通过发送包含客户端方法的名称和参数的消息来调用客户端代码。 使用配置的协议对作为方法参数发送的对象进行反序列化。 客户端会尝试将方法名称与客户端代码中的方法匹配。 当客户端找到匹配项时，它会调用该方法并将反序列化的参数数据传递给它。

## <a name="additional-resources"></a>其他资源

* [ASP.NET Core 的 SignalR 入门](xref:tutorials/signalr)
* [支持的平台](xref:signalr/supported-platforms)
* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
