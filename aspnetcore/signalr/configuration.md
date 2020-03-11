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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651684"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="04925-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="04925-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="04925-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="04925-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="04925-105">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="04925-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="04925-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="04925-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="04925-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="04925-108">`AddJsonProtocol`[可以在 `Startup.ConfigureServices`后添加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)。</span><span class="sxs-lookup"><span data-stu-id="04925-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="04925-109">`AddJsonProtocol` 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="04925-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="04925-110">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="04925-111">有关详细信息，请参阅[system.object 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="04925-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="04925-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请在 `Startup.ConfigureServices`中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="04925-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="04925-113">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="04925-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="04925-114">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="04925-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="04925-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="04925-116">切换到 Newtonsoft.json</span><span class="sxs-lookup"><span data-stu-id="04925-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="04925-117">如果需要 `System.Text.Json`不支持的 `Newtonsoft.Json` 功能，请参阅[切换到 newtonsoft.json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="04925-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="04925-118">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="04925-118">MessagePack serialization options</span></span>

<span data-ttu-id="04925-119">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="04925-120">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="04925-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="04925-121">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="04925-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="04925-122">Configure server options</span></span>

<span data-ttu-id="04925-123">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="04925-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="04925-124">选项</span><span class="sxs-lookup"><span data-stu-id="04925-124">Option</span></span> | <span data-ttu-id="04925-125">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-125">Default Value</span></span> | <span data-ttu-id="04925-126">说明</span><span class="sxs-lookup"><span data-stu-id="04925-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="04925-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="04925-127">30 seconds</span></span> | <span data-ttu-id="04925-128">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="04925-129">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="04925-130">建议值为 `KeepAliveInterval` 值的两倍。</span><span class="sxs-lookup"><span data-stu-id="04925-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="04925-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-131">15 seconds</span></span> | <span data-ttu-id="04925-132">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="04925-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="04925-133">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-134">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="04925-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-135">15 seconds</span></span> | <span data-ttu-id="04925-136">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="04925-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="04925-137">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="04925-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="04925-138">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="04925-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="04925-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="04925-139">All installed protocols</span></span> | <span data-ttu-id="04925-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="04925-140">Protocols supported by this hub.</span></span> <span data-ttu-id="04925-141">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="04925-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="04925-142">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="04925-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="04925-143">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="04925-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="04925-144">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="04925-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="04925-145">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="04925-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="04925-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="04925-146">32 KB</span></span> | <span data-ttu-id="04925-147">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="04925-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="04925-148">可以通过向 `Startup.ConfigureServices`中的 `AddSignalR` 调用提供选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="04925-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="04925-149">单个集线器的选项将覆盖 `AddSignalR` 中提供的全局选项，并且可以使用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="04925-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="04925-150">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="04925-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="04925-151">使用 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="04925-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="04925-152">可以通过将委托传递到 `Startup.Configure`中的[MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="04925-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="04925-153">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="04925-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="04925-154">选项</span><span class="sxs-lookup"><span data-stu-id="04925-154">Option</span></span> | <span data-ttu-id="04925-155">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-155">Default Value</span></span> | <span data-ttu-id="04925-156">说明</span><span class="sxs-lookup"><span data-stu-id="04925-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="04925-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="04925-157">32 KB</span></span> | <span data-ttu-id="04925-158">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="04925-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="04925-159">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="04925-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="04925-160">从应用于 Hub 类的 `Authorize` 属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="04925-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="04925-161">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="04925-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="04925-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="04925-162">32 KB</span></span> | <span data-ttu-id="04925-163">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="04925-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="04925-164">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="04925-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="04925-165">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="04925-165">All Transports are enabled.</span></span> | <span data-ttu-id="04925-166">一个位标志 `HttpTransportType` 值的枚举，这些值可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="04925-167">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="04925-167">See below.</span></span> | <span data-ttu-id="04925-168">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="04925-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="04925-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="04925-169">See below.</span></span> | <span data-ttu-id="04925-170">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="04925-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="04925-171">长轮询传输具有可使用 `LongPolling` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="04925-172">选项</span><span class="sxs-lookup"><span data-stu-id="04925-172">Option</span></span> | <span data-ttu-id="04925-173">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-173">Default Value</span></span> | <span data-ttu-id="04925-174">说明</span><span class="sxs-lookup"><span data-stu-id="04925-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="04925-175">90秒</span><span class="sxs-lookup"><span data-stu-id="04925-175">90 seconds</span></span> | <span data-ttu-id="04925-176">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="04925-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="04925-177">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="04925-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="04925-178">WebSocket 传输具有可使用 `WebSockets` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="04925-179">选项</span><span class="sxs-lookup"><span data-stu-id="04925-179">Option</span></span> | <span data-ttu-id="04925-180">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-180">Default Value</span></span> | <span data-ttu-id="04925-181">说明</span><span class="sxs-lookup"><span data-stu-id="04925-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="04925-182">5 秒</span><span class="sxs-lookup"><span data-stu-id="04925-182">5 seconds</span></span> | <span data-ttu-id="04925-183">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="04925-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="04925-184">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="04925-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="04925-185">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="04925-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="04925-186">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="04925-186">Configure client options</span></span>

<span data-ttu-id="04925-187">可以在 `HubConnectionBuilder` 类型上配置客户端选项（在 .NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="04925-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="04925-188">Java 客户端中也提供了此功能，但 `HttpHubConnectionBuilder` 子类包含生成器配置选项，以及 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="04925-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="04925-189">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="04925-189">Configure logging</span></span>

<span data-ttu-id="04925-190">使用 `ConfigureLogging` 方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="04925-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="04925-191">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="04925-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="04925-192">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="04925-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="04925-193">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="04925-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="04925-194">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="04925-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="04925-195">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="04925-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="04925-196">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="04925-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="04925-197">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="04925-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="04925-198">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="04925-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="04925-199">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="04925-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="04925-200">您还可以提供表示日志级别名称的 `string` 值，而不是 `LogLevel` 值。</span><span class="sxs-lookup"><span data-stu-id="04925-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="04925-201">在无法访问 `LogLevel` 常量的环境中配置 SignalR 日志记录时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="04925-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="04925-202">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="04925-202">The following table lists the available log levels.</span></span> <span data-ttu-id="04925-203">你为 `configureLogging` 提供的值设置将记录的**最小**日志级别。</span><span class="sxs-lookup"><span data-stu-id="04925-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="04925-204">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="04925-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="04925-205">String</span><span class="sxs-lookup"><span data-stu-id="04925-205">String</span></span>                      | <span data-ttu-id="04925-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="04925-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="04925-207">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="04925-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="04925-208">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="04925-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="04925-209">若要完全禁用日志记录，请在 `configureLogging` 方法中指定 `signalR.LogLevel.None`。</span><span class="sxs-lookup"><span data-stu-id="04925-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="04925-210">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="04925-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="04925-211">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="04925-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="04925-212">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="04925-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="04925-213">以下代码片段演示了如何在 SignalR Java 客户端中使用 `java.util.logging`。</span><span class="sxs-lookup"><span data-stu-id="04925-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="04925-214">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="04925-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="04925-215">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="04925-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="04925-216">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="04925-216">Configure allowed transports</span></span>

<span data-ttu-id="04925-217">可以在 `WithUrl` 调用（JavaScript 中的`withUrl`）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="04925-218">`HttpTransportType` 的值的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="04925-219">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="04925-219">All transports are enabled by default.</span></span>

<span data-ttu-id="04925-220">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="04925-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="04925-221">在 JavaScript 客户端中，通过在提供给 `withUrl`的 options 对象上设置 `transport` 字段来配置传输：</span><span class="sxs-lookup"><span data-stu-id="04925-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="04925-222">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="04925-223">在 Java 客户端中，通过 `HttpHubConnectionBuilder`上的 `withTransport` 方法选择传输。</span><span class="sxs-lookup"><span data-stu-id="04925-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="04925-224">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="04925-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="04925-225">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="04925-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="04925-226">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="04925-226">Configure bearer authentication</span></span>

<span data-ttu-id="04925-227">若要随 SignalR 请求一起提供身份验证数据，请使用 `AccessTokenProvider` 选项（JavaScript 中的`accessTokenFactory`）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="04925-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="04925-228">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Bearer`类型的 `Authorization` 标头）。</span><span class="sxs-lookup"><span data-stu-id="04925-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="04925-229">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="04925-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="04925-230">在这些情况下，访问令牌作为查询字符串值提供 `access_token`。</span><span class="sxs-lookup"><span data-stu-id="04925-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="04925-231">在 .NET 客户端中，可以使用 `WithUrl`中的 options 委托指定 `AccessTokenProvider` 选项：</span><span class="sxs-lookup"><span data-stu-id="04925-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="04925-232">在 JavaScript 客户端中，通过在 `withUrl`中的 options 对象上设置 `accessTokenFactory` 字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="04925-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="04925-233">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="04925-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="04925-234">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一\<字符串 >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="04925-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="04925-235">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="04925-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="04925-236">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="04925-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="04925-237">用于配置超时和保持活动状态的其他选项在 `HubConnection` 对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="04925-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="04925-238">.NET</span><span class="sxs-lookup"><span data-stu-id="04925-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="04925-239">选项</span><span class="sxs-lookup"><span data-stu-id="04925-239">Option</span></span> | <span data-ttu-id="04925-240">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-240">Default value</span></span> | <span data-ttu-id="04925-241">说明</span><span class="sxs-lookup"><span data-stu-id="04925-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="04925-242">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-243">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-243">Timeout for server activity.</span></span> <span data-ttu-id="04925-244">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="04925-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="04925-245">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-246">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="04925-247">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-247">15 seconds</span></span> | <span data-ttu-id="04925-248">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="04925-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="04925-249">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="04925-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="04925-250">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-251">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="04925-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-252">15 seconds</span></span> | <span data-ttu-id="04925-253">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="04925-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="04925-254">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="04925-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="04925-255">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="04925-256">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="04925-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="04925-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="04925-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="04925-258">选项</span><span class="sxs-lookup"><span data-stu-id="04925-258">Option</span></span> | <span data-ttu-id="04925-259">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-259">Default value</span></span> | <span data-ttu-id="04925-260">说明</span><span class="sxs-lookup"><span data-stu-id="04925-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="04925-261">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-262">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-262">Timeout for server activity.</span></span> <span data-ttu-id="04925-263">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="04925-264">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-265">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="04925-266">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="04925-267">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="04925-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="04925-268">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="04925-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="04925-269">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="04925-270">Java</span><span class="sxs-lookup"><span data-stu-id="04925-270">Java</span></span>](#tab/java)

| <span data-ttu-id="04925-271">选项</span><span class="sxs-lookup"><span data-stu-id="04925-271">Option</span></span> | <span data-ttu-id="04925-272">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-272">Default value</span></span> | <span data-ttu-id="04925-273">说明</span><span class="sxs-lookup"><span data-stu-id="04925-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="04925-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="04925-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="04925-275">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-276">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-276">Timeout for server activity.</span></span> <span data-ttu-id="04925-277">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="04925-278">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-279">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="04925-280">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-280">15 seconds</span></span> | <span data-ttu-id="04925-281">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="04925-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="04925-282">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="04925-283">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-284">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="04925-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="04925-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="04925-286">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="04925-287">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="04925-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="04925-288">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="04925-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="04925-289">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="04925-290">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="04925-290">Configure additional options</span></span>

<span data-ttu-id="04925-291">可以在 `HubConnectionBuilder` 上的 `WithUrl` （JavaScript 中的`withUrl`）方法中配置其他选项，或在 Java 客户端中 `HttpHubConnectionBuilder` 上的各种配置 Api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="04925-292">.NET</span><span class="sxs-lookup"><span data-stu-id="04925-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="04925-293">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="04925-293">.NET Option</span></span> |  <span data-ttu-id="04925-294">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-294">Default value</span></span> | <span data-ttu-id="04925-295">说明</span><span class="sxs-lookup"><span data-stu-id="04925-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="04925-296">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="04925-297">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-298">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-299">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="04925-300">空</span><span class="sxs-lookup"><span data-stu-id="04925-300">Empty</span></span> | <span data-ttu-id="04925-301">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="04925-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="04925-302">空</span><span class="sxs-lookup"><span data-stu-id="04925-302">Empty</span></span> | <span data-ttu-id="04925-303">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="04925-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="04925-304">空</span><span class="sxs-lookup"><span data-stu-id="04925-304">Empty</span></span> | <span data-ttu-id="04925-305">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="04925-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="04925-306">5 秒</span><span class="sxs-lookup"><span data-stu-id="04925-306">5 seconds</span></span> | <span data-ttu-id="04925-307">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="04925-307">WebSockets only.</span></span> <span data-ttu-id="04925-308">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="04925-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="04925-309">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="04925-310">空</span><span class="sxs-lookup"><span data-stu-id="04925-310">Empty</span></span> | <span data-ttu-id="04925-311">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="04925-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="04925-312">一个委托，可用于配置或替换用于发送 HTTP 请求的 `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="04925-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="04925-313">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="04925-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="04925-314">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="04925-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="04925-315">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="04925-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="04925-316">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="04925-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="04925-317">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="04925-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="04925-318">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="04925-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="04925-319">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="04925-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="04925-320">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="04925-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="04925-321">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="04925-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="04925-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="04925-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="04925-323">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="04925-323">JavaScript Option</span></span> | <span data-ttu-id="04925-324">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-324">Default Value</span></span> | <span data-ttu-id="04925-325">说明</span><span class="sxs-lookup"><span data-stu-id="04925-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="04925-326">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="04925-327">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-328">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-329">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="04925-330">Java</span><span class="sxs-lookup"><span data-stu-id="04925-330">Java</span></span>](#tab/java)

| <span data-ttu-id="04925-331">Java 选项</span><span class="sxs-lookup"><span data-stu-id="04925-331">Java Option</span></span> | <span data-ttu-id="04925-332">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-332">Default Value</span></span> | <span data-ttu-id="04925-333">说明</span><span class="sxs-lookup"><span data-stu-id="04925-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="04925-334">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="04925-335">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-336">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-337">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="04925-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="04925-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="04925-339">空</span><span class="sxs-lookup"><span data-stu-id="04925-339">Empty</span></span> | <span data-ttu-id="04925-340">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="04925-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="04925-341">在 .NET 客户端中，可以通过提供给 `WithUrl`的 options 委托来修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="04925-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="04925-342">在 JavaScript 客户端中，可以在提供给 `withUrl`的 JavaScript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="04925-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="04925-343">在 Java 客户端中，可以通过从 `HubConnectionBuilder.create("HUB URL")` 返回的 `HttpHubConnectionBuilder` 中的方法来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="04925-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="04925-344">其他资源</span><span class="sxs-lookup"><span data-stu-id="04925-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="04925-345">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="04925-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="04925-346">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="04925-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="04925-347">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="04925-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="04925-348">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该方法可在 `Startup.ConfigureServices` 方法中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="04925-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="04925-349">`AddJsonProtocol` 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="04925-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="04925-350">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于配置自变量和返回值的序列化 `JsonSerializerSettings` 对象的 JSON.NET。</span><span class="sxs-lookup"><span data-stu-id="04925-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="04925-351">有关详细信息，请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="04925-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="04925-352">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在 `Startup.ConfigureServices`中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="04925-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="04925-353">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="04925-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="04925-354">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="04925-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="04925-355">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="04925-356">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="04925-356">MessagePack serialization options</span></span>

<span data-ttu-id="04925-357">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="04925-358">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="04925-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="04925-359">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="04925-360">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="04925-360">Configure server options</span></span>

<span data-ttu-id="04925-361">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="04925-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="04925-362">选项</span><span class="sxs-lookup"><span data-stu-id="04925-362">Option</span></span> | <span data-ttu-id="04925-363">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-363">Default Value</span></span> | <span data-ttu-id="04925-364">说明</span><span class="sxs-lookup"><span data-stu-id="04925-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="04925-365">30 秒</span><span class="sxs-lookup"><span data-stu-id="04925-365">30 seconds</span></span> | <span data-ttu-id="04925-366">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="04925-367">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="04925-368">建议值为 `KeepAliveInterval` 值的两倍。</span><span class="sxs-lookup"><span data-stu-id="04925-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="04925-369">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-369">15 seconds</span></span> | <span data-ttu-id="04925-370">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="04925-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="04925-371">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-372">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="04925-373">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-373">15 seconds</span></span> | <span data-ttu-id="04925-374">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="04925-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="04925-375">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="04925-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="04925-376">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="04925-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="04925-377">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="04925-377">All installed protocols</span></span> | <span data-ttu-id="04925-378">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="04925-378">Protocols supported by this hub.</span></span> <span data-ttu-id="04925-379">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="04925-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="04925-380">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="04925-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="04925-381">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="04925-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="04925-382">可以通过向 `Startup.ConfigureServices`中的 `AddSignalR` 调用提供选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="04925-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="04925-383">单个集线器的选项将覆盖 `AddSignalR` 中提供的全局选项，并且可以使用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="04925-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="04925-384">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="04925-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="04925-385">使用 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="04925-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="04925-386">可以通过将委托传递到 `Startup.Configure`中的[MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="04925-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="04925-387">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="04925-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="04925-388">选项</span><span class="sxs-lookup"><span data-stu-id="04925-388">Option</span></span> | <span data-ttu-id="04925-389">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-389">Default Value</span></span> | <span data-ttu-id="04925-390">说明</span><span class="sxs-lookup"><span data-stu-id="04925-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="04925-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="04925-391">32 KB</span></span> | <span data-ttu-id="04925-392">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="04925-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="04925-393">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="04925-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="04925-394">从应用于 Hub 类的 `Authorize` 属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="04925-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="04925-395">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="04925-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="04925-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="04925-396">32 KB</span></span> | <span data-ttu-id="04925-397">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="04925-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="04925-398">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="04925-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="04925-399">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="04925-399">All Transports are enabled.</span></span> | <span data-ttu-id="04925-400">一个位标志 `HttpTransportType` 值的枚举，这些值可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="04925-401">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="04925-401">See below.</span></span> | <span data-ttu-id="04925-402">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="04925-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="04925-403">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="04925-403">See below.</span></span> | <span data-ttu-id="04925-404">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="04925-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="04925-405">长轮询传输具有可使用 `LongPolling` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="04925-406">选项</span><span class="sxs-lookup"><span data-stu-id="04925-406">Option</span></span> | <span data-ttu-id="04925-407">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-407">Default Value</span></span> | <span data-ttu-id="04925-408">说明</span><span class="sxs-lookup"><span data-stu-id="04925-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="04925-409">90秒</span><span class="sxs-lookup"><span data-stu-id="04925-409">90 seconds</span></span> | <span data-ttu-id="04925-410">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="04925-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="04925-411">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="04925-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="04925-412">WebSocket 传输具有可使用 `WebSockets` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="04925-413">选项</span><span class="sxs-lookup"><span data-stu-id="04925-413">Option</span></span> | <span data-ttu-id="04925-414">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-414">Default Value</span></span> | <span data-ttu-id="04925-415">说明</span><span class="sxs-lookup"><span data-stu-id="04925-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="04925-416">5 秒</span><span class="sxs-lookup"><span data-stu-id="04925-416">5 seconds</span></span> | <span data-ttu-id="04925-417">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="04925-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="04925-418">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="04925-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="04925-419">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="04925-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="04925-420">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="04925-420">Configure client options</span></span>

<span data-ttu-id="04925-421">可以在 `HubConnectionBuilder` 类型上配置客户端选项（在 .NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="04925-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="04925-422">Java 客户端中也提供了此功能，但 `HttpHubConnectionBuilder` 子类包含生成器配置选项，以及 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="04925-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="04925-423">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="04925-423">Configure logging</span></span>

<span data-ttu-id="04925-424">使用 `ConfigureLogging` 方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="04925-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="04925-425">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="04925-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="04925-426">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="04925-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="04925-427">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="04925-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="04925-428">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="04925-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="04925-429">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="04925-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="04925-430">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="04925-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="04925-431">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="04925-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="04925-432">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="04925-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="04925-433">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="04925-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="04925-434">若要完全禁用日志记录，请在 `configureLogging` 方法中指定 `signalR.LogLevel.None`。</span><span class="sxs-lookup"><span data-stu-id="04925-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="04925-435">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="04925-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="04925-436">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="04925-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="04925-437">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="04925-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="04925-438">以下代码片段演示了如何在 SignalR Java 客户端中使用 `java.util.logging`。</span><span class="sxs-lookup"><span data-stu-id="04925-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="04925-439">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="04925-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="04925-440">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="04925-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="04925-441">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="04925-441">Configure allowed transports</span></span>

<span data-ttu-id="04925-442">可以在 `WithUrl` 调用（JavaScript 中的`withUrl`）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="04925-443">`HttpTransportType` 的值的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="04925-444">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="04925-444">All transports are enabled by default.</span></span>

<span data-ttu-id="04925-445">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="04925-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="04925-446">在 JavaScript 客户端中，通过在提供给 `withUrl`的 options 对象上设置 `transport` 字段来配置传输：</span><span class="sxs-lookup"><span data-stu-id="04925-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="04925-447">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="04925-448">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="04925-448">Configure bearer authentication</span></span>

<span data-ttu-id="04925-449">若要随 SignalR 请求一起提供身份验证数据，请使用 `AccessTokenProvider` 选项（JavaScript 中的`accessTokenFactory`）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="04925-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="04925-450">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Bearer`类型的 `Authorization` 标头）。</span><span class="sxs-lookup"><span data-stu-id="04925-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="04925-451">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="04925-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="04925-452">在这些情况下，访问令牌作为查询字符串值提供 `access_token`。</span><span class="sxs-lookup"><span data-stu-id="04925-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="04925-453">在 .NET 客户端中，可以使用 `WithUrl`中的 options 委托指定 `AccessTokenProvider` 选项：</span><span class="sxs-lookup"><span data-stu-id="04925-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="04925-454">在 JavaScript 客户端中，通过在 `withUrl`中的 options 对象上设置 `accessTokenFactory` 字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="04925-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="04925-455">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="04925-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="04925-456">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一\<字符串 >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="04925-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="04925-457">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="04925-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="04925-458">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="04925-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="04925-459">用于配置超时和保持活动状态的其他选项在 `HubConnection` 对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="04925-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="04925-460">.NET</span><span class="sxs-lookup"><span data-stu-id="04925-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="04925-461">选项</span><span class="sxs-lookup"><span data-stu-id="04925-461">Option</span></span> | <span data-ttu-id="04925-462">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-462">Default value</span></span> | <span data-ttu-id="04925-463">说明</span><span class="sxs-lookup"><span data-stu-id="04925-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="04925-464">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-465">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-465">Timeout for server activity.</span></span> <span data-ttu-id="04925-466">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="04925-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="04925-467">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-468">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="04925-469">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-469">15 seconds</span></span> | <span data-ttu-id="04925-470">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="04925-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="04925-471">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="04925-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="04925-472">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-473">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="04925-474">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-474">15 seconds</span></span> | <span data-ttu-id="04925-475">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="04925-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="04925-476">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="04925-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="04925-477">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="04925-478">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="04925-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="04925-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="04925-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="04925-480">选项</span><span class="sxs-lookup"><span data-stu-id="04925-480">Option</span></span> | <span data-ttu-id="04925-481">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-481">Default value</span></span> | <span data-ttu-id="04925-482">说明</span><span class="sxs-lookup"><span data-stu-id="04925-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="04925-483">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-484">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-484">Timeout for server activity.</span></span> <span data-ttu-id="04925-485">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="04925-486">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-487">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="04925-488">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="04925-489">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="04925-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="04925-490">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="04925-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="04925-491">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="04925-492">Java</span><span class="sxs-lookup"><span data-stu-id="04925-492">Java</span></span>](#tab/java)

| <span data-ttu-id="04925-493">选项</span><span class="sxs-lookup"><span data-stu-id="04925-493">Option</span></span> | <span data-ttu-id="04925-494">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-494">Default value</span></span> | <span data-ttu-id="04925-495">说明</span><span class="sxs-lookup"><span data-stu-id="04925-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="04925-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="04925-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="04925-497">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-498">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-498">Timeout for server activity.</span></span> <span data-ttu-id="04925-499">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="04925-500">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-501">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="04925-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-502">15 seconds</span></span> | <span data-ttu-id="04925-503">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="04925-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="04925-504">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="04925-505">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-506">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="04925-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="04925-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="04925-508">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="04925-509">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="04925-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="04925-510">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="04925-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="04925-511">如果客户端未在服务器上设置的 `ClientTimeoutInterval` 中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="04925-512">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="04925-512">Configure additional options</span></span>

<span data-ttu-id="04925-513">可以在 `HubConnectionBuilder` 上的 `WithUrl` （JavaScript 中的`withUrl`）方法中配置其他选项，或在 Java 客户端中 `HttpHubConnectionBuilder` 上的各种配置 Api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="04925-514">.NET</span><span class="sxs-lookup"><span data-stu-id="04925-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="04925-515">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="04925-515">.NET Option</span></span> |  <span data-ttu-id="04925-516">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-516">Default value</span></span> | <span data-ttu-id="04925-517">说明</span><span class="sxs-lookup"><span data-stu-id="04925-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="04925-518">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="04925-519">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-520">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-521">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="04925-522">空</span><span class="sxs-lookup"><span data-stu-id="04925-522">Empty</span></span> | <span data-ttu-id="04925-523">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="04925-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="04925-524">空</span><span class="sxs-lookup"><span data-stu-id="04925-524">Empty</span></span> | <span data-ttu-id="04925-525">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="04925-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="04925-526">空</span><span class="sxs-lookup"><span data-stu-id="04925-526">Empty</span></span> | <span data-ttu-id="04925-527">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="04925-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="04925-528">5 秒</span><span class="sxs-lookup"><span data-stu-id="04925-528">5 seconds</span></span> | <span data-ttu-id="04925-529">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="04925-529">WebSockets only.</span></span> <span data-ttu-id="04925-530">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="04925-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="04925-531">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="04925-532">空</span><span class="sxs-lookup"><span data-stu-id="04925-532">Empty</span></span> | <span data-ttu-id="04925-533">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="04925-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="04925-534">一个委托，可用于配置或替换用于发送 HTTP 请求的 `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="04925-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="04925-535">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="04925-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="04925-536">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="04925-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="04925-537">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="04925-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="04925-538">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="04925-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="04925-539">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="04925-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="04925-540">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="04925-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="04925-541">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="04925-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="04925-542">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="04925-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="04925-543">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="04925-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="04925-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="04925-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="04925-545">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="04925-545">JavaScript Option</span></span> | <span data-ttu-id="04925-546">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-546">Default Value</span></span> | <span data-ttu-id="04925-547">说明</span><span class="sxs-lookup"><span data-stu-id="04925-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="04925-548">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="04925-549">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-550">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-551">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="04925-552">Java</span><span class="sxs-lookup"><span data-stu-id="04925-552">Java</span></span>](#tab/java)

| <span data-ttu-id="04925-553">Java 选项</span><span class="sxs-lookup"><span data-stu-id="04925-553">Java Option</span></span> | <span data-ttu-id="04925-554">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-554">Default Value</span></span> | <span data-ttu-id="04925-555">说明</span><span class="sxs-lookup"><span data-stu-id="04925-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="04925-556">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="04925-557">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-558">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-559">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="04925-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="04925-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="04925-561">空</span><span class="sxs-lookup"><span data-stu-id="04925-561">Empty</span></span> | <span data-ttu-id="04925-562">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="04925-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="04925-563">在 .NET 客户端中，可以通过提供给 `WithUrl`的 options 委托来修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="04925-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="04925-564">在 JavaScript 客户端中，可以在提供给 `withUrl`的 JavaScript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="04925-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="04925-565">在 Java 客户端中，可以通过从 `HubConnectionBuilder.create("HUB URL")` 返回的 `HttpHubConnectionBuilder` 中的方法来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="04925-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="04925-566">其他资源</span><span class="sxs-lookup"><span data-stu-id="04925-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="04925-567">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="04925-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="04925-568">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="04925-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="04925-569">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="04925-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="04925-570">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该方法可在 `Startup.ConfigureServices` 方法中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="04925-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="04925-571">`AddJsonProtocol` 方法采用接收 `options` 对象的委托。</span><span class="sxs-lookup"><span data-stu-id="04925-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="04925-572">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于配置自变量和返回值的序列化 `JsonSerializerSettings` 对象的 JSON.NET。</span><span class="sxs-lookup"><span data-stu-id="04925-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="04925-573">有关详细信息，请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="04925-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="04925-574">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在 `Startup.ConfigureServices`中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="04925-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="04925-575">在 .NET 客户端中， [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的 `AddJsonProtocol` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="04925-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="04925-576">必须导入 `Microsoft.Extensions.DependencyInjection` 命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="04925-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="04925-577">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="04925-578">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="04925-578">MessagePack serialization options</span></span>

<span data-ttu-id="04925-579">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="04925-580">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="04925-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="04925-581">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="04925-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="04925-582">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="04925-582">Configure server options</span></span>

<span data-ttu-id="04925-583">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="04925-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="04925-584">选项</span><span class="sxs-lookup"><span data-stu-id="04925-584">Option</span></span> | <span data-ttu-id="04925-585">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-585">Default Value</span></span> | <span data-ttu-id="04925-586">说明</span><span class="sxs-lookup"><span data-stu-id="04925-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="04925-587">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-587">15 seconds</span></span> | <span data-ttu-id="04925-588">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="04925-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="04925-589">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-590">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="04925-591">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-591">15 seconds</span></span> | <span data-ttu-id="04925-592">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="04925-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="04925-593">更改 `KeepAliveInterval`时，请更改客户端上的 `ServerTimeout`/`serverTimeoutInMilliseconds` 设置。</span><span class="sxs-lookup"><span data-stu-id="04925-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="04925-594">建议的 `ServerTimeout`/`serverTimeoutInMilliseconds` 值为 `KeepAliveInterval` 值的双精度值。</span><span class="sxs-lookup"><span data-stu-id="04925-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="04925-595">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="04925-595">All installed protocols</span></span> | <span data-ttu-id="04925-596">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="04925-596">Protocols supported by this hub.</span></span> <span data-ttu-id="04925-597">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="04925-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="04925-598">如果 `true`，则在 Hub 方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="04925-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="04925-599">默认值为 `false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="04925-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="04925-600">可以通过向 `Startup.ConfigureServices`中的 `AddSignalR` 调用提供选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="04925-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="04925-601">单个集线器的选项将覆盖 `AddSignalR` 中提供的全局选项，并且可以使用 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="04925-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="04925-602">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="04925-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="04925-603">使用 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="04925-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="04925-604">可以通过将委托传递到 `Startup.Configure`中的[MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="04925-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="04925-605">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="04925-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="04925-606">选项</span><span class="sxs-lookup"><span data-stu-id="04925-606">Option</span></span> | <span data-ttu-id="04925-607">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-607">Default Value</span></span> | <span data-ttu-id="04925-608">说明</span><span class="sxs-lookup"><span data-stu-id="04925-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="04925-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="04925-609">32 KB</span></span> | <span data-ttu-id="04925-610">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="04925-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="04925-611">增大此值可使服务器接收更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="04925-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="04925-612">从应用于 Hub 类的 `Authorize` 属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="04925-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="04925-613">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="04925-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="04925-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="04925-614">32 KB</span></span> | <span data-ttu-id="04925-615">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="04925-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="04925-616">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="04925-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="04925-617">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="04925-617">All Transports are enabled.</span></span> | <span data-ttu-id="04925-618">一个位标志 `HttpTransportType` 值的枚举，这些值可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="04925-619">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="04925-619">See below.</span></span> | <span data-ttu-id="04925-620">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="04925-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="04925-621">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="04925-621">See below.</span></span> | <span data-ttu-id="04925-622">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="04925-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="04925-623">长轮询传输具有可使用 `LongPolling` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="04925-624">选项</span><span class="sxs-lookup"><span data-stu-id="04925-624">Option</span></span> | <span data-ttu-id="04925-625">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-625">Default Value</span></span> | <span data-ttu-id="04925-626">说明</span><span class="sxs-lookup"><span data-stu-id="04925-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="04925-627">90秒</span><span class="sxs-lookup"><span data-stu-id="04925-627">90 seconds</span></span> | <span data-ttu-id="04925-628">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="04925-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="04925-629">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="04925-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="04925-630">WebSocket 传输具有可使用 `WebSockets` 属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="04925-631">选项</span><span class="sxs-lookup"><span data-stu-id="04925-631">Option</span></span> | <span data-ttu-id="04925-632">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-632">Default Value</span></span> | <span data-ttu-id="04925-633">说明</span><span class="sxs-lookup"><span data-stu-id="04925-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="04925-634">5 秒</span><span class="sxs-lookup"><span data-stu-id="04925-634">5 seconds</span></span> | <span data-ttu-id="04925-635">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="04925-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="04925-636">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="04925-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="04925-637">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="04925-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="04925-638">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="04925-638">Configure client options</span></span>

<span data-ttu-id="04925-639">可以在 `HubConnectionBuilder` 类型上配置客户端选项（在 .NET 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="04925-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="04925-640">Java 客户端中也提供了此功能，但 `HttpHubConnectionBuilder` 子类包含生成器配置选项，以及 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="04925-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="04925-641">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="04925-641">Configure logging</span></span>

<span data-ttu-id="04925-642">使用 `ConfigureLogging` 方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="04925-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="04925-643">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="04925-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="04925-644">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="04925-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="04925-645">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="04925-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="04925-646">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="04925-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="04925-647">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="04925-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="04925-648">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="04925-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="04925-649">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="04925-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="04925-650">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="04925-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="04925-651">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="04925-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="04925-652">若要完全禁用日志记录，请在 `configureLogging` 方法中指定 `signalR.LogLevel.None`。</span><span class="sxs-lookup"><span data-stu-id="04925-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="04925-653">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="04925-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="04925-654">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="04925-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="04925-655">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="04925-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="04925-656">以下代码片段演示了如何在 SignalR Java 客户端中使用 `java.util.logging`。</span><span class="sxs-lookup"><span data-stu-id="04925-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="04925-657">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="04925-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="04925-658">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="04925-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="04925-659">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="04925-659">Configure allowed transports</span></span>

<span data-ttu-id="04925-660">可以在 `WithUrl` 调用（JavaScript 中的`withUrl`）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="04925-661">`HttpTransportType` 的值的按位 "或" 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="04925-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="04925-662">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="04925-662">All transports are enabled by default.</span></span>

<span data-ttu-id="04925-663">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="04925-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="04925-664">在 JavaScript 客户端中，通过在提供给 `withUrl`的 options 对象上设置 `transport` 字段来配置传输：</span><span class="sxs-lookup"><span data-stu-id="04925-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="04925-665">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="04925-665">Configure bearer authentication</span></span>

<span data-ttu-id="04925-666">若要随 SignalR 请求一起提供身份验证数据，请使用 `AccessTokenProvider` 选项（JavaScript 中的`accessTokenFactory`）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="04925-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="04925-667">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Bearer`类型的 `Authorization` 标头）。</span><span class="sxs-lookup"><span data-stu-id="04925-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="04925-668">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="04925-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="04925-669">在这些情况下，访问令牌作为查询字符串值提供 `access_token`。</span><span class="sxs-lookup"><span data-stu-id="04925-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="04925-670">在 .NET 客户端中，可以使用 `WithUrl`中的 options 委托指定 `AccessTokenProvider` 选项：</span><span class="sxs-lookup"><span data-stu-id="04925-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="04925-671">在 JavaScript 客户端中，通过在 `withUrl`中的 options 对象上设置 `accessTokenFactory` 字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="04925-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="04925-672">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="04925-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="04925-673">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一\<字符串 >](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="04925-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="04925-674">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="04925-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="04925-675">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="04925-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="04925-676">用于配置超时和保持活动状态的其他选项在 `HubConnection` 对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="04925-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="04925-677">.NET</span><span class="sxs-lookup"><span data-stu-id="04925-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="04925-678">选项</span><span class="sxs-lookup"><span data-stu-id="04925-678">Option</span></span> | <span data-ttu-id="04925-679">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-679">Default value</span></span> | <span data-ttu-id="04925-680">说明</span><span class="sxs-lookup"><span data-stu-id="04925-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="04925-681">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-682">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-682">Timeout for server activity.</span></span> <span data-ttu-id="04925-683">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="04925-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="04925-684">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-685">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="04925-686">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-686">15 seconds</span></span> | <span data-ttu-id="04925-687">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="04925-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="04925-688">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（JavaScript 中的`onclose`）。</span><span class="sxs-lookup"><span data-stu-id="04925-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="04925-689">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-690">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="04925-691">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="04925-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="04925-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="04925-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="04925-693">选项</span><span class="sxs-lookup"><span data-stu-id="04925-693">Option</span></span> | <span data-ttu-id="04925-694">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-694">Default value</span></span> | <span data-ttu-id="04925-695">说明</span><span class="sxs-lookup"><span data-stu-id="04925-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="04925-696">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-697">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-697">Timeout for server activity.</span></span> <span data-ttu-id="04925-698">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="04925-699">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-700">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="04925-701">Java</span><span class="sxs-lookup"><span data-stu-id="04925-701">Java</span></span>](#tab/java)

| <span data-ttu-id="04925-702">选项</span><span class="sxs-lookup"><span data-stu-id="04925-702">Option</span></span> | <span data-ttu-id="04925-703">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-703">Default value</span></span> | <span data-ttu-id="04925-704">说明</span><span class="sxs-lookup"><span data-stu-id="04925-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="04925-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="04925-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="04925-706">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="04925-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="04925-707">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="04925-707">Timeout for server activity.</span></span> <span data-ttu-id="04925-708">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="04925-709">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="04925-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="04925-710">建议值至少为服务器的 `KeepAliveInterval` 值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="04925-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="04925-711">15 秒</span><span class="sxs-lookup"><span data-stu-id="04925-711">15 seconds</span></span> | <span data-ttu-id="04925-712">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="04925-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="04925-713">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="04925-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="04925-714">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="04925-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="04925-715">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="04925-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="04925-716">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="04925-716">Configure additional options</span></span>

<span data-ttu-id="04925-717">可以在 `HubConnectionBuilder` 上的 `WithUrl` （JavaScript 中的`withUrl`）方法中配置其他选项，或在 Java 客户端中 `HttpHubConnectionBuilder` 上的各种配置 Api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="04925-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="04925-718">.NET</span><span class="sxs-lookup"><span data-stu-id="04925-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="04925-719">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="04925-719">.NET Option</span></span> |  <span data-ttu-id="04925-720">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-720">Default value</span></span> | <span data-ttu-id="04925-721">说明</span><span class="sxs-lookup"><span data-stu-id="04925-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="04925-722">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="04925-723">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-724">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-725">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="04925-726">空</span><span class="sxs-lookup"><span data-stu-id="04925-726">Empty</span></span> | <span data-ttu-id="04925-727">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="04925-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="04925-728">空</span><span class="sxs-lookup"><span data-stu-id="04925-728">Empty</span></span> | <span data-ttu-id="04925-729">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="04925-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="04925-730">空</span><span class="sxs-lookup"><span data-stu-id="04925-730">Empty</span></span> | <span data-ttu-id="04925-731">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="04925-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="04925-732">5 秒</span><span class="sxs-lookup"><span data-stu-id="04925-732">5 seconds</span></span> | <span data-ttu-id="04925-733">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="04925-733">WebSockets only.</span></span> <span data-ttu-id="04925-734">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="04925-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="04925-735">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="04925-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="04925-736">空</span><span class="sxs-lookup"><span data-stu-id="04925-736">Empty</span></span> | <span data-ttu-id="04925-737">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="04925-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="04925-738">一个委托，可用于配置或替换用于发送 HTTP 请求的 `HttpMessageHandler`。</span><span class="sxs-lookup"><span data-stu-id="04925-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="04925-739">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="04925-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="04925-740">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="04925-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="04925-741">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="04925-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="04925-742">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="04925-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="04925-743">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="04925-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="04925-744">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="04925-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="04925-745">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="04925-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="04925-746">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="04925-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="04925-747">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="04925-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="04925-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="04925-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="04925-749">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="04925-749">JavaScript Option</span></span> | <span data-ttu-id="04925-750">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-750">Default Value</span></span> | <span data-ttu-id="04925-751">说明</span><span class="sxs-lookup"><span data-stu-id="04925-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="04925-752">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="04925-753">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-754">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-755">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="04925-756">Java</span><span class="sxs-lookup"><span data-stu-id="04925-756">Java</span></span>](#tab/java)

| <span data-ttu-id="04925-757">Java 选项</span><span class="sxs-lookup"><span data-stu-id="04925-757">Java Option</span></span> | <span data-ttu-id="04925-758">默认值</span><span class="sxs-lookup"><span data-stu-id="04925-758">Default Value</span></span> | <span data-ttu-id="04925-759">说明</span><span class="sxs-lookup"><span data-stu-id="04925-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="04925-760">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="04925-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="04925-761">将此设置为 "`true`" 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="04925-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="04925-762">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="04925-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="04925-763">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="04925-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="04925-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="04925-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="04925-765">空</span><span class="sxs-lookup"><span data-stu-id="04925-765">Empty</span></span> | <span data-ttu-id="04925-766">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="04925-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="04925-767">在 .NET 客户端中，可以通过提供给 `WithUrl`的 options 委托来修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="04925-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="04925-768">在 JavaScript 客户端中，可以在提供给 `withUrl`的 JavaScript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="04925-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="04925-769">在 Java 客户端中，可以通过从 `HubConnectionBuilder.create("HUB URL")` 返回的 `HttpHubConnectionBuilder` 中的方法来配置这些选项。</span><span class="sxs-lookup"><span data-stu-id="04925-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="04925-770">其他资源</span><span class="sxs-lookup"><span data-stu-id="04925-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end