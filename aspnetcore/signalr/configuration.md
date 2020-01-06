---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c225ff88110dc17185a430ac1c422d2433306115
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358107"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="f1d41-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="f1d41-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f1d41-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f1d41-105">ASP.NET Core SignalR 支持两种用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f1d41-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f1d41-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="f1d41-108">`AddJsonProtocol`[可以在 `Startup.ConfigureServices`后添加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f1d41-109">`AddJsonProtocol` 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="f1d41-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f1d41-110">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f1d41-111">有关详细信息，请参阅[system.object 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="f1d41-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请在 `Startup.ConfigureServices`中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="f1d41-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="f1d41-113">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f1d41-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f1d41-114">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="f1d41-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="f1d41-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="f1d41-116">切换到 Newtonsoft.json</span><span class="sxs-lookup"><span data-stu-id="f1d41-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="f1d41-117">如果需要 `System.Text.Json`不支持的 `Newtonsoft.Json` 功能，请参阅[切换到 newtonsoft.json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f1d41-118">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-118">MessagePack serialization options</span></span>

<span data-ttu-id="f1d41-119">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f1d41-120">有关更多详细信息，请参阅[SignalR中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="f1d41-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f1d41-121">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f1d41-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-122">Configure server options</span></span>

<span data-ttu-id="f1d41-123">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="f1d41-124">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-124">Option</span></span> | <span data-ttu-id="f1d41-125">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-125">Default Value</span></span> | <span data-ttu-id="f1d41-126">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f1d41-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-127">30 seconds</span></span> | <span data-ttu-id="f1d41-128">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f1d41-129">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f1d41-130">建议值为 `KeepAliveInterval` 值的两倍。</span><span class="sxs-lookup"><span data-stu-id="f1d41-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f1d41-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-131">15 seconds</span></span> | <span data-ttu-id="f1d41-132">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="f1d41-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f1d41-133">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-134">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f1d41-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-135">15 seconds</span></span> | <span data-ttu-id="f1d41-136">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="f1d41-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f1d41-137">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f1d41-138">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f1d41-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="f1d41-139">All installed protocols</span></span> | <span data-ttu-id="f1d41-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="f1d41-140">Protocols supported by this hub.</span></span> <span data-ttu-id="f1d41-141">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="f1d41-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f1d41-142">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="f1d41-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f1d41-143">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="f1d41-144">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="f1d41-145">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="f1d41-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="f1d41-146">32 KB</span></span> | <span data-ttu-id="f1d41-147">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="f1d41-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="f1d41-148">可以通过向 `Startup.ConfigureServices`中的 `AddSignalR` 调用提供选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="f1d41-149">单个集线器的选项将覆盖 `AddSignalR` 中提供的全局选项，并且可以使用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="f1d41-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f1d41-150">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f1d41-151">使用 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f1d41-152">可以通过将委托传递到 `Startup.Configure`中的[MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="f1d41-153">下表描述了用于配置 ASP.NET Core SignalR的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="f1d41-154">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-154">Option</span></span> | <span data-ttu-id="f1d41-155">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-155">Default Value</span></span> | <span data-ttu-id="f1d41-156">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f1d41-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="f1d41-157">32 KB</span></span> | <span data-ttu-id="f1d41-158">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="f1d41-159">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="f1d41-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f1d41-160">从应用于 Hub 类的 `Authorize` 属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f1d41-161">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="f1d41-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f1d41-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="f1d41-162">32 KB</span></span> | <span data-ttu-id="f1d41-163">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="f1d41-164">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="f1d41-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f1d41-165">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="f1d41-165">All Transports are enabled.</span></span> | <span data-ttu-id="f1d41-166">一个位标志 `HttpTransportType` 值的枚举，这些值可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f1d41-167">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="f1d41-167">See below.</span></span> | <span data-ttu-id="f1d41-168">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f1d41-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="f1d41-169">See below.</span></span> | <span data-ttu-id="f1d41-170">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f1d41-171">长轮询传输具有可使用 `LongPolling` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f1d41-172">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-172">Option</span></span> | <span data-ttu-id="f1d41-173">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-173">Default Value</span></span> | <span data-ttu-id="f1d41-174">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f1d41-175">90秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-175">90 seconds</span></span> | <span data-ttu-id="f1d41-176">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f1d41-177">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="f1d41-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f1d41-178">WebSocket 传输具有可使用 `WebSockets` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f1d41-179">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-179">Option</span></span> | <span data-ttu-id="f1d41-180">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-180">Default Value</span></span> | <span data-ttu-id="f1d41-181">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f1d41-182">5 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-182">5 seconds</span></span> | <span data-ttu-id="f1d41-183">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="f1d41-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f1d41-184">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f1d41-185">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f1d41-186">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-186">Configure client options</span></span>

<span data-ttu-id="f1d41-187">可以在 `HubConnectionBuilder` 类型上配置客户端选项（在 .NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f1d41-188">Java 客户端中也提供了此功能，但 `HttpHubConnectionBuilder` 子类包含生成器配置选项，以及 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="f1d41-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f1d41-189">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="f1d41-189">Configure logging</span></span>

<span data-ttu-id="f1d41-190">使用 `ConfigureLogging` 方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="f1d41-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f1d41-191">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="f1d41-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f1d41-192">请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f1d41-193">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="f1d41-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f1d41-194">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="f1d41-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f1d41-195">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f1d41-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f1d41-196">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="f1d41-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f1d41-197">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="f1d41-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f1d41-198">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="f1d41-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f1d41-199">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="f1d41-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="f1d41-200">您还可以提供表示日志级别名称的 `string` 值，而不是 `LogLevel` 值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="f1d41-201">当你在无权访问 `LogLevel` 常量的环境中配置 SignalR 日志记录时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="f1d41-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="f1d41-202">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="f1d41-202">The following table lists the available log levels.</span></span> <span data-ttu-id="f1d41-203">你为 `configureLogging` 提供的值设置将记录的**最小**日志级别。</span><span class="sxs-lookup"><span data-stu-id="f1d41-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="f1d41-204">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="f1d41-205">字符串</span><span class="sxs-lookup"><span data-stu-id="f1d41-205">String</span></span>                      | <span data-ttu-id="f1d41-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="f1d41-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="f1d41-207">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="f1d41-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="f1d41-208">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="f1d41-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="f1d41-209">若要完全禁用日志记录，请在 `configureLogging` 方法中指定 `signalR.LogLevel.None`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f1d41-210">有关日志记录的详细信息，请参阅[SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f1d41-211">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="f1d41-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f1d41-212">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="f1d41-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f1d41-213">下面的代码段演示如何将 `java.util.logging` 与 SignalR Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="f1d41-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f1d41-214">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="f1d41-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f1d41-215">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="f1d41-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f1d41-216">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="f1d41-216">Configure allowed transports</span></span>

<span data-ttu-id="f1d41-217">SignalR 使用的传输可以在 `WithUrl` 调用中配置（在 JavaScript 中为`withUrl`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f1d41-218">`HttpTransportType` 的值的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f1d41-219">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-219">All transports are enabled by default.</span></span>

<span data-ttu-id="f1d41-220">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f1d41-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f1d41-221">在 JavaScript 客户端中，通过在提供给 `withUrl`的 options 对象上设置 `transport` 字段来配置传输：</span><span class="sxs-lookup"><span data-stu-id="f1d41-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f1d41-222">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="f1d41-223">在 Java 客户端中，通过 `HttpHubConnectionBuilder`上的 `withTransport` 方法选择传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="f1d41-224">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f1d41-225">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="f1d41-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f1d41-226">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="f1d41-226">Configure bearer authentication</span></span>

<span data-ttu-id="f1d41-227">若要将身份验证数据与 SignalR 请求一起提供，请使用 `AccessTokenProvider` 选项（在 JavaScript 中为`accessTokenFactory`）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f1d41-228">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Bearer`类型的 `Authorization` 标头）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f1d41-229">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f1d41-230">在这些情况下，访问令牌作为查询字符串值提供 `access_token`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f1d41-231">在 .NET 客户端中，可以使用 `WithUrl`中的 options 委托指定 `AccessTokenProvider` 选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f1d41-232">在 JavaScript 客户端中，通过在 `withUrl`中的 options 对象上设置 `accessTokenFactory` 字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="f1d41-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="f1d41-233">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="f1d41-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f1d41-234">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一\<字符串 >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f1d41-235">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f1d41-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f1d41-236">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f1d41-237">用于配置超时和保持活动状态的其他选项在 `HubConnection` 对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="f1d41-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="f1d41-238">.NET</span><span class="sxs-lookup"><span data-stu-id="f1d41-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f1d41-239">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-239">Option</span></span> | <span data-ttu-id="f1d41-240">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-240">Default value</span></span> | <span data-ttu-id="f1d41-241">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f1d41-242">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-243">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-243">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-244">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f1d41-245">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-246">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f1d41-247">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-247">15 seconds</span></span> | <span data-ttu-id="f1d41-248">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="f1d41-249">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f1d41-250">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-251">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f1d41-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-252">15 seconds</span></span> | <span data-ttu-id="f1d41-253">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="f1d41-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f1d41-254">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f1d41-255">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f1d41-256">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="f1d41-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f1d41-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f1d41-258">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-258">Option</span></span> | <span data-ttu-id="f1d41-259">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-259">Default value</span></span> | <span data-ttu-id="f1d41-260">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f1d41-261">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-262">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-262">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-263">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f1d41-264">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-265">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f1d41-266">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-267">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="f1d41-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f1d41-268">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f1d41-269">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="f1d41-270">Java</span><span class="sxs-lookup"><span data-stu-id="f1d41-270">Java</span></span>](#tab/java)

| <span data-ttu-id="f1d41-271">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-271">Option</span></span> | <span data-ttu-id="f1d41-272">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-272">Default value</span></span> | <span data-ttu-id="f1d41-273">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f1d41-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f1d41-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f1d41-275">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-276">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-276">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-277">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f1d41-278">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-279">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f1d41-280">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-280">15 seconds</span></span> | <span data-ttu-id="f1d41-281">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="f1d41-282">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f1d41-283">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-284">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f1d41-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f1d41-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f1d41-286">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-287">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="f1d41-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f1d41-288">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f1d41-289">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f1d41-290">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-290">Configure additional options</span></span>

<span data-ttu-id="f1d41-291">可以在 `HubConnectionBuilder` 上的 `WithUrl` （JavaScript 中的`withUrl`）方法中配置其他选项，或在 Java 客户端中 `HttpHubConnectionBuilder` 上的各种配置 Api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="f1d41-292">.NET</span><span class="sxs-lookup"><span data-stu-id="f1d41-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f1d41-293">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-293">.NET Option</span></span> |  <span data-ttu-id="f1d41-294">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-294">Default value</span></span> | <span data-ttu-id="f1d41-295">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f1d41-296">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f1d41-297">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-298">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-299">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f1d41-300">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-300">Empty</span></span> | <span data-ttu-id="f1d41-301">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="f1d41-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="f1d41-302">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-302">Empty</span></span> | <span data-ttu-id="f1d41-303">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="f1d41-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f1d41-304">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-304">Empty</span></span> | <span data-ttu-id="f1d41-305">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f1d41-306">5 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-306">5 seconds</span></span> | <span data-ttu-id="f1d41-307">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="f1d41-307">WebSockets only.</span></span> <span data-ttu-id="f1d41-308">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f1d41-309">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f1d41-310">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-310">Empty</span></span> | <span data-ttu-id="f1d41-311">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="f1d41-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f1d41-312">一个委托，可用于配置或替换用于发送 HTTP 请求的 `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f1d41-313">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="f1d41-314">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f1d41-315">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="f1d41-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f1d41-316">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="f1d41-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f1d41-317">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="f1d41-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f1d41-318">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f1d41-319">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f1d41-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f1d41-320">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="f1d41-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f1d41-321">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="f1d41-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="f1d41-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f1d41-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f1d41-323">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-323">JavaScript Option</span></span> | <span data-ttu-id="f1d41-324">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-324">Default Value</span></span> | <span data-ttu-id="f1d41-325">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f1d41-326">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f1d41-327">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-328">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-329">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="f1d41-330">Java</span><span class="sxs-lookup"><span data-stu-id="f1d41-330">Java</span></span>](#tab/java)

| <span data-ttu-id="f1d41-331">Java 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-331">Java Option</span></span> | <span data-ttu-id="f1d41-332">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-332">Default Value</span></span> | <span data-ttu-id="f1d41-333">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f1d41-334">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f1d41-335">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-336">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-337">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="f1d41-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f1d41-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f1d41-339">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-339">Empty</span></span> | <span data-ttu-id="f1d41-340">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="f1d41-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f1d41-341">在 .NET 客户端中，可以通过提供给 `WithUrl`的 options 委托来修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f1d41-342">在 JavaScript 客户端中，可以在提供给 `withUrl`的 JavaScript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f1d41-343">在 Java 客户端中，可以通过从 `HubConnectionBuilder.create("HUB URL")` 返回的 `HttpHubConnectionBuilder` 中的方法来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f1d41-344">其他资源</span><span class="sxs-lookup"><span data-stu-id="f1d41-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f1d41-345">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f1d41-346">ASP.NET Core SignalR 支持两种用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f1d41-347">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f1d41-348">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该方法可在 `Startup.ConfigureServices` 方法中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="f1d41-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f1d41-349">`AddJsonProtocol` 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="f1d41-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f1d41-350">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于配置自变量和返回值的序列化 `JsonSerializerSettings` 对象的 JSON.NET。</span><span class="sxs-lookup"><span data-stu-id="f1d41-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f1d41-351">有关详细信息，请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="f1d41-352">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在 `Startup.ConfigureServices`中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="f1d41-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="f1d41-353">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f1d41-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f1d41-354">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="f1d41-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="f1d41-355">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f1d41-356">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-356">MessagePack serialization options</span></span>

<span data-ttu-id="f1d41-357">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f1d41-358">有关更多详细信息，请参阅[SignalR中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="f1d41-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f1d41-359">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f1d41-360">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-360">Configure server options</span></span>

<span data-ttu-id="f1d41-361">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="f1d41-362">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-362">Option</span></span> | <span data-ttu-id="f1d41-363">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-363">Default Value</span></span> | <span data-ttu-id="f1d41-364">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="f1d41-365">30 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-365">30 seconds</span></span> | <span data-ttu-id="f1d41-366">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="f1d41-367">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="f1d41-368">建议值为 `KeepAliveInterval` 值的两倍。</span><span class="sxs-lookup"><span data-stu-id="f1d41-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="f1d41-369">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-369">15 seconds</span></span> | <span data-ttu-id="f1d41-370">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="f1d41-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f1d41-371">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-372">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f1d41-373">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-373">15 seconds</span></span> | <span data-ttu-id="f1d41-374">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="f1d41-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f1d41-375">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f1d41-376">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f1d41-377">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="f1d41-377">All installed protocols</span></span> | <span data-ttu-id="f1d41-378">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="f1d41-378">Protocols supported by this hub.</span></span> <span data-ttu-id="f1d41-379">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="f1d41-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f1d41-380">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="f1d41-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f1d41-381">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="f1d41-382">可以通过向 `Startup.ConfigureServices`中的 `AddSignalR` 调用提供选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="f1d41-383">单个集线器的选项将覆盖 `AddSignalR` 中提供的全局选项，并且可以使用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="f1d41-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f1d41-384">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f1d41-385">使用 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f1d41-386">可以通过将委托传递到 `Startup.Configure`中的[MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="f1d41-387">下表描述了用于配置 ASP.NET Core SignalR的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="f1d41-388">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-388">Option</span></span> | <span data-ttu-id="f1d41-389">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-389">Default Value</span></span> | <span data-ttu-id="f1d41-390">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f1d41-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="f1d41-391">32 KB</span></span> | <span data-ttu-id="f1d41-392">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="f1d41-393">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="f1d41-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f1d41-394">从应用于 Hub 类的 `Authorize` 属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f1d41-395">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="f1d41-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f1d41-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="f1d41-396">32 KB</span></span> | <span data-ttu-id="f1d41-397">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="f1d41-398">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="f1d41-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f1d41-399">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="f1d41-399">All Transports are enabled.</span></span> | <span data-ttu-id="f1d41-400">一个位标志 `HttpTransportType` 值的枚举，这些值可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f1d41-401">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="f1d41-401">See below.</span></span> | <span data-ttu-id="f1d41-402">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f1d41-403">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="f1d41-403">See below.</span></span> | <span data-ttu-id="f1d41-404">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f1d41-405">长轮询传输具有可使用 `LongPolling` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f1d41-406">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-406">Option</span></span> | <span data-ttu-id="f1d41-407">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-407">Default Value</span></span> | <span data-ttu-id="f1d41-408">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f1d41-409">90秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-409">90 seconds</span></span> | <span data-ttu-id="f1d41-410">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f1d41-411">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="f1d41-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f1d41-412">WebSocket 传输具有可使用 `WebSockets` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f1d41-413">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-413">Option</span></span> | <span data-ttu-id="f1d41-414">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-414">Default Value</span></span> | <span data-ttu-id="f1d41-415">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f1d41-416">5 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-416">5 seconds</span></span> | <span data-ttu-id="f1d41-417">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="f1d41-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f1d41-418">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f1d41-419">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f1d41-420">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-420">Configure client options</span></span>

<span data-ttu-id="f1d41-421">可以在 `HubConnectionBuilder` 类型上配置客户端选项（在 .NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f1d41-422">Java 客户端中也提供了此功能，但 `HttpHubConnectionBuilder` 子类包含生成器配置选项，以及 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="f1d41-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f1d41-423">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="f1d41-423">Configure logging</span></span>

<span data-ttu-id="f1d41-424">使用 `ConfigureLogging` 方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="f1d41-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f1d41-425">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="f1d41-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f1d41-426">请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f1d41-427">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="f1d41-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f1d41-428">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="f1d41-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f1d41-429">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f1d41-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f1d41-430">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="f1d41-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f1d41-431">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="f1d41-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f1d41-432">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="f1d41-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f1d41-433">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="f1d41-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f1d41-434">若要完全禁用日志记录，请在 `configureLogging` 方法中指定 `signalR.LogLevel.None`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f1d41-435">有关日志记录的详细信息，请参阅[SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f1d41-436">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="f1d41-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f1d41-437">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="f1d41-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f1d41-438">下面的代码段演示如何将 `java.util.logging` 与 SignalR Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="f1d41-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f1d41-439">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="f1d41-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f1d41-440">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="f1d41-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f1d41-441">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="f1d41-441">Configure allowed transports</span></span>

<span data-ttu-id="f1d41-442">SignalR 使用的传输可以在 `WithUrl` 调用中配置（在 JavaScript 中为`withUrl`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f1d41-443">`HttpTransportType` 的值的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f1d41-444">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-444">All transports are enabled by default.</span></span>

<span data-ttu-id="f1d41-445">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f1d41-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f1d41-446">在 JavaScript 客户端中，通过在提供给 `withUrl`的 options 对象上设置 `transport` 字段来配置传输：</span><span class="sxs-lookup"><span data-stu-id="f1d41-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="f1d41-447">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f1d41-448">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="f1d41-448">Configure bearer authentication</span></span>

<span data-ttu-id="f1d41-449">若要将身份验证数据与 SignalR 请求一起提供，请使用 `AccessTokenProvider` 选项（在 JavaScript 中为`accessTokenFactory`）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f1d41-450">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Bearer`类型的 `Authorization` 标头）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f1d41-451">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f1d41-452">在这些情况下，访问令牌作为查询字符串值提供 `access_token`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f1d41-453">在 .NET 客户端中，可以使用 `WithUrl`中的 options 委托指定 `AccessTokenProvider` 选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f1d41-454">在 JavaScript 客户端中，通过在 `withUrl`中的 options 对象上设置 `accessTokenFactory` 字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="f1d41-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="f1d41-455">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="f1d41-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f1d41-456">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一\<字符串 >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f1d41-457">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f1d41-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f1d41-458">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f1d41-459">用于配置超时和保持活动状态的其他选项在 `HubConnection` 对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="f1d41-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="f1d41-460">.NET</span><span class="sxs-lookup"><span data-stu-id="f1d41-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f1d41-461">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-461">Option</span></span> | <span data-ttu-id="f1d41-462">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-462">Default value</span></span> | <span data-ttu-id="f1d41-463">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f1d41-464">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-465">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-465">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-466">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f1d41-467">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-468">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f1d41-469">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-469">15 seconds</span></span> | <span data-ttu-id="f1d41-470">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="f1d41-471">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f1d41-472">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-473">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f1d41-474">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-474">15 seconds</span></span> | <span data-ttu-id="f1d41-475">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="f1d41-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f1d41-476">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f1d41-477">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="f1d41-478">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="f1d41-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f1d41-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f1d41-480">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-480">Option</span></span> | <span data-ttu-id="f1d41-481">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-481">Default value</span></span> | <span data-ttu-id="f1d41-482">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f1d41-483">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-484">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-484">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-485">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f1d41-486">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-487">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="f1d41-488">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-489">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="f1d41-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f1d41-490">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f1d41-491">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="f1d41-492">Java</span><span class="sxs-lookup"><span data-stu-id="f1d41-492">Java</span></span>](#tab/java)

| <span data-ttu-id="f1d41-493">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-493">Option</span></span> | <span data-ttu-id="f1d41-494">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-494">Default value</span></span> | <span data-ttu-id="f1d41-495">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f1d41-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f1d41-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f1d41-497">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-498">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-498">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-499">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f1d41-500">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-501">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f1d41-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-502">15 seconds</span></span> | <span data-ttu-id="f1d41-503">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="f1d41-504">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f1d41-505">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-506">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="f1d41-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="f1d41-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="f1d41-508">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-509">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="f1d41-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="f1d41-510">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="f1d41-511">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f1d41-512">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-512">Configure additional options</span></span>

<span data-ttu-id="f1d41-513">可以在 `HubConnectionBuilder` 上的 `WithUrl` （JavaScript 中的`withUrl`）方法中配置其他选项，或在 Java 客户端中 `HttpHubConnectionBuilder` 上的各种配置 Api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="f1d41-514">.NET</span><span class="sxs-lookup"><span data-stu-id="f1d41-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f1d41-515">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-515">.NET Option</span></span> |  <span data-ttu-id="f1d41-516">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-516">Default value</span></span> | <span data-ttu-id="f1d41-517">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f1d41-518">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f1d41-519">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-520">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-521">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f1d41-522">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-522">Empty</span></span> | <span data-ttu-id="f1d41-523">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="f1d41-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="f1d41-524">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-524">Empty</span></span> | <span data-ttu-id="f1d41-525">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="f1d41-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f1d41-526">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-526">Empty</span></span> | <span data-ttu-id="f1d41-527">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f1d41-528">5 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-528">5 seconds</span></span> | <span data-ttu-id="f1d41-529">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="f1d41-529">WebSockets only.</span></span> <span data-ttu-id="f1d41-530">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f1d41-531">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f1d41-532">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-532">Empty</span></span> | <span data-ttu-id="f1d41-533">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="f1d41-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f1d41-534">一个委托，可用于配置或替换用于发送 HTTP 请求的 `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f1d41-535">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="f1d41-536">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f1d41-537">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="f1d41-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f1d41-538">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="f1d41-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f1d41-539">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="f1d41-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f1d41-540">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f1d41-541">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f1d41-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f1d41-542">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="f1d41-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f1d41-543">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="f1d41-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="f1d41-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f1d41-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f1d41-545">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-545">JavaScript Option</span></span> | <span data-ttu-id="f1d41-546">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-546">Default Value</span></span> | <span data-ttu-id="f1d41-547">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f1d41-548">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f1d41-549">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-550">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-551">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="f1d41-552">Java</span><span class="sxs-lookup"><span data-stu-id="f1d41-552">Java</span></span>](#tab/java)

| <span data-ttu-id="f1d41-553">Java 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-553">Java Option</span></span> | <span data-ttu-id="f1d41-554">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-554">Default Value</span></span> | <span data-ttu-id="f1d41-555">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f1d41-556">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f1d41-557">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-558">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-559">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="f1d41-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f1d41-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f1d41-561">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-561">Empty</span></span> | <span data-ttu-id="f1d41-562">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="f1d41-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f1d41-563">在 .NET 客户端中，可以通过提供给 `WithUrl`的 options 委托来修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f1d41-564">在 JavaScript 客户端中，可以在提供给 `withUrl`的 JavaScript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f1d41-565">在 Java 客户端中，可以通过从 `HubConnectionBuilder.create("HUB URL")` 返回的 `HttpHubConnectionBuilder` 中的方法来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f1d41-566">其他资源</span><span class="sxs-lookup"><span data-stu-id="f1d41-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="f1d41-567">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="f1d41-568">ASP.NET Core SignalR 支持两种用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="f1d41-569">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="f1d41-570">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该方法可在 `Startup.ConfigureServices` 方法中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="f1d41-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f1d41-571">`AddJsonProtocol` 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="f1d41-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="f1d41-572">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于配置自变量和返回值的序列化 `JsonSerializerSettings` 对象的 JSON.NET。</span><span class="sxs-lookup"><span data-stu-id="f1d41-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="f1d41-573">有关详细信息，请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="f1d41-574">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在 `Startup.ConfigureServices`中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="f1d41-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="f1d41-575">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f1d41-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="f1d41-576">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="f1d41-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="f1d41-577">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="f1d41-578">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-578">MessagePack serialization options</span></span>

<span data-ttu-id="f1d41-579">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="f1d41-580">有关更多详细信息，请参阅[SignalR中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="f1d41-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="f1d41-581">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="f1d41-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="f1d41-582">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-582">Configure server options</span></span>

<span data-ttu-id="f1d41-583">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="f1d41-584">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-584">Option</span></span> | <span data-ttu-id="f1d41-585">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-585">Default Value</span></span> | <span data-ttu-id="f1d41-586">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="f1d41-587">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-587">15 seconds</span></span> | <span data-ttu-id="f1d41-588">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="f1d41-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="f1d41-589">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-590">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="f1d41-591">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-591">15 seconds</span></span> | <span data-ttu-id="f1d41-592">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="f1d41-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="f1d41-593">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="f1d41-594">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="f1d41-595">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="f1d41-595">All installed protocols</span></span> | <span data-ttu-id="f1d41-596">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="f1d41-596">Protocols supported by this hub.</span></span> <span data-ttu-id="f1d41-597">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="f1d41-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="f1d41-598">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="f1d41-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="f1d41-599">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="f1d41-600">可以通过向 `Startup.ConfigureServices`中的 `AddSignalR` 调用提供选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="f1d41-601">单个集线器的选项将覆盖 `AddSignalR` 中提供的全局选项，并且可以使用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="f1d41-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="f1d41-602">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="f1d41-603">使用 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="f1d41-604">可以通过将委托传递到 `Startup.Configure`中的[MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="f1d41-605">下表描述了用于配置 ASP.NET Core SignalR的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="f1d41-606">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-606">Option</span></span> | <span data-ttu-id="f1d41-607">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-607">Default Value</span></span> | <span data-ttu-id="f1d41-608">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="f1d41-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="f1d41-609">32 KB</span></span> | <span data-ttu-id="f1d41-610">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="f1d41-611">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="f1d41-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="f1d41-612">从应用于 Hub 类的 `Authorize` 属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="f1d41-613">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="f1d41-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="f1d41-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="f1d41-614">32 KB</span></span> | <span data-ttu-id="f1d41-615">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="f1d41-616">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="f1d41-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="f1d41-617">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="f1d41-617">All Transports are enabled.</span></span> | <span data-ttu-id="f1d41-618">一个位标志 `HttpTransportType` 值的枚举，这些值可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="f1d41-619">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="f1d41-619">See below.</span></span> | <span data-ttu-id="f1d41-620">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="f1d41-621">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="f1d41-621">See below.</span></span> | <span data-ttu-id="f1d41-622">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="f1d41-623">长轮询传输具有可使用 `LongPolling` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="f1d41-624">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-624">Option</span></span> | <span data-ttu-id="f1d41-625">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-625">Default Value</span></span> | <span data-ttu-id="f1d41-626">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="f1d41-627">90秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-627">90 seconds</span></span> | <span data-ttu-id="f1d41-628">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="f1d41-629">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="f1d41-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="f1d41-630">WebSocket 传输具有可使用 `WebSockets` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="f1d41-631">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-631">Option</span></span> | <span data-ttu-id="f1d41-632">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-632">Default Value</span></span> | <span data-ttu-id="f1d41-633">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="f1d41-634">5 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-634">5 seconds</span></span> | <span data-ttu-id="f1d41-635">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="f1d41-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="f1d41-636">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="f1d41-637">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="f1d41-638">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-638">Configure client options</span></span>

<span data-ttu-id="f1d41-639">可以在 `HubConnectionBuilder` 类型上配置客户端选项（在 .NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="f1d41-640">Java 客户端中也提供了此功能，但 `HttpHubConnectionBuilder` 子类包含生成器配置选项，以及 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="f1d41-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="f1d41-641">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="f1d41-641">Configure logging</span></span>

<span data-ttu-id="f1d41-642">使用 `ConfigureLogging` 方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="f1d41-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="f1d41-643">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="f1d41-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="f1d41-644">请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f1d41-645">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="f1d41-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="f1d41-646">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="f1d41-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="f1d41-647">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f1d41-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="f1d41-648">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="f1d41-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="f1d41-649">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="f1d41-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="f1d41-650">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="f1d41-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="f1d41-651">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="f1d41-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="f1d41-652">若要完全禁用日志记录，请在 `configureLogging` 方法中指定 `signalR.LogLevel.None`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f1d41-653">有关日志记录的详细信息，请参阅[SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="f1d41-654">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="f1d41-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f1d41-655">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="f1d41-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f1d41-656">下面的代码段演示如何将 `java.util.logging` 与 SignalR Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="f1d41-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f1d41-657">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="f1d41-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f1d41-658">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="f1d41-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="f1d41-659">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="f1d41-659">Configure allowed transports</span></span>

<span data-ttu-id="f1d41-660">SignalR 使用的传输可以在 `WithUrl` 调用中配置（在 JavaScript 中为`withUrl`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="f1d41-661">`HttpTransportType` 的值的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="f1d41-662">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="f1d41-662">All transports are enabled by default.</span></span>

<span data-ttu-id="f1d41-663">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f1d41-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="f1d41-664">在 JavaScript 客户端中，通过在提供给 `withUrl`的 options 对象上设置 `transport` 字段来配置传输：</span><span class="sxs-lookup"><span data-stu-id="f1d41-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="f1d41-665">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="f1d41-665">Configure bearer authentication</span></span>

<span data-ttu-id="f1d41-666">若要将身份验证数据与 SignalR 请求一起提供，请使用 `AccessTokenProvider` 选项（在 JavaScript 中为`accessTokenFactory`）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="f1d41-667">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Bearer`类型的 `Authorization` 标头）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="f1d41-668">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="f1d41-669">在这些情况下，访问令牌作为查询字符串值提供 `access_token`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="f1d41-670">在 .NET 客户端中，可以使用 `WithUrl`中的 options 委托指定 `AccessTokenProvider` 选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="f1d41-671">在 JavaScript 客户端中，通过在 `withUrl`中的 options 对象上设置 `accessTokenFactory` 字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="f1d41-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="f1d41-672">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="f1d41-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f1d41-673">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一\<字符串 >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f1d41-674">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f1d41-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="f1d41-675">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="f1d41-676">用于配置超时和保持活动状态的其他选项在 `HubConnection` 对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="f1d41-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="f1d41-677">.NET</span><span class="sxs-lookup"><span data-stu-id="f1d41-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f1d41-678">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-678">Option</span></span> | <span data-ttu-id="f1d41-679">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-679">Default value</span></span> | <span data-ttu-id="f1d41-680">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="f1d41-681">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-682">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-682">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-683">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f1d41-684">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-685">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="f1d41-686">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-686">15 seconds</span></span> | <span data-ttu-id="f1d41-687">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="f1d41-688">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="f1d41-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="f1d41-689">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-690">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="f1d41-691">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="f1d41-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="f1d41-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f1d41-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f1d41-693">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-693">Option</span></span> | <span data-ttu-id="f1d41-694">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-694">Default value</span></span> | <span data-ttu-id="f1d41-695">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="f1d41-696">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-697">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-697">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-698">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="f1d41-699">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-700">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="f1d41-701">Java</span><span class="sxs-lookup"><span data-stu-id="f1d41-701">Java</span></span>](#tab/java)

| <span data-ttu-id="f1d41-702">选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-702">Option</span></span> | <span data-ttu-id="f1d41-703">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-703">Default value</span></span> | <span data-ttu-id="f1d41-704">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="f1d41-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="f1d41-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="f1d41-706">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="f1d41-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="f1d41-707">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="f1d41-707">Timeout for server activity.</span></span> <span data-ttu-id="f1d41-708">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="f1d41-709">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="f1d41-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="f1d41-710">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="f1d41-711">15 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-711">15 seconds</span></span> | <span data-ttu-id="f1d41-712">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="f1d41-713">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="f1d41-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="f1d41-714">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1d41-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="f1d41-715">有关握手过程的详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="f1d41-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="f1d41-716">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-716">Configure additional options</span></span>

<span data-ttu-id="f1d41-717">可以在 `HubConnectionBuilder` 上的 `WithUrl` （JavaScript 中的`withUrl`）方法中配置其他选项，或在 Java 客户端中 `HttpHubConnectionBuilder` 上的各种配置 Api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="f1d41-718">.NET</span><span class="sxs-lookup"><span data-stu-id="f1d41-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="f1d41-719">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-719">.NET Option</span></span> |  <span data-ttu-id="f1d41-720">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-720">Default value</span></span> | <span data-ttu-id="f1d41-721">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="f1d41-722">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="f1d41-723">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-724">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-725">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="f1d41-726">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-726">Empty</span></span> | <span data-ttu-id="f1d41-727">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="f1d41-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="f1d41-728">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-728">Empty</span></span> | <span data-ttu-id="f1d41-729">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="f1d41-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="f1d41-730">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-730">Empty</span></span> | <span data-ttu-id="f1d41-731">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="f1d41-732">5 秒</span><span class="sxs-lookup"><span data-stu-id="f1d41-732">5 seconds</span></span> | <span data-ttu-id="f1d41-733">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="f1d41-733">WebSockets only.</span></span> <span data-ttu-id="f1d41-734">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="f1d41-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="f1d41-735">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="f1d41-736">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-736">Empty</span></span> | <span data-ttu-id="f1d41-737">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="f1d41-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="f1d41-738">一个委托，可用于配置或替换用于发送 HTTP 请求的 `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="f1d41-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="f1d41-739">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="f1d41-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="f1d41-740">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="f1d41-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="f1d41-741">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="f1d41-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="f1d41-742">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="f1d41-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="f1d41-743">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="f1d41-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="f1d41-744">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="f1d41-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="f1d41-745">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f1d41-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="f1d41-746">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="f1d41-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="f1d41-747">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="f1d41-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="f1d41-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="f1d41-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="f1d41-749">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-749">JavaScript Option</span></span> | <span data-ttu-id="f1d41-750">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-750">Default Value</span></span> | <span data-ttu-id="f1d41-751">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="f1d41-752">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="f1d41-753">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-754">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-755">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="f1d41-756">Java</span><span class="sxs-lookup"><span data-stu-id="f1d41-756">Java</span></span>](#tab/java)

| <span data-ttu-id="f1d41-757">Java 选项</span><span class="sxs-lookup"><span data-stu-id="f1d41-757">Java Option</span></span> | <span data-ttu-id="f1d41-758">默认值</span><span class="sxs-lookup"><span data-stu-id="f1d41-758">Default Value</span></span> | <span data-ttu-id="f1d41-759">描述</span><span class="sxs-lookup"><span data-stu-id="f1d41-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="f1d41-760">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="f1d41-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="f1d41-761">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="f1d41-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="f1d41-762">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="f1d41-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="f1d41-763">使用 Azure SignalR 服务时，无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="f1d41-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="f1d41-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="f1d41-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="f1d41-765">空</span><span class="sxs-lookup"><span data-stu-id="f1d41-765">Empty</span></span> | <span data-ttu-id="f1d41-766">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="f1d41-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="f1d41-767">在 .NET 客户端中，可以通过提供给 `WithUrl`的 options 委托来修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="f1d41-768">在 JavaScript 客户端中，可以在提供给 `withUrl`的 JavaScript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="f1d41-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="f1d41-769">在 Java 客户端中，可以通过从 `HubConnectionBuilder.create("HUB URL")` 返回的 `HttpHubConnectionBuilder` 中的方法来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="f1d41-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="f1d41-770">其他资源</span><span class="sxs-lookup"><span data-stu-id="f1d41-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end