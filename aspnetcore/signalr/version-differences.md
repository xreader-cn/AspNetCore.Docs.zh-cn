---
title: 和 ASP.NET Core SignalR之间的差异SignalR
author: bradygaster
description: 和 ASP.NET Core SignalR之间的差异SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 58d134ae971bace178561322f1c8a6351432be03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772551"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="65415-103">ASP.NET SignalR 和 ASP.NET Core SignalR 之间的差异</span><span class="sxs-lookup"><span data-stu-id="65415-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="65415-104">ASP.NET Core SignalR 与 ASP.NET SignalR 的客户端或服务器不兼容。</span><span class="sxs-lookup"><span data-stu-id="65415-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="65415-105">本文详细介绍了 ASP.NET Core SignalR 中删除或更改的功能。</span><span class="sxs-lookup"><span data-stu-id="65415-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="65415-106">如何识别 SignalR 版本</span><span class="sxs-lookup"><span data-stu-id="65415-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="65415-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="65415-107">ASP.NET SignalR</span></span> | <span data-ttu-id="65415-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="65415-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="65415-109">服务器 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="65415-109">Server NuGet package</span></span> | [<span data-ttu-id="65415-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="65415-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="65415-111">无。</span><span class="sxs-lookup"><span data-stu-id="65415-111">None.</span></span> <span data-ttu-id="65415-112">包含在[AspNetCore](xref:fundamentals/metapackage-app)共享框架中。</span><span class="sxs-lookup"><span data-stu-id="65415-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="65415-113">客户端 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="65415-113">Client NuGet packages</span></span> | [<span data-ttu-id="65415-114">SignalR。</span><span class="sxs-lookup"><span data-stu-id="65415-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="65415-115">SignalR）</span><span class="sxs-lookup"><span data-stu-id="65415-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="65415-116">AspNetCore. SignalR</span><span class="sxs-lookup"><span data-stu-id="65415-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="65415-117">JavaScript 客户端 npm 包</span><span class="sxs-lookup"><span data-stu-id="65415-117">JavaScript client npm package</span></span> | [<span data-ttu-id="65415-118">signalr</span><span class="sxs-lookup"><span data-stu-id="65415-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="65415-119">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="65415-119">Java client</span></span> | <span data-ttu-id="65415-120">[GitHub 存储库](https://github.com/SignalR/java-client)（已弃用）</span><span class="sxs-lookup"><span data-stu-id="65415-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="65415-121">Maven 包[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="65415-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="65415-122">服务器应用类型</span><span class="sxs-lookup"><span data-stu-id="65415-122">Server app type</span></span> | <span data-ttu-id="65415-123">ASP.NET （System.web）或 OWIN 自承载</span><span class="sxs-lookup"><span data-stu-id="65415-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="65415-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65415-124">ASP.NET Core</span></span> |
| <span data-ttu-id="65415-125">支持的服务器平台</span><span class="sxs-lookup"><span data-stu-id="65415-125">Supported server platforms</span></span> | <span data-ttu-id="65415-126">.NET Framework 4.5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="65415-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="65415-127">.NET Core 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="65415-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="65415-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="65415-128">ASP.NET SignalR</span></span> | <span data-ttu-id="65415-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="65415-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="65415-130">服务器 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="65415-130">Server NuGet package</span></span> | <span data-ttu-id="65415-131">[Microsoft。SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="65415-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="65415-132">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) （.net Core）</span><span class="sxs-lookup"><span data-stu-id="65415-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="65415-133">[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="65415-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="65415-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="65415-134">(.NET Framework)</span></span> |
| <span data-ttu-id="65415-135">客户端 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="65415-135">Client NuGet packages</span></span> | <span data-ttu-id="65415-136">[Microsoft。SignalR.机](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="65415-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="65415-137">[Microsoft。SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="65415-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="65415-138">[AspNetCore。SignalR.机](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="65415-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="65415-139">JavaScript 客户端 npm 包</span><span class="sxs-lookup"><span data-stu-id="65415-139">JavaScript client npm package</span></span> | [<span data-ttu-id="65415-140">signalr</span><span class="sxs-lookup"><span data-stu-id="65415-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="65415-141">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="65415-141">Java client</span></span> | <span data-ttu-id="65415-142">[GitHub 存储库](https://github.com/SignalR/java-client)（已弃用）</span><span class="sxs-lookup"><span data-stu-id="65415-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="65415-143">Maven 包[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="65415-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="65415-144">服务器应用类型</span><span class="sxs-lookup"><span data-stu-id="65415-144">Server app type</span></span> | <span data-ttu-id="65415-145">ASP.NET （System.web）或 OWIN 自承载</span><span class="sxs-lookup"><span data-stu-id="65415-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="65415-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65415-146">ASP.NET Core</span></span> |
| <span data-ttu-id="65415-147">支持的服务器平台</span><span class="sxs-lookup"><span data-stu-id="65415-147">Supported server platforms</span></span> | <span data-ttu-id="65415-148">.NET Framework 4.5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="65415-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="65415-149">.NET Framework 4.6.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="65415-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="65415-150">.NET Core 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="65415-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="65415-151">功能差异</span><span class="sxs-lookup"><span data-stu-id="65415-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="65415-152">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="65415-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65415-153">在 ASP.NET SignalR中：</span><span class="sxs-lookup"><span data-stu-id="65415-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="65415-154">默认情况下SignalR ，如果断开连接，则将尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="65415-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="65415-155">在 ASP.NET Core SignalR中：</span><span class="sxs-lookup"><span data-stu-id="65415-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="65415-156">自动重新连接同时选择[.net 客户](xref:signalr/dotnet-client#automatically-reconnect)[端和 JavaScript 客户端](xref:signalr/javascript-client#automatically-reconnect)：</span><span class="sxs-lookup"><span data-stu-id="65415-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

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

<span data-ttu-id="65415-157">ASP.NET Core 3.0 之前， SignalR不支持自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="65415-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="65415-158">如果客户端已断开连接，用户必须显式启动新连接才能重新连接。</span><span class="sxs-lookup"><span data-stu-id="65415-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="65415-159">如果断开SignalR连接SignalR ，则在 ASP.NET 中尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="65415-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="65415-160">协议支持</span><span class="sxs-lookup"><span data-stu-id="65415-160">Protocol support</span></span>

<span data-ttu-id="65415-161">ASP.NET Core SignalR支持 JSON，以及基于[MessagePack](xref:signalr/messagepackhubprotocol)的新二进制协议。</span><span class="sxs-lookup"><span data-stu-id="65415-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="65415-162">此外，还可以创建自定义协议。</span><span class="sxs-lookup"><span data-stu-id="65415-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="65415-163">传输</span><span class="sxs-lookup"><span data-stu-id="65415-163">Transports</span></span>

<span data-ttu-id="65415-164">ASP.NET Core SignalR中不支持永久帧传输。</span><span class="sxs-lookup"><span data-stu-id="65415-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="65415-165">服务器上的差异</span><span class="sxs-lookup"><span data-stu-id="65415-165">Differences on the server</span></span>

<span data-ttu-id="65415-166">ASP.NET Core SignalR的服务器端库包含在[AspNetCore](xref:fundamentals/metapackage-app)中，该应用程序用于Razor和 MVC 项目的**ASP.NET Core Web 应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="65415-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="65415-167">ASP.NET Core SignalR是 ASP.NET Core 的中间件。</span><span class="sxs-lookup"><span data-stu-id="65415-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="65415-168">必须通过在中<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices`调用来配置它。</span><span class="sxs-lookup"><span data-stu-id="65415-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65415-169">若要配置路由，请将路由映射到<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure`方法中的方法调用内的中心。</span><span class="sxs-lookup"><span data-stu-id="65415-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="65415-170">若要配置路由，请将路由映射到<xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> `Startup.Configure`方法中的方法调用内的中心。</span><span class="sxs-lookup"><span data-stu-id="65415-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="65415-171">粘滞会话</span><span class="sxs-lookup"><span data-stu-id="65415-171">Sticky sessions</span></span>

<span data-ttu-id="65415-172">ASP.NET SignalR的扩展模型允许客户端将消息重新连接到场中的任何服务器并向其发送消息。</span><span class="sxs-lookup"><span data-stu-id="65415-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="65415-173">在 ASP.NET Core SignalR中，客户端必须在连接期间与同一服务器交互。</span><span class="sxs-lookup"><span data-stu-id="65415-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="65415-174">对于使用 Redis 的扩展，这意味着需要使用粘滞会话。</span><span class="sxs-lookup"><span data-stu-id="65415-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="65415-175">对于使用[Azure SignalR服务](/azure/azure-signalr/)的扩展，无需使用粘滞会话，因为服务会处理与客户端的连接。</span><span class="sxs-lookup"><span data-stu-id="65415-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="65415-176">单个集线器每个连接</span><span class="sxs-lookup"><span data-stu-id="65415-176">Single hub per connection</span></span>

<span data-ttu-id="65415-177">在 ASP.NET Core SignalR中，连接模型已简化。</span><span class="sxs-lookup"><span data-stu-id="65415-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="65415-178">直接连接到单个集线器，而不是使用单个连接来共享对多个中心的访问。</span><span class="sxs-lookup"><span data-stu-id="65415-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="65415-179">流式处理</span><span class="sxs-lookup"><span data-stu-id="65415-179">Streaming</span></span>

<span data-ttu-id="65415-180">ASP.NET Core SignalR现在支持从中心到客户端的[流数据](xref:signalr/streaming)。</span><span class="sxs-lookup"><span data-stu-id="65415-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="65415-181">状态</span><span class="sxs-lookup"><span data-stu-id="65415-181">State</span></span>

<span data-ttu-id="65415-182">可在客户端和中心之间传递任意状态（通常称为`HubState`）的功能已被删除，并且支持进度消息。</span><span class="sxs-lookup"><span data-stu-id="65415-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="65415-183">目前没有集线器代理。</span><span class="sxs-lookup"><span data-stu-id="65415-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="65415-184">删除 PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="65415-184">PersistentConnection removal</span></span>

<span data-ttu-id="65415-185">在 ASP.NET Core SignalR中，已删除[PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))类。</span><span class="sxs-lookup"><span data-stu-id="65415-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="65415-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="65415-186">GlobalHost</span></span>

<span data-ttu-id="65415-187">ASP.NET Core 在框架中内置了依赖关系注入（DI）。</span><span class="sxs-lookup"><span data-stu-id="65415-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="65415-188">服务可以使用 DI 来访问[HubContext](xref:signalr/hubcontext)。</span><span class="sxs-lookup"><span data-stu-id="65415-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="65415-189">在`GlobalHost` ASP.NET SignalR中用于获取的`HubContext`对象在 ASP.NET Core SignalR中不存在。</span><span class="sxs-lookup"><span data-stu-id="65415-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="65415-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="65415-190">HubPipeline</span></span>

<span data-ttu-id="65415-191">ASP.NET Core SignalR不支持`HubPipeline`模块。</span><span class="sxs-lookup"><span data-stu-id="65415-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="65415-192">与客户端之间的差异</span><span class="sxs-lookup"><span data-stu-id="65415-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="65415-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="65415-193">TypeScript</span></span>

<span data-ttu-id="65415-194">ASP.NET Core SignalR客户端是以[TypeScript](https://www.typescriptlang.org/)编写的。</span><span class="sxs-lookup"><span data-stu-id="65415-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="65415-195">使用[javascript 客户端](xref:signalr/javascript-client)时，可以使用 Javascript 或 TypeScript 来编写。</span><span class="sxs-lookup"><span data-stu-id="65415-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="65415-196">JavaScript 客户端托管在 npm</span><span class="sxs-lookup"><span data-stu-id="65415-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65415-197">在 ASP.NET 版本中，JavaScript 客户端通过 Visual Studio 中的 NuGet 包获得。</span><span class="sxs-lookup"><span data-stu-id="65415-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="65415-198">在 ASP.NET Core 版本中， [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm 包包含 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="65415-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="65415-199">此包不包含在**ASP.NET Core Web 应用程序**模板中。</span><span class="sxs-lookup"><span data-stu-id="65415-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="65415-200">使用 npm 获取和安装`@microsoft/signalr` npm 包。</span><span class="sxs-lookup"><span data-stu-id="65415-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="65415-201">在 ASP.NET 版本中，JavaScript 客户端通过 Visual Studio 中的 NuGet 包获得。</span><span class="sxs-lookup"><span data-stu-id="65415-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="65415-202">在 ASP.NET Core 版本中， [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm 包包含 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="65415-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="65415-203">此包不包含在**ASP.NET Core Web 应用程序**模板中。</span><span class="sxs-lookup"><span data-stu-id="65415-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="65415-204">使用 npm 获取和安装`@aspnet/signalr` npm 包。</span><span class="sxs-lookup"><span data-stu-id="65415-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="65415-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="65415-205">jQuery</span></span>

<span data-ttu-id="65415-206">已移除 jQuery 的依赖项，但项目仍可使用 jQuery。</span><span class="sxs-lookup"><span data-stu-id="65415-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="65415-207">Internet Explorer 支持</span><span class="sxs-lookup"><span data-stu-id="65415-207">Internet Explorer support</span></span>

<span data-ttu-id="65415-208">ASP.NET Core SignalR需要 Microsoft internet explorer 11 或更高版本SignalR （ASP.NET 支持的 microsoft internet explorer 8 及更高版本）。</span><span class="sxs-lookup"><span data-stu-id="65415-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="65415-209">JavaScript 客户端方法语法</span><span class="sxs-lookup"><span data-stu-id="65415-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65415-210">JavaScript 语法已在的 ASP.NET 版本中发生了SignalR更改。</span><span class="sxs-lookup"><span data-stu-id="65415-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="65415-211">不要使用`$connection`对象，而是使用[HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 创建连接。</span><span class="sxs-lookup"><span data-stu-id="65415-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="65415-212">使用[on](/javascript/api/@microsoft/signalr/HubConnection#on)方法可指定中心可调用的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="65415-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="65415-213">JavaScript 语法已在的 ASP.NET 版本中发生了SignalR更改。</span><span class="sxs-lookup"><span data-stu-id="65415-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="65415-214">不要使用`$connection`对象，而是使用[HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 创建连接。</span><span class="sxs-lookup"><span data-stu-id="65415-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="65415-215">使用[on](/javascript/api/@aspnet/signalr/HubConnection#on)方法可指定中心可调用的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="65415-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="65415-216">创建客户端方法后，启动集线器连接。</span><span class="sxs-lookup"><span data-stu-id="65415-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="65415-217">将[catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)方法链接到日志或处理错误。</span><span class="sxs-lookup"><span data-stu-id="65415-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="65415-218">中心代理</span><span class="sxs-lookup"><span data-stu-id="65415-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65415-219">中心代理不再自动生成。</span><span class="sxs-lookup"><span data-stu-id="65415-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="65415-220">相反，方法名称将作为字符串传递到[调用](/javascript/api/@microsoft/signalr/hubconnection#invoke)API。</span><span class="sxs-lookup"><span data-stu-id="65415-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="65415-221">中心代理不再自动生成。</span><span class="sxs-lookup"><span data-stu-id="65415-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="65415-222">相反，方法名称将作为字符串传递到[调用](/javascript/api/@aspnet/signalr/hubconnection#invoke)API。</span><span class="sxs-lookup"><span data-stu-id="65415-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="65415-223">.NET 和其他客户端</span><span class="sxs-lookup"><span data-stu-id="65415-223">.NET and other clients</span></span>

<span data-ttu-id="65415-224">[AspNetCore.SignalR客户端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet 包包含用于 ASP.NET Core SignalR的 .net 客户端库。</span><span class="sxs-lookup"><span data-stu-id="65415-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="65415-225"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>使用创建和生成与集线器的连接的实例。</span><span class="sxs-lookup"><span data-stu-id="65415-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="65415-226">扩展差异</span><span class="sxs-lookup"><span data-stu-id="65415-226">Scaleout differences</span></span>

<span data-ttu-id="65415-227">ASP.NET SignalR支持 SQL Server 和 Redis。</span><span class="sxs-lookup"><span data-stu-id="65415-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="65415-228">ASP.NET Core SignalR支持 Azure SignalR服务和 Redis。</span><span class="sxs-lookup"><span data-stu-id="65415-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="65415-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="65415-229">ASP.NET</span></span>

* <span data-ttu-id="65415-230">[SignalR与 Azure 服务总线扩展](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="65415-230">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="65415-231">[SignalR带 Redis 的扩展](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="65415-231">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="65415-232">[SignalR扩展与 SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="65415-232">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="65415-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65415-233">ASP.NET Core</span></span>

* <span data-ttu-id="65415-234">[Azure SignalR服务](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="65415-234">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="65415-235">Redis 底板</span><span class="sxs-lookup"><span data-stu-id="65415-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="65415-236">其他资源</span><span class="sxs-lookup"><span data-stu-id="65415-236">Additional resources</span></span>

* [<span data-ttu-id="65415-237">中心</span><span class="sxs-lookup"><span data-stu-id="65415-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="65415-238">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="65415-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="65415-239">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="65415-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="65415-240">支持的平台</span><span class="sxs-lookup"><span data-stu-id="65415-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
