---
title: SignalR 和 ASP.NET Core 之间的差异 SignalR
author: bradygaster
description: SignalR 和 ASP.NET Core 之间的差异 SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 0f644c132b0fcf9a0ecf0ab181791a6477c97f76
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963724"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a>ASP.NET SignalR 和 ASP.NET Core 之间的差异 SignalR

ASP.NET Core SignalR 与 ASP.NET SignalR的客户端或服务器不兼容。 本文详细介绍了 ASP.NET Core SignalR中已删除或更改的功能。

## <a name="how-to-identify-the-opno-locsignalr-version"></a>如何识别 SignalR 版本

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| 服务器 NuGet 包 | [SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) （.net Core）<br>[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) （.NET Framework） |
| 客户端 NuGet 包 | [SignalR。机](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[SignalR。JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore.SignalR。机](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| 客户端 npm 包 | [signalr](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Java 客户端 | [GitHub 存储库](https://github.com/SignalR/java-client)（已弃用）  | Maven 包[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| 服务器应用类型 | ASP.NET （System.web）或 OWIN 自承载 | ASP.NET Core |
| 支持的服务器平台 | .NET Framework 4.5 或更高版本 | .NET Framework 4.6.1 或更高版本<br>.NET Core 2.1 或更高版本 |

## <a name="feature-differences"></a>功能差异

### <a name="automatic-reconnects"></a>自动重新连接

ASP.NET Core SignalR不支持自动重新连接。 如果客户端已断开连接，则用户必须显式启动新连接（如果要重新连接）。 在 ASP.NET SignalR中，SignalR 在断开连接时尝试重新连接到服务器。

### <a name="protocol-support"></a>协议支持

ASP.NET Core SignalR 支持 JSON，以及基于[MessagePack](xref:signalr/messagepackhubprotocol)的新二进制协议。 此外，还可以创建自定义协议。

### <a name="transports"></a>传输

ASP.NET Core SignalR不支持永久帧传输。

## <a name="differences-on-the-server"></a>服务器上的差异

SignalR 服务器端库的 ASP.NET Core 包含在[元包](xref:fundamentals/metapackage-app)包中，后者是 RAZOR 和 MVC 项目的**ASP.NET Core Web 应用程序**模板的一部分。

ASP.NET Core SignalR 是 ASP.NET Core 中间件，所以必须通过在 `Startup.ConfigureServices`中调用[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)进行配置。

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

若要配置路由，请将路由映射到 `Startup.Configure` 方法中的[UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints)方法调用内的中心。


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

若要配置路由，请将路由映射到 `Startup.Configure` 方法中的[UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr)方法调用内的中心。

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>粘滞会话

ASP.NET SignalR 的扩展模型使客户端可以重新连接到场中的任何服务器并向其发送消息。 在 ASP.NET Core SignalR中，客户端必须在连接期间与同一服务器交互。 对于使用 Redis 的扩展，这意味着需要使用粘滞会话。 对于使用[Azure SignalR 服务](/azure/azure-signalr/)的扩展，无需使用粘滞会话，因为服务会处理与客户端的连接。

### <a name="single-hub-per-connection"></a>单个集线器每个连接

在 ASP.NET Core SignalR中，连接模型已简化。 直接连接到单个集线器，而不是使用单个连接来共享对多个中心的访问。

### <a name="streaming"></a>流式处理

ASP.NET Core SignalR 现在支持从中心到客户端的[流数据](xref:signalr/streaming)。

### <a name="state"></a>状态

可在客户端和中心之间传递任意状态的功能（通常称为 HubState）已删除，并支持进度消息。 目前没有集线器代理。

### <a name="persistentconnection-removal"></a>删除 PersistentConnection

在 ASP.NET Core SignalR中，已删除[PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))类。

### <a name="globalhost"></a>GlobalHost

ASP.NET Core 在框架中内置了依赖关系注入（DI）。 服务可以使用 DI 来访问[HubContext](xref:signalr/hubcontext)。 在 ASP.NET SignalR 中用于获取 `HubContext` 的 `GlobalHost` 对象在 ASP.NET Core SignalR中不存在。

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR 不支持 `HubPipeline` 模块。

## <a name="differences-on-the-client"></a>与客户端之间的差异

### <a name="typescript"></a>TypeScript

SignalR 客户端 ASP.NET Core 是以[TypeScript](https://www.typescriptlang.org/)编写的。 使用[javascript 客户端](xref:signalr/javascript-client)时，可以使用 Javascript 或 TypeScript 来编写。

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>JavaScript 客户端托管在[npm](https://www.npmjs.com/)

在以前的版本中，JavaScript 客户端通过 Visual Studio 中的 NuGet 包获得。 对于核心版本， [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm 包包含 JavaScript 库。 此包不包含在**ASP.NET Core Web 应用程序**模板中。 使用 npm 获取并安装 `@aspnet/signalr` npm 包。

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

已移除 jQuery 的依赖项，但项目仍可使用 jQuery。

### <a name="internet-explorer-support"></a>Internet Explorer 支持

ASP.NET Core SignalR 需要 Microsoft Internet Explorer 11 或更高版本（ASP.NET SignalR 支持的 Microsoft Internet Explorer 8 及更高版本）。

### <a name="javascript-client-method-syntax"></a>JavaScript 客户端方法语法

JavaScript 语法已与 SignalR的以前版本发生了更改。 不要使用 `$connection` 对象，而是使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API 创建连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

使用[on](/javascript/api/@aspnet/signalr/HubConnection#on)方法可指定中心可调用的客户端方法。

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

创建客户端方法后，启动集线器连接。 将[catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)方法链接到日志或处理错误。

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>中心代理

中心代理不再自动生成。 相反，方法名称将作为字符串传递到[调用](/javascript/api/%40aspnet/signalr/hubconnection#invoke)API。

### <a name="net-and-other-clients"></a>.NET 和其他客户端

`Microsoft.AspNetCore.SignalR.Client` NuGet 包包含用于 ASP.NET Core SignalR的 .NET 客户端库。

使用[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)创建和生成与集线器的连接的实例。

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>扩展差异

ASP.NET SignalR 支持 SQL Server 和 Redis。 ASP.NET Core SignalR 支持 Azure SignalR 服务和 Redis。

### <a name="aspnet"></a>ASP.NET

* [与 Azure 服务总线 SignalR 扩展](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR Redis 的扩展](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR 扩展与 SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR 服务](/azure/azure-signalr/)
* [Redis 底板](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>其他资源

* [中心](xref:signalr/hubs)
* [JavaScript 客户端](xref:signalr/javascript-client)
* [.NET 客户端](xref:signalr/dotnet-client)
* [支持的平台](xref:signalr/supported-platforms)
