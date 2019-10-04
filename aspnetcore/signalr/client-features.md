---
title: SignalR 客户端功能
author: bradygaster
description: 了解各种 ASP.NET Core SignalR 客户端支持的功能。
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 6718722cdbcfae500026fcd429ca6b9de8f0f103
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925357"
---
# <a name="aspnet-core-signalr-client-features"></a>ASP.NET Core SignalR 客户端功能

## <a name="feature-distribution"></a>功能分发

下表显示提供实时支持的客户端的功能和支持。 对于每项功能，将列出支持此功能的*最低*版本。 如果未列出任何版本，则不支持此功能。

| 功能 | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Azure SignalR 服务支持 |1.0.0|1.0.0|1.0.0|
| [服务器到客户端流式处理](xref:signalr/streaming)          |1.0.0|1.0.0|1.0.0|
| [客户端到服务器的流式处理](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|
| 自动重新连接（[.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)， [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients)）          |3.0.0|3.0.0|❌|
| Websocket 传输 |1.0.0|1.0.0|1.0.0|
| 服务器发送的事件传输 |1.0.0|1.0.0|❌|
| 长轮询传输 |1.0.0|1.0.0|3.0.0|
| JSON 集线器协议 |1.0.0|1.0.0|1.0.0|
| MessagePack 中心协议 |1.0.0|1.0.0|❌|

在[问题跟踪](https://github.com/aspnet/AspNetCore/issues/8711)程序中跟踪了 Java 客户端中的自动重新连接支持。

## <a name="additional-resources"></a>其他资源

* [开始使用 ASP.NET Core SignalR](xref:tutorials/signalr)
* [支持的平台](xref:signalr/supported-platforms)
* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
