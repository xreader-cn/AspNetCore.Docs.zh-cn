---
title: SignalR 和 ASP.NET Core SignalR 之间的差异
author: bradygaster
description: SignalR 和 ASP.NET Core SignalR 之间的差异
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 091fc44fccf820a394e7c6f775700c85bebc9101
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892024"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>ASP.NET SignalR 和 ASP.NET Core SignalR 之间的差异

ASP.NET Core SignalR 与 ASP.NET SignalR 的客户端或服务器不兼容。 本文详细介绍 ASP.NET Core SignalR 中已删除或已更改的功能。

## <a name="how-to-identify-the-signalr-version"></a>如何识别 SignalR 版本

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| 服务器 NuGet 包 | [Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| 客户端 NuGet 包 | [Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| 客户端 npm 包 | [signalr](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Java 客户端 | [GitHub 存储库](https://github.com/SignalR/java-client)（已弃用）  | Maven 包[com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| 服务器应用类型 | ASP.NET (System.Web) 或 OWIN 自承载 | ASP.NET Core |
| 受支持的服务器平台 | .NET framework 4.5 或更高版本 | .NET Framework 4.6.1 或更高版本<br>.NET core 2.1 或更高版本 |

## <a name="feature-differences"></a>功能差异

### <a name="automatic-reconnects"></a>自动重新连接

ASP.NET Core SignalR 不支持自动重新连接。 如果客户端已断开连接，则用户必须显式启动新连接才能重新连接。 在 ASP.NET SignalR 中，如果连接断开，SignalR 会尝试重新连接到服务器。 

### <a name="protocol-support"></a>协议支持

ASP.NET Core SignalR 支持 JSON 以及一种基于 [MessagePack](xref:signalr/messagepackhubprotocol) 的新二进制协议。 此外，还可创建自定义协议。

### <a name="transports"></a>传输

ASP.NET Core SignalR 不支持永久帧传输。

## <a name="differences-on-the-server"></a>服务器上的差异

ASP.NET Core SignalR 服务器端库包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，该包属于适用于 Razor 和 MVC 项目的 **ASP.NET Core Web 应用程序**模板。

ASP.NET Core SignalR 是一个 ASP.NET Core 中间件，因此必须通过在 `Startup.ConfigureServices` 中调用 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 来配置它。

```csharp
services.AddSignalR()
```

若要配置路由，请在 `Startup.Configure` 方法中将路由映射到 [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) 方法调用内的中心。

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a>粘滞会话

ASP.NET SignalR 的横向扩展模型允许客户端重新连接场中的任何服务器并将消息发送到这些服务器。 在 ASP.NET Core SignalR 中，客户端必须在连接期间与同一服务器进行交互。 对于使用 Redis 的横向扩展，这意味着需要粘滞会话。 对于使用 [Azure SignalR 服务](/azure/azure-signalr/)的横向扩展，则不需要粘滞会话，因为该服务会处理与客户端的连接。 

### <a name="single-hub-per-connection"></a>一个连接一个中心

在ASP.NET Core SignalR中，连接模型已经简化。 可直接连接到单个中心，不必通过单个连接来共享对多个中心的访问。

### <a name="streaming"></a>流式处理

ASP.NET Core SignalR 现在支持从中心[流式传输数据](xref:signalr/streaming)到客户端。

### <a name="state"></a>状态

已删除在客户端和中心之间传递任意状态（通常称为 HubState）的功能以及对进度消息的支持。 目前没有中心代理的对应项。

### <a name="persistentconnection-removal"></a>删除 PersistentConnection

在 ASP.NET Core SignalR 中，[PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) 类已删除。 

### <a name="globalhost"></a>GlobalHost

ASP.NET Core 在框架中内置了依赖关系注入 (DI)。 服务可以使用 DI 来访问 [HubContext](xref:signalr/hubcontext)。 ASP.NET CoreR SignalR 中不存在用于在 ASP.NET SignalR 中获取 `HubContext` 的 `GlobalHost` 对象。

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR 没有对 `HubPipeline` 模块的支持。

## <a name="differences-on-the-client"></a>客户端上的差异

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalR 客户端以 [TypeScript](https://www.typescriptlang.org/) 编写。 使用 [JavaScript 客户端](xref:signalr/javascript-client)时，可以以 JavaScript 或 TypeScript 编写。

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>JavaScript 客户端托管在 [npm](https://www.npmjs.com/) 上

在以前的版本中，JavaScript 客户端是通过 Visual Studio 中的 NuGet 包获取的。 Core 版本的 [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm 包包含 JavaScript 库。 此包不包括在**ASP.NET Core Web 应用程序**模板。 使用 npm 获取并安装 `@aspnet/signalr` npm 包。

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

已删除对 jQuery 的依赖关系，但项目仍然可以使用 jQuery。

### <a name="internet-explorer-support"></a>Internet Explorer 支持

ASP.NET Core SignalR 需要 Microsoft Internet Explorer 11 或更高版本（ASP.NET SignalR 支持 Microsoft Internet Explorer 8 及更高版本）。

### <a name="javascript-client-method-syntax"></a>JavaScript 客户端方法语法

JavaScript 语法已与 Signalr 早期版本中的相应语法不同。 请使用 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API 而非 `$connection` 对象创建连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

使用 [on](/javascript/api/@aspnet/signalr/HubConnection#on) 方法指定可供中心调用的客户端方法。

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

创建客户端方法后，启动中心连接。 链接一种 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 方法以记录或处理错误。

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>中心代理

中心代理不再自动生成。 与之相反，方法名称将以字符串形式传递到 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API 中。

### <a name="net-and-other-clients"></a>.NET 和其他客户端

`Microsoft.AspNetCore.SignalR.Client` NuGet 包中包含 ASP.NET Core SignalR 的 .NET 客户端库。

使用 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) 创建和生成到中心的连接的实例。

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>横向扩展差异

ASP.NET SignalR 支持 SQL Server 和 Redis。 ASP.NET Core SignalR 支持 Azure SignalR 服务和 Redis。

### <a name="aspnet"></a>ASP.NET

* [使用 Azure 服务总线的 SignalR 横向扩展](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [使用 Redis 的 SignalR 横向扩展](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [使用 SQL Server 的 SignalR 横向扩展](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR 服务](/azure/azure-signalr/)

## <a name="additional-resources"></a>其他资源

* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
* [.NET 客户端](xref:signalr/dotnet-client)
* [支持的平台](xref:signalr/supported-platforms)
