---
title: ASP.NET Core SignalR 支持的平台
author: bradygaster
description: 了解 ASP.NET Core SignalR的支持平台。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 9b9cf1d57d61c333c485f23b7ab952c66814d2aa
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317469"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a>ASP.NET Core SignalR 支持的平台

## <a name="server-system-requirements"></a>服务器系统要求

ASP.NET Core SignalR 支持 ASP.NET Core 支持的任何服务器平台。

## <a name="javascript-client"></a>JavaScript 客户端

[JavaScript 客户端](xref:signalr/javascript-client)需要NodeJS 8或更高版本和以下浏览器上运行：

| 浏览者                         | 版本         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | 当前&dagger; |
| Mozilla Firefox                 | 当前&dagger; |
| Google Chrome;包括 Android | 当前&dagger; |
| 免费包括 iOS            | 当前&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*当前*指的是浏览器的最新版本。

## <a name="net-client"></a>.NET 客户端

[.NET 客户端](xref:signalr/dotnet-client)可以在 ASP.NET Core 支持的任何平台上运行。 例如， [xamarin 开发人员可以使用 SignalR](https://github.com/aspnet/Announcements/issues/305)来使用 xamarin 8.4.0.1 和更高版本以及使用 xamarin 11.14.0.4 和更高版本的 ios 应用程序构建 Android 应用程序。

如果服务器运行 IIS，则 Websocket 传输要求在 Windows Server 2012 或更高版本上安装 IIS 8.0 或更高版本。 其他传输在所有平台上都受支持。

## <a name="java-client"></a>Java 客户端

[Java 客户端](xref:signalr/java-client)支持 Java 8 或更高版本。

## <a name="unsupported-clients"></a>不受支持的客户端

以下客户端可用，但是是实验性的或非官方。 它们目前不支持，可能永远不会。

* [C++机](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Swift 客户端](https://github.com/moozzyk/SignalR-Client-Swift)
