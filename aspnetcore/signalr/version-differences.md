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
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="a8b55-103">ASP.NET SignalR 和 ASP.NET Core SignalR 之间的差异</span><span class="sxs-lookup"><span data-stu-id="a8b55-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="a8b55-104">ASP.NET Core SignalR 与 ASP.NET SignalR 的客户端或服务器不兼容。</span><span class="sxs-lookup"><span data-stu-id="a8b55-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="a8b55-105">本文详细介绍 ASP.NET Core SignalR 中已删除或已更改的功能。</span><span class="sxs-lookup"><span data-stu-id="a8b55-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="a8b55-106">如何识别 SignalR 版本</span><span class="sxs-lookup"><span data-stu-id="a8b55-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="a8b55-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="a8b55-107">ASP.NET SignalR</span></span> | <span data-ttu-id="a8b55-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="a8b55-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="a8b55-109">服务器 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="a8b55-109">Server NuGet Package</span></span> | [<span data-ttu-id="a8b55-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="a8b55-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="a8b55-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="a8b55-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="a8b55-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="a8b55-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="a8b55-113">客户端 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="a8b55-113">Client NuGet Packages</span></span> | [<span data-ttu-id="a8b55-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="a8b55-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="a8b55-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="a8b55-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="a8b55-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="a8b55-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="a8b55-117">客户端 npm 包</span><span class="sxs-lookup"><span data-stu-id="a8b55-117">Client npm Package</span></span> | [<span data-ttu-id="a8b55-118">signalr</span><span class="sxs-lookup"><span data-stu-id="a8b55-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="a8b55-119">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="a8b55-119">Java Client</span></span> | <span data-ttu-id="a8b55-120">[GitHub 存储库](https://github.com/SignalR/java-client)（已弃用）</span><span class="sxs-lookup"><span data-stu-id="a8b55-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="a8b55-121">Maven 包[com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="a8b55-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="a8b55-122">服务器应用类型</span><span class="sxs-lookup"><span data-stu-id="a8b55-122">Server App Type</span></span> | <span data-ttu-id="a8b55-123">ASP.NET (System.Web) 或 OWIN 自承载</span><span class="sxs-lookup"><span data-stu-id="a8b55-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="a8b55-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8b55-124">ASP.NET Core</span></span> |
| <span data-ttu-id="a8b55-125">受支持的服务器平台</span><span class="sxs-lookup"><span data-stu-id="a8b55-125">Supported Server Platforms</span></span> | <span data-ttu-id="a8b55-126">.NET framework 4.5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="a8b55-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="a8b55-127">.NET Framework 4.6.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="a8b55-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="a8b55-128">.NET core 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="a8b55-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="a8b55-129">功能差异</span><span class="sxs-lookup"><span data-stu-id="a8b55-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="a8b55-130">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="a8b55-130">Automatic reconnects</span></span>

<span data-ttu-id="a8b55-131">ASP.NET Core SignalR 不支持自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="a8b55-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="a8b55-132">如果客户端已断开连接，则用户必须显式启动新连接才能重新连接。</span><span class="sxs-lookup"><span data-stu-id="a8b55-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="a8b55-133">在 ASP.NET SignalR 中，如果连接断开，SignalR 会尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="a8b55-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

### <a name="protocol-support"></a><span data-ttu-id="a8b55-134">协议支持</span><span class="sxs-lookup"><span data-stu-id="a8b55-134">Protocol support</span></span>

<span data-ttu-id="a8b55-135">ASP.NET Core SignalR 支持 JSON 以及一种基于 [MessagePack](xref:signalr/messagepackhubprotocol) 的新二进制协议。</span><span class="sxs-lookup"><span data-stu-id="a8b55-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="a8b55-136">此外，还可创建自定义协议。</span><span class="sxs-lookup"><span data-stu-id="a8b55-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="a8b55-137">传输</span><span class="sxs-lookup"><span data-stu-id="a8b55-137">Transports</span></span>

<span data-ttu-id="a8b55-138">ASP.NET Core SignalR 不支持永久帧传输。</span><span class="sxs-lookup"><span data-stu-id="a8b55-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="a8b55-139">服务器上的差异</span><span class="sxs-lookup"><span data-stu-id="a8b55-139">Differences on the server</span></span>

<span data-ttu-id="a8b55-140">ASP.NET Core SignalR 服务器端库包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，该包属于适用于 Razor 和 MVC 项目的 **ASP.NET Core Web 应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="a8b55-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="a8b55-141">ASP.NET Core SignalR 是一个 ASP.NET Core 中间件，因此必须通过在 `Startup.ConfigureServices` 中调用 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 来配置它。</span><span class="sxs-lookup"><span data-stu-id="a8b55-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

<span data-ttu-id="a8b55-142">若要配置路由，请在 `Startup.Configure` 方法中将路由映射到 [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) 方法调用内的中心。</span><span class="sxs-lookup"><span data-stu-id="a8b55-142">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a><span data-ttu-id="a8b55-143">粘滞会话</span><span class="sxs-lookup"><span data-stu-id="a8b55-143">Sticky sessions</span></span>

<span data-ttu-id="a8b55-144">ASP.NET SignalR 的横向扩展模型允许客户端重新连接场中的任何服务器并将消息发送到这些服务器。</span><span class="sxs-lookup"><span data-stu-id="a8b55-144">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="a8b55-145">在 ASP.NET Core SignalR 中，客户端必须在连接期间与同一服务器进行交互。</span><span class="sxs-lookup"><span data-stu-id="a8b55-145">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="a8b55-146">对于使用 Redis 的横向扩展，这意味着需要粘滞会话。</span><span class="sxs-lookup"><span data-stu-id="a8b55-146">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="a8b55-147">对于使用 [Azure SignalR 服务](/azure/azure-signalr/)的横向扩展，则不需要粘滞会话，因为该服务会处理与客户端的连接。</span><span class="sxs-lookup"><span data-stu-id="a8b55-147">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span> 

### <a name="single-hub-per-connection"></a><span data-ttu-id="a8b55-148">一个连接一个中心</span><span class="sxs-lookup"><span data-stu-id="a8b55-148">Single hub per connection</span></span>

<span data-ttu-id="a8b55-149">在ASP.NET Core SignalR中，连接模型已经简化。</span><span class="sxs-lookup"><span data-stu-id="a8b55-149">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="a8b55-150">可直接连接到单个中心，不必通过单个连接来共享对多个中心的访问。</span><span class="sxs-lookup"><span data-stu-id="a8b55-150">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="a8b55-151">流式处理</span><span class="sxs-lookup"><span data-stu-id="a8b55-151">Streaming</span></span>

<span data-ttu-id="a8b55-152">ASP.NET Core SignalR 现在支持从中心[流式传输数据](xref:signalr/streaming)到客户端。</span><span class="sxs-lookup"><span data-stu-id="a8b55-152">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="a8b55-153">状态</span><span class="sxs-lookup"><span data-stu-id="a8b55-153">State</span></span>

<span data-ttu-id="a8b55-154">已删除在客户端和中心之间传递任意状态（通常称为 HubState）的功能以及对进度消息的支持。</span><span class="sxs-lookup"><span data-stu-id="a8b55-154">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="a8b55-155">目前没有中心代理的对应项。</span><span class="sxs-lookup"><span data-stu-id="a8b55-155">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="a8b55-156">删除 PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="a8b55-156">PersistentConnection removal</span></span>

<span data-ttu-id="a8b55-157">在 ASP.NET Core SignalR 中，[PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) 类已删除。</span><span class="sxs-lookup"><span data-stu-id="a8b55-157">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span> 

### <a name="globalhost"></a><span data-ttu-id="a8b55-158">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="a8b55-158">GlobalHost</span></span>

<span data-ttu-id="a8b55-159">ASP.NET Core 在框架中内置了依赖关系注入 (DI)。</span><span class="sxs-lookup"><span data-stu-id="a8b55-159">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="a8b55-160">服务可以使用 DI 来访问 [HubContext](xref:signalr/hubcontext)。</span><span class="sxs-lookup"><span data-stu-id="a8b55-160">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="a8b55-161">ASP.NET CoreR SignalR 中不存在用于在 ASP.NET SignalR 中获取 `HubContext` 的 `GlobalHost` 对象。</span><span class="sxs-lookup"><span data-stu-id="a8b55-161">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="a8b55-162">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="a8b55-162">HubPipeline</span></span>

<span data-ttu-id="a8b55-163">ASP.NET Core SignalR 没有对 `HubPipeline` 模块的支持。</span><span class="sxs-lookup"><span data-stu-id="a8b55-163">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="a8b55-164">客户端上的差异</span><span class="sxs-lookup"><span data-stu-id="a8b55-164">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="a8b55-165">TypeScript</span><span class="sxs-lookup"><span data-stu-id="a8b55-165">TypeScript</span></span>

<span data-ttu-id="a8b55-166">ASP.NET Core SignalR 客户端以 [TypeScript](https://www.typescriptlang.org/) 编写。</span><span class="sxs-lookup"><span data-stu-id="a8b55-166">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="a8b55-167">使用 [JavaScript 客户端](xref:signalr/javascript-client)时，可以以 JavaScript 或 TypeScript 编写。</span><span class="sxs-lookup"><span data-stu-id="a8b55-167">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="a8b55-168">JavaScript 客户端托管在 [npm](https://www.npmjs.com/) 上</span><span class="sxs-lookup"><span data-stu-id="a8b55-168">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="a8b55-169">在以前的版本中，JavaScript 客户端是通过 Visual Studio 中的 NuGet 包获取的。</span><span class="sxs-lookup"><span data-stu-id="a8b55-169">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="a8b55-170">Core 版本的 [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm 包包含 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="a8b55-170">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="a8b55-171">此包不包括在**ASP.NET Core Web 应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="a8b55-171">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="a8b55-172">使用 npm 获取并安装 `@aspnet/signalr` npm 包。</span><span class="sxs-lookup"><span data-stu-id="a8b55-172">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="a8b55-173">jQuery</span><span class="sxs-lookup"><span data-stu-id="a8b55-173">jQuery</span></span>

<span data-ttu-id="a8b55-174">已删除对 jQuery 的依赖关系，但项目仍然可以使用 jQuery。</span><span class="sxs-lookup"><span data-stu-id="a8b55-174">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="a8b55-175">Internet Explorer 支持</span><span class="sxs-lookup"><span data-stu-id="a8b55-175">Internet Explorer support</span></span>

<span data-ttu-id="a8b55-176">ASP.NET Core SignalR 需要 Microsoft Internet Explorer 11 或更高版本（ASP.NET SignalR 支持 Microsoft Internet Explorer 8 及更高版本）。</span><span class="sxs-lookup"><span data-stu-id="a8b55-176">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="a8b55-177">JavaScript 客户端方法语法</span><span class="sxs-lookup"><span data-stu-id="a8b55-177">JavaScript client method syntax</span></span>

<span data-ttu-id="a8b55-178">JavaScript 语法已与 Signalr 早期版本中的相应语法不同。</span><span class="sxs-lookup"><span data-stu-id="a8b55-178">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="a8b55-179">请使用 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API 而非 `$connection` 对象创建连接。</span><span class="sxs-lookup"><span data-stu-id="a8b55-179">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="a8b55-180">使用 [on](/javascript/api/@aspnet/signalr/HubConnection#on) 方法指定可供中心调用的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="a8b55-180">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="a8b55-181">创建客户端方法后，启动中心连接。</span><span class="sxs-lookup"><span data-stu-id="a8b55-181">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="a8b55-182">链接一种 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) 方法以记录或处理错误。</span><span class="sxs-lookup"><span data-stu-id="a8b55-182">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="a8b55-183">中心代理</span><span class="sxs-lookup"><span data-stu-id="a8b55-183">Hub proxies</span></span>

<span data-ttu-id="a8b55-184">中心代理不再自动生成。</span><span class="sxs-lookup"><span data-stu-id="a8b55-184">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="a8b55-185">与之相反，方法名称将以字符串形式传递到 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API 中。</span><span class="sxs-lookup"><span data-stu-id="a8b55-185">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="a8b55-186">.NET 和其他客户端</span><span class="sxs-lookup"><span data-stu-id="a8b55-186">.NET and other clients</span></span>

<span data-ttu-id="a8b55-187">`Microsoft.AspNetCore.SignalR.Client` NuGet 包中包含 ASP.NET Core SignalR 的 .NET 客户端库。</span><span class="sxs-lookup"><span data-stu-id="a8b55-187">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="a8b55-188">使用 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) 创建和生成到中心的连接的实例。</span><span class="sxs-lookup"><span data-stu-id="a8b55-188">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="a8b55-189">横向扩展差异</span><span class="sxs-lookup"><span data-stu-id="a8b55-189">Scaleout differences</span></span>

<span data-ttu-id="a8b55-190">ASP.NET SignalR 支持 SQL Server 和 Redis。</span><span class="sxs-lookup"><span data-stu-id="a8b55-190">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="a8b55-191">ASP.NET Core SignalR 支持 Azure SignalR 服务和 Redis。</span><span class="sxs-lookup"><span data-stu-id="a8b55-191">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="a8b55-192">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="a8b55-192">ASP.NET</span></span>

* [<span data-ttu-id="a8b55-193">使用 Azure 服务总线的 SignalR 横向扩展</span><span class="sxs-lookup"><span data-stu-id="a8b55-193">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="a8b55-194">使用 Redis 的 SignalR 横向扩展</span><span class="sxs-lookup"><span data-stu-id="a8b55-194">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="a8b55-195">使用 SQL Server 的 SignalR 横向扩展</span><span class="sxs-lookup"><span data-stu-id="a8b55-195">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="a8b55-196">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8b55-196">ASP.NET Core</span></span>

* [<span data-ttu-id="a8b55-197">Azure SignalR 服务</span><span class="sxs-lookup"><span data-stu-id="a8b55-197">Azure SignalR Service</span></span>](/azure/azure-signalr/)

## <a name="additional-resources"></a><span data-ttu-id="a8b55-198">其他资源</span><span class="sxs-lookup"><span data-stu-id="a8b55-198">Additional resources</span></span>

* [<span data-ttu-id="a8b55-199">中心</span><span class="sxs-lookup"><span data-stu-id="a8b55-199">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a8b55-200">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="a8b55-200">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="a8b55-201">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="a8b55-201">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a8b55-202">支持的平台</span><span class="sxs-lookup"><span data-stu-id="a8b55-202">Supported platforms</span></span>](xref:signalr/supported-platforms)
