---
title: ASP.NET Core SignalR 支持的平台
author: bradygaster
description: 了解 ASP.NET Core SignalR 支持的平台。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/06/2019
uid: signalr/supported-platforms
ms.openlocfilehash: fefaaf97de3f1fabf8f3154bf5b4ccb37195ccff
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068217"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>ASP.NET Core SignalR 支持的平台

## <a name="server-system-requirements"></a>服务器系统要求

ASP.NET Core SignalR 适用于 ASP.NET Core 支持的任何服务器平台。

## <a name="javascript-client"></a>JavaScript 客户端

[JavaScript 客户端](https://www.npmjs.com/package/@aspnet/signalr)需要NodeJS 8或更高版本和以下浏览器上运行：

| 浏览者                         | 版本 |
| ------------------------------- | ------- |
| Microsoft Edge                  | 当前 |
| Mozilla Firefox                 | 当前 |
| Google Chrome;包括 Android | 当前 |
| Safari;包含 iOS            | 当前 |
| Microsoft Internet Explorer     | 11      |
 
## <a name="net-client"></a>.NET 客户端

[.NET 客户端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)可以在 ASP.NET Core 支持的任何平台上运行。 例如， [Xamarin 开发人员可以使用 SignalR](https://github.com/aspnet/Announcements/issues/305)用于构建 Android 应用程序使用 Xamarin.Android 8.4.0.1 或更高版本和 iOS 应用程序使用 Xamarin.iOS 11.14.0.4 或更高版本。

如果服务器运行 IIS，Websocket 传输要求安装 IIS 8.0 或更高版本在 Windows Server 2012 或更高版本。 其他传输在所有平台上都受支持。

## <a name="java-client"></a>Java 客户端

[Java 客户端](https://search.maven.org/artifact/com.microsoft.aspnet/signalr)支持 Java 8 或更高版本。

## <a name="unsupported-clients"></a>不受支持的客户端

以下客户端可用，但是是实验性的或非官方。 它们目前不支持，可能永远不会。

* [C++客户端](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Swift 的客户端](https://github.com/moozzyk/SignalR-Client-Swift)
