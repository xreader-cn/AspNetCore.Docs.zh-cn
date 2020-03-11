---
title: SignalR 和 ASP.NET Core 之间的差异 SignalR
author: bradygaster
description: SignalR 和 ASP.NET Core 之间的差异 SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: cca9a0cb0c46fc25eb5d1f7127d31fd3ab92f0b4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653532"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>ASP.NET SignalR 和 ASP.NET Core SignalR 之间的差异

ASP.NET Core SignalR 与 ASP.NET SignalR 的客户端或服务器不兼容。 本文详细介绍 ASP.NET Core SignalR 中已删除或已更改的功能。

## <a name="how-to-identify-the-signalr-version"></a>如何识别 SignalR 版本

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| 服务器 NuGet 包 | [SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | 无。 包含在[AspNetCore](xref:fundamentals/metapackage-app)共享框架中。 |
| 客户端 NuGet 包 | [SignalR。](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[SignalR）](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore. SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript 客户端 npm 包 | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Java 客户端 | [GitHub 存储库](https://github.com/SignalR/java-client)（已弃用）  | Maven 包[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| 服务器应用类型 | ASP.NET (System.Web) 或 OWIN 自承载 | ASP.NET Core |
| 支持的服务器平台 | .NET framework 4.5 或更高版本 | .NET Core 3.0 或更高版本 |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| 服务器 NuGet 包 | [SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) （.net Core）<br>[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| 客户端 NuGet 包 | [SignalR。机](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[SignalR。JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [AspNetCore.SignalR。机](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript 客户端 npm 包 | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Java 客户端 | [GitHub 存储库](https://github.com/SignalR/java-client)（已弃用）  | Maven 包[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| 服务器应用类型 | ASP.NET (System.Web) 或 OWIN 自承载 | ASP.NET Core |
| 支持的服务器平台 | .NET framework 4.5 或更高版本 | .NET Framework 4.6.1 或更高版本<br>.NET core 2.1 或更高版本 |

::: moniker-end

## <a name="feature-differences"></a>功能差异

### <a name="automatic-reconnects"></a>自动重新连接

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET 中 SignalR：

* 默认情况下，如果连接被删除，SignalR 会尝试重新连接到服务器。 

在 ASP.NET Core SignalR：

* 自动重新连接同时选择[.net 客户](xref:signalr/dotnet-client#automatically-reconnect)[端和 JavaScript 客户端](xref:signalr/javascript-client#automatically-reconnect)：

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 3.0 之前，SignalR 不支持自动重新连接。 如果客户端已断开连接，用户必须显式启动新连接才能重新连接。 在 ASP.NET SignalR中，SignalR 在断开连接时尝试重新连接到服务器。

::: moniker-end

### <a name="protocol-support"></a>协议支持

ASP.NET Core SignalR 支持 JSON，以及基于[MessagePack](xref:signalr/messagepackhubprotocol)的新二进制协议。 此外，还可创建自定义协议。

### <a name="transports"></a>传输

ASP.NET Core SignalR不支持永久帧传输。

## <a name="differences-on-the-server"></a>服务器上的差异

ASP.NET Core SignalR 服务器端库包含在[AspNetCore](xref:fundamentals/metapackage-app)中，该应用程序用于 RAZOR 和 MVC 项目的**ASP.NET Core Web 应用程序**模板。

ASP.NET Core SignalR 是 ASP.NET Core 中间件。 必须通过在 `Startup.ConfigureServices`中调用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> 来配置它。

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

若要配置路由，请将路由映射到 `Startup.Configure` 方法中 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 方法调用内的中心。

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

若要配置路由，请将路由映射到 `Startup.Configure` 方法中 <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> 方法调用内的中心。

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>粘滞会话

ASP.NET SignalR 的扩展模型使客户端可以重新连接到场中的任何服务器并向其发送消息。 在 ASP.NET Core SignalR中，客户端必须在连接期间与同一服务器交互。 对于使用 Redis 的横向扩展，这意味着需要粘滞会话。 对于使用[Azure SignalR 服务](/azure/azure-signalr/)的扩展，无需使用粘滞会话，因为服务会处理与客户端的连接。

### <a name="single-hub-per-connection"></a>一个连接一个中心

在 ASP.NET Core SignalR中，连接模型已简化。 可直接连接到单个中心，而不是连接到用于共享对多个中心的访问的单个连接。

### <a name="streaming"></a>流式处理

ASP.NET Core SignalR 现在支持从中心到客户端的[流数据](xref:signalr/streaming)。

### <a name="state"></a>状态

能够在客户端和中心之间传递任意状态（通常称为 `HubState`），并支持进度消息。 目前没有中心代理的对应项。

### <a name="persistentconnection-removal"></a>删除 PersistentConnection

在 ASP.NET Core SignalR中，已删除[PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))类。

### <a name="globalhost"></a>GlobalHost

ASP.NET Core 在框架中内置了依赖关系注入 (DI)。 服务可以使用 DI 来访问[HubContext](xref:signalr/hubcontext)。 在 ASP.NET SignalR 中用于获取 `HubContext` 的 `GlobalHost` 对象在 ASP.NET Core SignalR中不存在。

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR 不支持 `HubPipeline` 模块。

## <a name="differences-on-the-client"></a>客户端上的差异

### <a name="typescript"></a>TypeScript

SignalR 客户端 ASP.NET Core 是以[TypeScript](https://www.typescriptlang.org/)编写的。 使用[javascript 客户端](xref:signalr/javascript-client)时，可以使用 Javascript 或 TypeScript 来编写。

### <a name="the-javascript-client-is-hosted-at-npm"></a>JavaScript 客户端托管在 npm

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET 版本中，JavaScript 客户端通过 Visual Studio 中的 NuGet 包获得。 在 ASP.NET Core 版本中， [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 包包含 JavaScript 库。 此包不包含在**ASP.NET Core Web 应用程序**模板中。 使用 npm 获取并安装 `@microsoft/signalr` npm 包。

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

在 ASP.NET 版本中，JavaScript 客户端通过 Visual Studio 中的 NuGet 包获得。 在 ASP.NET Core 版本中， [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 包包含 JavaScript 库。 此包不包含在**ASP.NET Core Web 应用程序**模板中。 使用 npm 获取并安装 `@aspnet/signalr` npm 包。

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

已删除对 jQuery 的依赖关系，但项目仍然可以使用 jQuery。

### <a name="internet-explorer-support"></a>Internet Explorer 支持

ASP.NET Core SignalR 需要 Microsoft Internet Explorer 11 或更高版本（ASP.NET SignalR 支持的 Microsoft Internet Explorer 8 及更高版本）。

### <a name="javascript-client-method-syntax"></a>JavaScript 客户端方法语法

::: moniker range=">= aspnetcore-3.0"

JavaScript 语法已在 SignalR的 ASP.NET 版本中发生了更改。 不要使用 `$connection` 对象，而是使用[HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 创建连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

使用[on](/javascript/api/@microsoft/signalr/HubConnection#on)方法可指定中心可调用的客户端方法。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

JavaScript 语法已在 SignalR的 ASP.NET 版本中发生了更改。 不要使用 `$connection` 对象，而是使用[HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 创建连接。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

使用[on](/javascript/api/@aspnet/signalr/HubConnection#on)方法可指定中心可调用的客户端方法。

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

创建客户端方法后，启动中心连接。 将[catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)方法链接到日志或处理错误。

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>中心代理

::: moniker range=">= aspnetcore-3.0"

中心代理不再自动生成。 相反，方法名称将作为字符串传递到[调用](/javascript/api/@microsoft/signalr/hubconnection#invoke)API。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

中心代理不再自动生成。 相反，方法名称将作为字符串传递到[调用](/javascript/api/@aspnet/signalr/hubconnection#invoke)API。

::: moniker-end

### <a name="net-and-other-clients"></a>.NET 和其他客户端

[AspNetCore.SignalR。客户端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet 包包含用于 ASP.NET Core SignalR的 .net 客户端库。

使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 创建和生成与集线器的连接的实例。

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>横向扩展差异

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
