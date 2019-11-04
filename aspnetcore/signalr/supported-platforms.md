---
title: ASP.NET Core SignalR 支持的平台
author: bradygaster
description: 了解 ASP.NET Core SignalR 的受支持平台。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2019
uid: signalr/supported-platforms
ms.openlocfilehash: 1be7a307710e6e522c0088fd1ca01da11a13eda1
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426974"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>ASP.NET Core SignalR 支持的平台

## <a name="server-system-requirements"></a>服务器系统要求

SignalR for ASP.NET Core 支持 ASP.NET Core 支持的任何服务器平台。

## <a name="javascript-client"></a>JavaScript 客户端

[JavaScript 客户端](https://www.npmjs.com/package/@aspnet/signalr)在 NodeJS 8 及更高版本以及以下浏览器上运行：

| 浏览者                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | 当前&dagger; |
| Mozilla Firefox                 | 当前&dagger; |
| Google Chrome;包括 Android | 当前&dagger; |
| 免费包括 iOS            | 当前&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*当前*指的是浏览器的最新版本。

## <a name="net-client"></a>.NET 客户端

[.Net 客户端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)在 ASP.NET Core 支持的任何平台上运行。 例如， [xamarin 开发人员可以使用 SignalR](https://github.com/aspnet/Announcements/issues/305)通过 xamarin 8.4.0.1 和更高版本以及使用 xamarin 11.14.0.4 和更高版本的 ios 应用来构建 Android 应用程序。

如果服务器运行 IIS，则 Websocket 传输要求在 Windows Server 2012 或更高版本上安装 IIS 8.0 或更高版本。 所有平台都支持其他传输。

## <a name="java-client"></a>Java 客户端

[Java 客户端](https://search.maven.org/artifact/com.microsoft.aspnet/signalr)支持 java 8 及更高版本。

## <a name="unsupported-clients"></a>不受支持的客户端

以下客户端可用，但为试验或非正式客户端。 它们目前不受支持，可能永远不会。

* [C++机](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Swift 客户端](https://github.com/moozzyk/SignalR-Client-Swift)
