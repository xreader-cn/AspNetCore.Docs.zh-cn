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
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a><span data-ttu-id="07858-103">ASP.NET SignalR 和 ASP.NET Core 之间的差异 SignalR</span><span class="sxs-lookup"><span data-stu-id="07858-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="07858-104">ASP.NET Core SignalR 与 ASP.NET SignalR的客户端或服务器不兼容。</span><span class="sxs-lookup"><span data-stu-id="07858-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="07858-105">本文详细介绍了 ASP.NET Core SignalR中已删除或更改的功能。</span><span class="sxs-lookup"><span data-stu-id="07858-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-opno-locsignalr-version"></a><span data-ttu-id="07858-106">如何识别 SignalR 版本</span><span class="sxs-lookup"><span data-stu-id="07858-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="07858-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="07858-107">ASP.NET SignalR</span></span> | <span data-ttu-id="07858-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="07858-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="07858-109">服务器 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="07858-109">Server NuGet Package</span></span> | <span data-ttu-id="07858-110">[SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="07858-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="07858-111">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) （.net Core）</span><span class="sxs-lookup"><span data-stu-id="07858-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="07858-112">[AspNetCore。SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="07858-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="07858-113">（.NET Framework）</span><span class="sxs-lookup"><span data-stu-id="07858-113">(.NET Framework)</span></span> |
| <span data-ttu-id="07858-114">客户端 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="07858-114">Client NuGet Packages</span></span> | <span data-ttu-id="07858-115">[SignalR。机](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="07858-115">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="07858-116">[SignalR。JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="07858-116">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="07858-117">[AspNetCore.SignalR。机](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="07858-117">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="07858-118">客户端 npm 包</span><span class="sxs-lookup"><span data-stu-id="07858-118">Client npm Package</span></span> | [<span data-ttu-id="07858-119">signalr</span><span class="sxs-lookup"><span data-stu-id="07858-119">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="07858-120">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="07858-120">Java Client</span></span> | <span data-ttu-id="07858-121">[GitHub 存储库](https://github.com/SignalR/java-client)（已弃用）</span><span class="sxs-lookup"><span data-stu-id="07858-121">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="07858-122">Maven 包[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="07858-122">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="07858-123">服务器应用类型</span><span class="sxs-lookup"><span data-stu-id="07858-123">Server App Type</span></span> | <span data-ttu-id="07858-124">ASP.NET （System.web）或 OWIN 自承载</span><span class="sxs-lookup"><span data-stu-id="07858-124">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="07858-125">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07858-125">ASP.NET Core</span></span> |
| <span data-ttu-id="07858-126">支持的服务器平台</span><span class="sxs-lookup"><span data-stu-id="07858-126">Supported Server Platforms</span></span> | <span data-ttu-id="07858-127">.NET Framework 4.5 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07858-127">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="07858-128">.NET Framework 4.6.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07858-128">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="07858-129">.NET Core 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07858-129">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="07858-130">功能差异</span><span class="sxs-lookup"><span data-stu-id="07858-130">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="07858-131">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="07858-131">Automatic reconnects</span></span>

<span data-ttu-id="07858-132">ASP.NET Core SignalR不支持自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="07858-132">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="07858-133">如果客户端已断开连接，则用户必须显式启动新连接（如果要重新连接）。</span><span class="sxs-lookup"><span data-stu-id="07858-133">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="07858-134">在 ASP.NET SignalR中，SignalR 在断开连接时尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="07858-134">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="07858-135">协议支持</span><span class="sxs-lookup"><span data-stu-id="07858-135">Protocol support</span></span>

<span data-ttu-id="07858-136">ASP.NET Core SignalR 支持 JSON，以及基于[MessagePack](xref:signalr/messagepackhubprotocol)的新二进制协议。</span><span class="sxs-lookup"><span data-stu-id="07858-136">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="07858-137">此外，还可以创建自定义协议。</span><span class="sxs-lookup"><span data-stu-id="07858-137">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="07858-138">传输</span><span class="sxs-lookup"><span data-stu-id="07858-138">Transports</span></span>

<span data-ttu-id="07858-139">ASP.NET Core SignalR不支持永久帧传输。</span><span class="sxs-lookup"><span data-stu-id="07858-139">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="07858-140">服务器上的差异</span><span class="sxs-lookup"><span data-stu-id="07858-140">Differences on the server</span></span>

<span data-ttu-id="07858-141">SignalR 服务器端库的 ASP.NET Core 包含在[元包](xref:fundamentals/metapackage-app)包中，后者是 RAZOR 和 MVC 项目的**ASP.NET Core Web 应用程序**模板的一部分。</span><span class="sxs-lookup"><span data-stu-id="07858-141">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="07858-142">ASP.NET Core SignalR 是 ASP.NET Core 中间件，所以必须通过在 `Startup.ConfigureServices`中调用[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)进行配置。</span><span class="sxs-lookup"><span data-stu-id="07858-142">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07858-143">若要配置路由，请将路由映射到 `Startup.Configure` 方法中的[UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints)方法调用内的中心。</span><span class="sxs-lookup"><span data-stu-id="07858-143">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="07858-144">若要配置路由，请将路由映射到 `Startup.Configure` 方法中的[UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr)方法调用内的中心。</span><span class="sxs-lookup"><span data-stu-id="07858-144">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="07858-145">粘滞会话</span><span class="sxs-lookup"><span data-stu-id="07858-145">Sticky sessions</span></span>

<span data-ttu-id="07858-146">ASP.NET SignalR 的扩展模型使客户端可以重新连接到场中的任何服务器并向其发送消息。</span><span class="sxs-lookup"><span data-stu-id="07858-146">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="07858-147">在 ASP.NET Core SignalR中，客户端必须在连接期间与同一服务器交互。</span><span class="sxs-lookup"><span data-stu-id="07858-147">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="07858-148">对于使用 Redis 的扩展，这意味着需要使用粘滞会话。</span><span class="sxs-lookup"><span data-stu-id="07858-148">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="07858-149">对于使用[Azure SignalR 服务](/azure/azure-signalr/)的扩展，无需使用粘滞会话，因为服务会处理与客户端的连接。</span><span class="sxs-lookup"><span data-stu-id="07858-149">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="07858-150">单个集线器每个连接</span><span class="sxs-lookup"><span data-stu-id="07858-150">Single hub per connection</span></span>

<span data-ttu-id="07858-151">在 ASP.NET Core SignalR中，连接模型已简化。</span><span class="sxs-lookup"><span data-stu-id="07858-151">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="07858-152">直接连接到单个集线器，而不是使用单个连接来共享对多个中心的访问。</span><span class="sxs-lookup"><span data-stu-id="07858-152">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="07858-153">流式处理</span><span class="sxs-lookup"><span data-stu-id="07858-153">Streaming</span></span>

<span data-ttu-id="07858-154">ASP.NET Core SignalR 现在支持从中心到客户端的[流数据](xref:signalr/streaming)。</span><span class="sxs-lookup"><span data-stu-id="07858-154">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="07858-155">状态</span><span class="sxs-lookup"><span data-stu-id="07858-155">State</span></span>

<span data-ttu-id="07858-156">可在客户端和中心之间传递任意状态的功能（通常称为 HubState）已删除，并支持进度消息。</span><span class="sxs-lookup"><span data-stu-id="07858-156">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="07858-157">目前没有集线器代理。</span><span class="sxs-lookup"><span data-stu-id="07858-157">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="07858-158">删除 PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="07858-158">PersistentConnection removal</span></span>

<span data-ttu-id="07858-159">在 ASP.NET Core SignalR中，已删除[PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))类。</span><span class="sxs-lookup"><span data-stu-id="07858-159">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="07858-160">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="07858-160">GlobalHost</span></span>

<span data-ttu-id="07858-161">ASP.NET Core 在框架中内置了依赖关系注入（DI）。</span><span class="sxs-lookup"><span data-stu-id="07858-161">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="07858-162">服务可以使用 DI 来访问[HubContext](xref:signalr/hubcontext)。</span><span class="sxs-lookup"><span data-stu-id="07858-162">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="07858-163">在 ASP.NET SignalR 中用于获取 `HubContext` 的 `GlobalHost` 对象在 ASP.NET Core SignalR中不存在。</span><span class="sxs-lookup"><span data-stu-id="07858-163">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="07858-164">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="07858-164">HubPipeline</span></span>

<span data-ttu-id="07858-165">ASP.NET Core SignalR 不支持 `HubPipeline` 模块。</span><span class="sxs-lookup"><span data-stu-id="07858-165">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="07858-166">与客户端之间的差异</span><span class="sxs-lookup"><span data-stu-id="07858-166">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="07858-167">TypeScript</span><span class="sxs-lookup"><span data-stu-id="07858-167">TypeScript</span></span>

<span data-ttu-id="07858-168">SignalR 客户端 ASP.NET Core 是以[TypeScript](https://www.typescriptlang.org/)编写的。</span><span class="sxs-lookup"><span data-stu-id="07858-168">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="07858-169">使用[javascript 客户端](xref:signalr/javascript-client)时，可以使用 Javascript 或 TypeScript 来编写。</span><span class="sxs-lookup"><span data-stu-id="07858-169">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="07858-170">JavaScript 客户端托管在[npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="07858-170">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="07858-171">在以前的版本中，JavaScript 客户端通过 Visual Studio 中的 NuGet 包获得。</span><span class="sxs-lookup"><span data-stu-id="07858-171">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="07858-172">对于核心版本， [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm 包包含 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="07858-172">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="07858-173">此包不包含在**ASP.NET Core Web 应用程序**模板中。</span><span class="sxs-lookup"><span data-stu-id="07858-173">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="07858-174">使用 npm 获取并安装 `@aspnet/signalr` npm 包。</span><span class="sxs-lookup"><span data-stu-id="07858-174">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="07858-175">jQuery</span><span class="sxs-lookup"><span data-stu-id="07858-175">jQuery</span></span>

<span data-ttu-id="07858-176">已移除 jQuery 的依赖项，但项目仍可使用 jQuery。</span><span class="sxs-lookup"><span data-stu-id="07858-176">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="07858-177">Internet Explorer 支持</span><span class="sxs-lookup"><span data-stu-id="07858-177">Internet Explorer support</span></span>

<span data-ttu-id="07858-178">ASP.NET Core SignalR 需要 Microsoft Internet Explorer 11 或更高版本（ASP.NET SignalR 支持的 Microsoft Internet Explorer 8 及更高版本）。</span><span class="sxs-lookup"><span data-stu-id="07858-178">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="07858-179">JavaScript 客户端方法语法</span><span class="sxs-lookup"><span data-stu-id="07858-179">JavaScript client method syntax</span></span>

<span data-ttu-id="07858-180">JavaScript 语法已与 SignalR的以前版本发生了更改。</span><span class="sxs-lookup"><span data-stu-id="07858-180">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="07858-181">不要使用 `$connection` 对象，而是使用[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API 创建连接。</span><span class="sxs-lookup"><span data-stu-id="07858-181">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="07858-182">使用[on](/javascript/api/@aspnet/signalr/HubConnection#on)方法可指定中心可调用的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="07858-182">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="07858-183">创建客户端方法后，启动集线器连接。</span><span class="sxs-lookup"><span data-stu-id="07858-183">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="07858-184">将[catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)方法链接到日志或处理错误。</span><span class="sxs-lookup"><span data-stu-id="07858-184">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="07858-185">中心代理</span><span class="sxs-lookup"><span data-stu-id="07858-185">Hub proxies</span></span>

<span data-ttu-id="07858-186">中心代理不再自动生成。</span><span class="sxs-lookup"><span data-stu-id="07858-186">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="07858-187">相反，方法名称将作为字符串传递到[调用](/javascript/api/%40aspnet/signalr/hubconnection#invoke)API。</span><span class="sxs-lookup"><span data-stu-id="07858-187">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="07858-188">.NET 和其他客户端</span><span class="sxs-lookup"><span data-stu-id="07858-188">.NET and other clients</span></span>

<span data-ttu-id="07858-189">`Microsoft.AspNetCore.SignalR.Client` NuGet 包包含用于 ASP.NET Core SignalR的 .NET 客户端库。</span><span class="sxs-lookup"><span data-stu-id="07858-189">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="07858-190">使用[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)创建和生成与集线器的连接的实例。</span><span class="sxs-lookup"><span data-stu-id="07858-190">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="07858-191">扩展差异</span><span class="sxs-lookup"><span data-stu-id="07858-191">Scaleout differences</span></span>

<span data-ttu-id="07858-192">ASP.NET SignalR 支持 SQL Server 和 Redis。</span><span class="sxs-lookup"><span data-stu-id="07858-192">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="07858-193">ASP.NET Core SignalR 支持 Azure SignalR 服务和 Redis。</span><span class="sxs-lookup"><span data-stu-id="07858-193">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="07858-194">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="07858-194">ASP.NET</span></span>

* <span data-ttu-id="07858-195">[与 Azure 服务总线 SignalR 扩展](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="07858-195">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="07858-196">[SignalR Redis 的扩展](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="07858-196">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="07858-197">[SignalR 扩展与 SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="07858-197">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="07858-198">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07858-198">ASP.NET Core</span></span>

* <span data-ttu-id="07858-199">[Azure SignalR 服务](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="07858-199">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="07858-200">Redis 底板</span><span class="sxs-lookup"><span data-stu-id="07858-200">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="07858-201">其他资源</span><span class="sxs-lookup"><span data-stu-id="07858-201">Additional resources</span></span>

* [<span data-ttu-id="07858-202">中心</span><span class="sxs-lookup"><span data-stu-id="07858-202">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="07858-203">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="07858-203">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="07858-204">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="07858-204">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="07858-205">支持的平台</span><span class="sxs-lookup"><span data-stu-id="07858-205">Supported platforms</span></span>](xref:signalr/supported-platforms)
