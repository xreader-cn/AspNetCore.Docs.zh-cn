---
uid: signalr/overview/getting-started/supported-platforms
title: 支持的平台 |Microsoft Docs
author: bradygaster
description: 本文介绍通过 SignalR 支持哪些客户端和服务器。
ms.author: bradyg
ms.date: 04/18/2018
ms.assetid: eac31beb-0f46-4afa-9def-e80904dea4f0
msc.legacyurl: /signalr/overview/getting-started/supported-platforms
msc.type: authoredcontent
ms.openlocfilehash: 60fa74b54797efbe14ba525160b2f750a4f5a451
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836046"
---
<a name="supported-platforms"></a>支持的平台
====================
通过[Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文介绍通过 SignalR 支持哪些客户端和服务器。 
> 
> ## <a name="questions-and-comments"></a>问题和提出的意见
> 
> 请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。

SignalR 支持到不同的服务器和客户端配置。 此外，每个传输选项具有一组其自身; 的要求如果传输的系统要求不可用，SignalR 会顺利地实现故障转移到其他传输。 SignalR 支持的传输的详细信息，请参阅[传输和回退](introduction-to-signalr.md#transports)。

## <a name="server-system-requirements"></a>服务器系统要求

SignalR 服务器组件可以驻留在不同的服务器配置。 本部分介绍受支持的版本的操作系统，.NET framework、 Internet Information Server 和其他组件。

### <a name="supported-server-operating-systems"></a>支持的服务器操作系统

SignalR 服务器组件可以托管在以下服务器或客户端操作系统。 请注意，对于使用 Websocket 的 SignalR，Windows Server 2012、 Windows Server 2016 或 Windows 8 需 （WebSocket 可以使用 Windows Azure 网站上，只要站点的.NET framework 版本设置为 4.5，并且在站点中启用 Web 套接字配置页）。

- Windows 2016 Server
- Windows Server 2012
- Windows Server 2008 r2
- Windows 10
- Windows 8
- Windows 7
- Microsoft Azure

### <a name="supported-server-net-framework-version"></a>受支持的服务器的.NET Framework 版本

SignalR 2 仅支持.NET Framework 4.5 上。 请参阅[推荐的更新](#updates)增强可靠性、 兼容性、 稳定性和性能的更新的部分。

### <a name="supported-server-iis-versions"></a>受支持的服务器的 IIS 版本

当在 IIS 中托管 SignalR 时，支持以下版本。 请注意，是否使用客户端操作系统，例如用于开发 （Windows 8 或 Windows 7） 的完整版本的 IIS 或 Cassini 不应使用，因为限制为 10 个并发连接的规定，这将非常快速地达到自连接以来暂时性故障，经常重新建立，并且不会释放，立即在不再使用时。 应在客户端操作系统上使用 IIS Express。

此外请注意，signalr 使用 WebSocket，必须使用 IIS 8 或 IIS 8 Express，服务器必须使用 Windows 8，Windows Server 2012 或更高版本，必须在 IIS 中启用 WebSocket。 有关如何启用 WebSocket 在 IIS 中的信息，请参阅[IIS 8.0 WebSocket 协议支持](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support)。

- IIS 8 IIS Express。
- IIS 7 和 7.5。 为支持[无扩展名 Url](https://support.microsoft.com/kb/980368)是必需的。
- IIS 必须运行在集成模式下;不支持经典模式。 如果在使用服务器发送事件传输的经典模式下运行 IIS，则可能遇到的最多 30 秒的消息延迟。
- 托管应用程序必须在完全信任模式下运行。

## <a name="client-system-requirements"></a>客户端系统要求

SignalR 可在多个客户端平台。 本部分介绍使用 SignalR 在 web 浏览器、 Windows 桌面应用程序、 Silverlight 应用程序和移动设备的系统要求。

### <a name="web-browsers"></a>Web 浏览器

SignalR 可在各种 web 浏览器，但通常情况下，支持的最新两个版本。

使用 SignalR 在浏览器中的应用程序必须使用 jQuery 版本 1.6.4 或主要更高版本 （如 1.7.2、 1.8.2 或 1.9.1）。

可以在以下浏览器中使用 SignalR:

- Microsoft Internet Explorer 版本 8、 9、 10 和 11。 新式、 桌面和移动版支持。
- Mozilla Firefox： 当前版本-1，Windows 和 Mac 版本。
- Google Chrome： 当前版本-1，Windows 和 Mac 版本。
- Safari： 当前版本-1，Mac 和 iOS 版本。
- Opera： 当前版本-1，仅 Windows。
- Android 浏览器

除了需要某些浏览器，SignalR 使用的各种传输具有其自己的要求。 下列传输支持在以下配置：

<a id="browser"></a>

**Web 浏览器传输要求**

| 传输 | Internet Explorer | Chrome （Windows 或 iOS） | Firefox | Safari （OSX 或 iOS） | Android |
| --- | --- | --- | --- | --- | --- |
| WebSockets | 10+ | 当前值-1 | 当前值-1 | 当前值-1 | 不可用 |
| 服务器发送事件 | 不可用 | 当前值-1 | 当前值-1 | 当前值-1 | 不可用 |
| ForeverFrame | 8+ | 不可用 | 不可用 | 不可用 | 4.1 |
| 长轮询 | 8+ | 当前值-1 | 当前值-1 | 当前值-1 | 4.1 |

\*：6 + 所需的全部功能。

#### <a name="unsupported-browsers"></a>不受支持的浏览器

尽管 SignalR*可能*运行而无需较旧的浏览器版本中的主要问题，我们不要主动进行测试，SignalR 中它们，并通常不修复可能会出现在它们的 bug。

### <a name="windows-desktop-and-silverlight-applications"></a>Windows 桌面和 Silverlight 应用程序

除了在 web 浏览器中运行，可以在独立的 Windows 客户端或 Silverlight 应用程序中托管 SignalR。 Windows 桌面和 Silverlight SignalR 应用程序具有以下系统要求。

- Windows XP SP3 或更高版本支持使用.NET 4 应用程序。
- Windows Vista 或更高版本支持使用.NET Framework 4.5 的应用程序。

除了操作系统和.NET framework 要求，可与 SignalR 的传输具有其自己的要求。 下列传输支持在以下配置：

**Windows 桌面和 Silverlight 传输要求**

| 传输 | .NET 应用程序 | Silverlight |
| --- | --- | --- |
| Web 套接字 | Windows 8 + 和.NET 4.5 + | 不可用 |
| 永久帧 | 不可用 | 不可用 |
| 服务器发送事件 | .NET 4+ | 5+ |
| 长轮询 | .NET 4+ | 5+ |

<a id="android"></a>

### <a name="windows-store-and-windows-phone-applications"></a>Windows 应用商店和 Windows Phone 应用程序

可以在 Windows 应用商店应用程序和 Windows Phone 8 应用程序使用 SignalR。 下列传输支持在以下配置：

**Windows 应用商店和 Windows Phone 传输要求**

| 传输 | Windows 应用商店 /.NET | Windows 应用商店 / JavaScript | Windows Phone/ IE | Windows Phone/ .NET |
| --- | --- | --- | --- | --- |
| WebSockets | 不可用 | Win8+ | 8+ | 不可用 |
| 永久帧 | 不可用 | Win8+ | 7.5+ | 不可用 |
| 服务器发送事件 | Win8+ | 不可用 | 不可用 | 8+ |
| 长轮询 | Win8+ | Win8+ | 7.5+ | 8+ |

<a id="updates"></a>

## <a name="recommended-updates"></a>建议的更新

SignalR 服务器建议以下更新：

- 有可用的更新的.NET Framework 4.5[此处](https://support.microsoft.com/kb/2750149)。
- 对于 ASP.NET，Microsoft 将定期发布 Qfe。 这些应应用为可用。
