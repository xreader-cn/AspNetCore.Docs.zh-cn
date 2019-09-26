---
title: SignalR 客户端功能
author: bradygaster
description: 了解各种 ASP.NET Core SignalR 客户端支持的功能。
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301191"
---
# <a name="aspnet-core-signalr-client-features"></a>ASP.NET Core SignalR 客户端功能

## <a name="feature-distribution"></a>功能分发

下表显示提供实时支持的客户端的功能和支持。

| 功能 | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Azure SignalR 服务支持 |✔|✔|✔|
| [服务器到客户端流式处理](xref:signalr/streaming)          |✔|✔|✔|
| [客户端到服务器的流式处理](xref:signalr/streaming)          |✔|✔|✔|
| 自动重新连接（[.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)， [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients)）          |✔|✔| |
| Websocket 传输 |✔|✔|✔|
| 服务器发送的事件传输 |✔|✔| |
| 长轮询传输 |✔|✔|✔|
| JSON 集线器协议 |✔|✔|✔|
| MessagePack 中心协议 |✔|✔| |

在[问题跟踪](https://github.com/aspnet/AspNetCore/issues/8711)程序中跟踪了 Java 客户端中的自动重新连接支持。

## <a name="additional-resources"></a>其他资源

* [开始使用 ASP.NET Core SignalR](xref:tutorials/signalr)
* [支持的平台](xref:signalr/supported-platforms)
* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
