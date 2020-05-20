---
title: ASP.NET Core SignalR 客户端
author: bradygaster
description: 了解各种 ASP.NET Core 的客户端支持的功能 SignalR 。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/client-features
ms.openlocfilehash: a759e473ff7ffaebd0eb9309f37a959d0e06a466
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438951"
---
# <a name="aspnet-core-signalr-clients"></a>ASP.NET Core SignalR 客户端

## <a name="versioning-support-and-compatibility"></a>版本控制、支持和兼容性

SignalR 客户端与服务器组件一起发运，并进行版本控制以使其匹配。 任何受支持的客户端都可以安全地连接到任何受支持的服务器，任何兼容性问题都将被视为修复 bug。 与 .NET Core 的其余部分相同的支持生命周期支持 SignalR 客户端。 有关详细信息，请参阅[.Net Core 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

许多功能都需要兼容的客户端**和**服务器。 请参阅下面的表，其中显示了各种功能的最低版本。

SignalR 的1.x 版本映射到2.1 和 2.2 .NET Core 版本，并具有相同的生存期。 对于版本1.x 和更高版本，SignalR 版本与 .NET 的其余部分完全匹配，并且具有相同的支持生命周期。

| SignalR 版本 | .NET Core 版本 | 支持级别 | 结束支持 |
| - | - | - | - |
| 1.0. x | 2.1.x | 长期支持 | 2021年8月21日 |
| 1.1. x | 2.2. x | 生命周期结束 | 2019年12月23日 |
| 3. x 或更高版本 | *与 SignalR 版本相同* | 请参阅[.Net Core 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) |

**注意：** 在 ASP.NET Core 3.0 中，JavaScript 客户端*移*到 `@microsoft/signalr` npm 包。

## <a name="feature-distribution"></a>功能分发

下表显示提供实时支持的客户端的功能和支持。 对于每项功能，将列出支持此功能的*最低*版本。 如果未列出任何版本，则不支持此功能。

| 功能 | Server (服务器) | .NET 客户端 | JavaScript 客户端 | Java 客户端 |
| ---- | :-: | :-: | :-: | :-: |
| Azure SignalR 服务支持 |2.1.0|1.0.0|1.0.0|1.0.0|
| [服务器到客户端流式处理](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [客户端到服务器的流式处理](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| 自动重新连接（[.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)， [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients)）          |3.0.0|3.0.0|3.0.0|❌|
| Websocket 传输 |2.1.0|1.0.0|1.0.0|1.0.0|
| 服务器发送的事件传输 |2.1.0|1.0.0|1.0.0|❌|
| 长轮询传输 |2.1.0|1.0.0|1.0.0|3.0.0|
| JSON 集线器协议 |2.1.0|1.0.0|1.0.0|1.0.0|
| MessagePack 中心协议 |2.1.0|1.0.0|1.0.0|❌|

[我们的问题跟踪](https://github.com/dotnet/AspNetCore/issues)程序中跟踪了对启用其他客户端功能的支持。

## <a name="additional-resources"></a>其他资源

* [SignalRASP.NET Core 入门](xref:tutorials/signalr)
* [支持的平台](xref:signalr/supported-platforms)
* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
