---
title: 'SignalR和 ASP.NET Core 之间的差异SignalR'
author: bradygaster
description: 'SignalR和 ASP.NET Core 之间的差异SignalR'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/version-differences
ms.openlocfilehash: c4c0ff83cb789e9aa35085496daa461404615726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061205"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="71d2f-103">ASP.NET SignalR 与 ASP.NET Core 之间的差异 SignalR</span><span class="sxs-lookup"><span data-stu-id="71d2f-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="71d2f-104">ASP.NET Core SignalR 与 ASP.NET 的客户端或服务器不兼容 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="71d2f-105">本文详细介绍了 ASP.NET Core 中已删除或更改的功能 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="71d2f-106">如何标识 SignalR 版本</span><span class="sxs-lookup"><span data-stu-id="71d2f-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="71d2f-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="71d2f-107">ASP.NET SignalR</span></span> | <span data-ttu-id="71d2f-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="71d2f-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="71d2f-109">**服务器 NuGet 包**</span><span class="sxs-lookup"><span data-stu-id="71d2f-109">**Server NuGet package**</span></span> | <span data-ttu-id="71d2f-110">[Microsoft。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="71d2f-111">无。</span><span class="sxs-lookup"><span data-stu-id="71d2f-111">None.</span></span> <span data-ttu-id="71d2f-112">包含在 [AspNetCore](xref:fundamentals/metapackage-app) 共享框架中。</span><span class="sxs-lookup"><span data-stu-id="71d2f-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="71d2f-113">**客户端 NuGet 包**</span><span class="sxs-lookup"><span data-stu-id="71d2f-113">**Client NuGet packages**</span></span> | <span data-ttu-id="71d2f-114">[Microsoft SignalR 。机](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="71d2f-115">[Microsoft SignalR 。JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="71d2f-116">[AspNetCore SignalR 。机](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="71d2f-117">**JavaScript 客户端 npm 包**</span><span class="sxs-lookup"><span data-stu-id="71d2f-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="71d2f-118">signalr</span><span class="sxs-lookup"><span data-stu-id="71d2f-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="71d2f-119">**Java 客户端**</span><span class="sxs-lookup"><span data-stu-id="71d2f-119">**Java client**</span></span> | <span data-ttu-id="71d2f-120">[GitHub 存储库](https://github.com/SignalR/java-client) (弃用) </span><span class="sxs-lookup"><span data-stu-id="71d2f-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="71d2f-121">Maven 包 [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="71d2f-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="71d2f-122">**服务器应用类型**</span><span class="sxs-lookup"><span data-stu-id="71d2f-122">**Server app type**</span></span> | <span data-ttu-id="71d2f-123">ASP.NET (system.web) 或 OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="71d2f-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="71d2f-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71d2f-124">ASP.NET Core</span></span> |
| <span data-ttu-id="71d2f-125">**支持的服务器平台**</span><span class="sxs-lookup"><span data-stu-id="71d2f-125">**Supported server platforms**</span></span> | <span data-ttu-id="71d2f-126">.NET Framework 4.5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71d2f-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="71d2f-127">.NET Core 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71d2f-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="71d2f-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="71d2f-128">ASP.NET SignalR</span></span> | <span data-ttu-id="71d2f-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="71d2f-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="71d2f-130">**服务器 NuGet 包**</span><span class="sxs-lookup"><span data-stu-id="71d2f-130">**Server NuGet package**</span></span> | <span data-ttu-id="71d2f-131">[Microsoft。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="71d2f-132">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) ( .net Core) </span><span class="sxs-lookup"><span data-stu-id="71d2f-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="71d2f-133">[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="71d2f-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="71d2f-134">(.NET Framework)</span></span> |
| <span data-ttu-id="71d2f-135">**客户端 NuGet 包**</span><span class="sxs-lookup"><span data-stu-id="71d2f-135">**Client NuGet packages**</span></span> | <span data-ttu-id="71d2f-136">[Microsoft SignalR 。机](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="71d2f-137">[Microsoft SignalR 。JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="71d2f-138">[AspNetCore SignalR 。机](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="71d2f-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="71d2f-139">**JavaScript 客户端 npm 包**</span><span class="sxs-lookup"><span data-stu-id="71d2f-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="71d2f-140">signalr</span><span class="sxs-lookup"><span data-stu-id="71d2f-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="71d2f-141">**Java 客户端**</span><span class="sxs-lookup"><span data-stu-id="71d2f-141">**Java client**</span></span> | <span data-ttu-id="71d2f-142">[GitHub 存储库](https://github.com/SignalR/java-client) (弃用) </span><span class="sxs-lookup"><span data-stu-id="71d2f-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="71d2f-143">Maven 包 [signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="71d2f-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="71d2f-144">**服务器应用类型**</span><span class="sxs-lookup"><span data-stu-id="71d2f-144">**Server app type**</span></span> | <span data-ttu-id="71d2f-145">ASP.NET (system.web) 或 OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="71d2f-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="71d2f-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71d2f-146">ASP.NET Core</span></span> |
| <span data-ttu-id="71d2f-147">**支持的服务器平台**</span><span class="sxs-lookup"><span data-stu-id="71d2f-147">**Supported server platforms**</span></span> | <span data-ttu-id="71d2f-148">.NET Framework 4.5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71d2f-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="71d2f-149">.NET Framework 4.6.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71d2f-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="71d2f-150">.NET Core 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="71d2f-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="71d2f-151">功能差异</span><span class="sxs-lookup"><span data-stu-id="71d2f-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="71d2f-152">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="71d2f-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71d2f-153">在 ASP.NET 中 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="71d2f-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="71d2f-154">默认情况下， SignalR 如果断开连接，则将尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="71d2f-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="71d2f-155">在 ASP.NET Core 中 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="71d2f-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="71d2f-156">自动重新连接同时选择[.net 客户](xref:signalr/dotnet-client#automatically-reconnect)[端和 JavaScript 客户端](xref:signalr/javascript-client#automatically-reconnect)：</span><span class="sxs-lookup"><span data-stu-id="71d2f-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71d2f-157">ASP.NET Core 3.0 之前， SignalR 不支持自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="71d2f-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="71d2f-158">如果客户端已断开连接，用户必须显式启动新连接才能重新连接。</span><span class="sxs-lookup"><span data-stu-id="71d2f-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="71d2f-159">SignalR如果断开连接，则在 ASP.NET 中 SignalR 尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="71d2f-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="71d2f-160">协议支持</span><span class="sxs-lookup"><span data-stu-id="71d2f-160">Protocol support</span></span>

<span data-ttu-id="71d2f-161">ASP.NET Core SignalR 支持 JSON，以及基于 [MessagePack](xref:signalr/messagepackhubprotocol)的新二进制协议。</span><span class="sxs-lookup"><span data-stu-id="71d2f-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="71d2f-162">此外，还可以创建自定义协议。</span><span class="sxs-lookup"><span data-stu-id="71d2f-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="71d2f-163">传输</span><span class="sxs-lookup"><span data-stu-id="71d2f-163">Transports</span></span>

<span data-ttu-id="71d2f-164">ASP.NET Core 中不支持永久帧传输 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="71d2f-165">服务器上的差异</span><span class="sxs-lookup"><span data-stu-id="71d2f-165">Differences on the server</span></span>

<span data-ttu-id="71d2f-166">ASP.NET Core 的 SignalR 服务器端库包含在 [AspNetCore](xref:fundamentals/metapackage-app)中，该应用程序用于和 MVC 项目的 **ASP.NET Core Web 应用程序** 模板 Razor 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="71d2f-167">ASP.NET Core SignalR 是 ASP.NET Core 的中间件。</span><span class="sxs-lookup"><span data-stu-id="71d2f-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="71d2f-168">必须通过在中调用来配置它 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71d2f-169">若要配置路由，请将路由映射到 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 方法中的方法调用内的中心 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="71d2f-170">若要配置路由，请将路由映射到 <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> 方法中的方法调用内的中心 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="71d2f-171">粘滞会话</span><span class="sxs-lookup"><span data-stu-id="71d2f-171">Sticky sessions</span></span>

<span data-ttu-id="71d2f-172">ASP.NET 的扩展模型 SignalR 允许客户端将消息重新连接到场中的任何服务器并向其发送消息。</span><span class="sxs-lookup"><span data-stu-id="71d2f-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="71d2f-173">在 ASP.NET Core 中 SignalR ，客户端必须在连接期间与同一服务器交互。</span><span class="sxs-lookup"><span data-stu-id="71d2f-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="71d2f-174">对于使用 Redis 的扩展，这意味着需要使用粘滞会话。</span><span class="sxs-lookup"><span data-stu-id="71d2f-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="71d2f-175">对于使用 [Azure SignalR 服务](/azure/azure-signalr/)的扩展，无需使用粘滞会话，因为服务会处理与客户端的连接。</span><span class="sxs-lookup"><span data-stu-id="71d2f-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="71d2f-176">单个集线器每个连接</span><span class="sxs-lookup"><span data-stu-id="71d2f-176">Single hub per connection</span></span>

<span data-ttu-id="71d2f-177">在 ASP.NET Core 中 SignalR ，连接模型已简化。</span><span class="sxs-lookup"><span data-stu-id="71d2f-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="71d2f-178">直接连接到单个集线器，而不是使用单个连接来共享对多个中心的访问。</span><span class="sxs-lookup"><span data-stu-id="71d2f-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="71d2f-179">流式处理</span><span class="sxs-lookup"><span data-stu-id="71d2f-179">Streaming</span></span>

<span data-ttu-id="71d2f-180">ASP.NET Core SignalR 现在支持从中心到客户端的 [流数据](xref:signalr/streaming) 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="71d2f-181">状态</span><span class="sxs-lookup"><span data-stu-id="71d2f-181">State</span></span>

<span data-ttu-id="71d2f-182">能够在客户端和中心之间传递任意状态 (经常称为 `HubState`) ，并支持进度消息。</span><span class="sxs-lookup"><span data-stu-id="71d2f-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="71d2f-183">目前没有集线器代理。</span><span class="sxs-lookup"><span data-stu-id="71d2f-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="71d2f-184">删除 PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="71d2f-184">PersistentConnection removal</span></span>

<span data-ttu-id="71d2f-185">在 ASP.NET Core 中 SignalR ，已删除 [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) 类。</span><span class="sxs-lookup"><span data-stu-id="71d2f-185">In ASP.NET Core SignalR, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="71d2f-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="71d2f-186">GlobalHost</span></span>

<span data-ttu-id="71d2f-187">ASP.NET Core 在框架中内置了 (DI) 依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="71d2f-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="71d2f-188">服务可以使用 DI 来访问 [HubContext](xref:signalr/hubcontext)。</span><span class="sxs-lookup"><span data-stu-id="71d2f-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="71d2f-189">在 `GlobalHost` ASP.NET SignalR 中用于获取的对象 `HubContext` 在 ASP.NET Core 中不存在 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="71d2f-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="71d2f-190">HubPipeline</span></span>

<span data-ttu-id="71d2f-191">ASP.NET Core SignalR 不支持 `HubPipeline` 模块。</span><span class="sxs-lookup"><span data-stu-id="71d2f-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="71d2f-192">与客户端之间的差异</span><span class="sxs-lookup"><span data-stu-id="71d2f-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="71d2f-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="71d2f-193">TypeScript</span></span>

<span data-ttu-id="71d2f-194">ASP.NET Core SignalR 客户端是以 [TypeScript](https://www.typescriptlang.org/)编写的。</span><span class="sxs-lookup"><span data-stu-id="71d2f-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="71d2f-195">使用 [javascript 客户端](xref:signalr/javascript-client)时，可以使用 Javascript 或 TypeScript 来编写。</span><span class="sxs-lookup"><span data-stu-id="71d2f-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="71d2f-196">JavaScript 客户端托管在 npm</span><span class="sxs-lookup"><span data-stu-id="71d2f-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71d2f-197">在 ASP.NET 版本中，JavaScript 客户端通过 Visual Studio 中的 NuGet 包获得。</span><span class="sxs-lookup"><span data-stu-id="71d2f-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="71d2f-198">在 ASP.NET Core 版本中， [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 包包含 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="71d2f-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="71d2f-199">此包不包含在 **ASP.NET Core Web 应用程序** 模板中。</span><span class="sxs-lookup"><span data-stu-id="71d2f-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="71d2f-200">使用 npm 获取和安装 `@microsoft/signalr` npm 包。</span><span class="sxs-lookup"><span data-stu-id="71d2f-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="71d2f-201">在 ASP.NET 版本中，JavaScript 客户端通过 Visual Studio 中的 NuGet 包获得。</span><span class="sxs-lookup"><span data-stu-id="71d2f-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="71d2f-202">在 ASP.NET Core 版本中， [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 包包含 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="71d2f-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="71d2f-203">此包不包含在 **ASP.NET Core Web 应用程序** 模板中。</span><span class="sxs-lookup"><span data-stu-id="71d2f-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="71d2f-204">使用 npm 获取和安装 `@aspnet/signalr` npm 包。</span><span class="sxs-lookup"><span data-stu-id="71d2f-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="71d2f-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="71d2f-205">jQuery</span></span>

<span data-ttu-id="71d2f-206">已移除 jQuery 的依赖项，但项目仍可使用 jQuery。</span><span class="sxs-lookup"><span data-stu-id="71d2f-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="71d2f-207">Internet Explorer 支持</span><span class="sxs-lookup"><span data-stu-id="71d2f-207">Internet Explorer support</span></span>

<span data-ttu-id="71d2f-208">ASP.NET Core SignalR 支持 Microsoft Internet explorer 11 或更高版本，而 ASP.NET SignalR 支持 Microsoft internet explorer 8 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="71d2f-208">ASP.NET Core SignalR supports Microsoft Internet Explorer 11 or later, whereas ASP.NET SignalR supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="71d2f-209">有关浏览器支持的详细信息，请参阅 [支持的平台](xref:signalr/supported-platforms#javascript-client)。</span><span class="sxs-lookup"><span data-stu-id="71d2f-209">More info on browser support can be found at [supported platforms](xref:signalr/supported-platforms#javascript-client).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="71d2f-210">JavaScript 客户端方法语法</span><span class="sxs-lookup"><span data-stu-id="71d2f-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71d2f-211">JavaScript 语法已在的 ASP.NET 版本中发生了更改 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-211">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="71d2f-212">不要使用 `$connection` 对象，而是使用 [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 创建连接。</span><span class="sxs-lookup"><span data-stu-id="71d2f-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="71d2f-213">使用 [on](/javascript/api/@microsoft/signalr/HubConnection#on) 方法可指定中心可调用的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="71d2f-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="71d2f-214">JavaScript 语法已在的 ASP.NET 版本中发生了更改 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-214">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="71d2f-215">不要使用 `$connection` 对象，而是使用 [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 创建连接。</span><span class="sxs-lookup"><span data-stu-id="71d2f-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="71d2f-216">使用 [on](/javascript/api/@aspnet/signalr/HubConnection#on) 方法可指定中心可调用的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="71d2f-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="71d2f-217">创建客户端方法后，启动集线器连接。</span><span class="sxs-lookup"><span data-stu-id="71d2f-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="71d2f-218">将 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 方法链接到日志或处理错误。</span><span class="sxs-lookup"><span data-stu-id="71d2f-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="71d2f-219">中心代理</span><span class="sxs-lookup"><span data-stu-id="71d2f-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71d2f-220">中心代理不再自动生成。</span><span class="sxs-lookup"><span data-stu-id="71d2f-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="71d2f-221">相反，方法名称将作为字符串传递到 [调用](/javascript/api/@microsoft/signalr/hubconnection#invoke) API。</span><span class="sxs-lookup"><span data-stu-id="71d2f-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="71d2f-222">中心代理不再自动生成。</span><span class="sxs-lookup"><span data-stu-id="71d2f-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="71d2f-223">相反，方法名称将作为字符串传递到 [调用](/javascript/api/@aspnet/signalr/hubconnection#invoke) API。</span><span class="sxs-lookup"><span data-stu-id="71d2f-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="71d2f-224">.NET 和其他客户端</span><span class="sxs-lookup"><span data-stu-id="71d2f-224">.NET and other clients</span></span>

<span data-ttu-id="71d2f-225">[AspNetCore. SignalR客户端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet 包包含用于 ASP.NET Core 的 .net 客户端库 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="71d2f-225">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="71d2f-226">使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 创建和生成与集线器的连接的实例。</span><span class="sxs-lookup"><span data-stu-id="71d2f-226">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="71d2f-227">扩展差异</span><span class="sxs-lookup"><span data-stu-id="71d2f-227">Scaleout differences</span></span>

<span data-ttu-id="71d2f-228">ASP.NET SignalR 支持 SQL Server 和 Redis。</span><span class="sxs-lookup"><span data-stu-id="71d2f-228">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="71d2f-229">ASP.NET Core SignalR 支持 Azure SignalR 服务和 Redis。</span><span class="sxs-lookup"><span data-stu-id="71d2f-229">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="71d2f-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="71d2f-230">ASP.NET</span></span>

* [<span data-ttu-id="71d2f-231">SignalR 与 Azure 服务总线扩展</span><span class="sxs-lookup"><span data-stu-id="71d2f-231">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="71d2f-232">SignalR 带 Redis 的扩展</span><span class="sxs-lookup"><span data-stu-id="71d2f-232">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="71d2f-233">SignalR 扩展与 SQL Server</span><span class="sxs-lookup"><span data-stu-id="71d2f-233">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="71d2f-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71d2f-234">ASP.NET Core</span></span>

* [<span data-ttu-id="71d2f-235">Azure SignalR 服务</span><span class="sxs-lookup"><span data-stu-id="71d2f-235">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="71d2f-236">Redis 底板</span><span class="sxs-lookup"><span data-stu-id="71d2f-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="71d2f-237">其他资源</span><span class="sxs-lookup"><span data-stu-id="71d2f-237">Additional resources</span></span>

* [<span data-ttu-id="71d2f-238">中心</span><span class="sxs-lookup"><span data-stu-id="71d2f-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="71d2f-239">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="71d2f-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="71d2f-240">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="71d2f-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="71d2f-241">支持的平台</span><span class="sxs-lookup"><span data-stu-id="71d2f-241">Supported platforms</span></span>](xref:signalr/supported-platforms)
