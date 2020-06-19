---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 809bdc777b6307314a7bcde82ab5e0c6888db99b
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074473"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="cf329-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="cf329-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cf329-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cf329-105">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cf329-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cf329-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cf329-108">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cf329-109">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cf329-110">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cf329-111">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="cf329-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cf329-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="cf329-113">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cf329-114">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cf329-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cf329-116">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="cf329-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cf329-117">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="cf329-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cf329-118">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-118">MessagePack serialization options</span></span>

<span data-ttu-id="cf329-119">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cf329-120">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="cf329-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-121">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cf329-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="cf329-122">Configure server options</span></span>

<span data-ttu-id="cf329-123">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="cf329-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cf329-124">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-124">Option</span></span> | <span data-ttu-id="cf329-125">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-125">Default Value</span></span> | <span data-ttu-id="cf329-126">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cf329-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-127">30 seconds</span></span> | <span data-ttu-id="cf329-128">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cf329-129">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cf329-130">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cf329-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-131">15 seconds</span></span> | <span data-ttu-id="cf329-132">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="cf329-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cf329-133">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-134">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-135">15 seconds</span></span> | <span data-ttu-id="cf329-136">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="cf329-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cf329-137">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cf329-138">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cf329-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="cf329-139">All installed protocols</span></span> | <span data-ttu-id="cf329-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-140">Protocols supported by this hub.</span></span> <span data-ttu-id="cf329-141">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cf329-142">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cf329-143">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="cf329-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cf329-144">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="cf329-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cf329-145">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="cf329-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cf329-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-146">32 KB</span></span> | <span data-ttu-id="cf329-147">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="cf329-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cf329-148">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cf329-149">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="cf329-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cf329-150">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="cf329-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cf329-151">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cf329-152">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="cf329-153">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cf329-154">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-154">Option</span></span> | <span data-ttu-id="cf329-155">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-155">Default Value</span></span> | <span data-ttu-id="cf329-156">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cf329-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-157">32 KB</span></span> | <span data-ttu-id="cf329-158">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cf329-159">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="cf329-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cf329-160">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cf329-161">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="cf329-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cf329-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-162">32 KB</span></span> | <span data-ttu-id="cf329-163">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cf329-164">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="cf329-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cf329-165">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="cf329-165">All Transports are enabled.</span></span> | <span data-ttu-id="cf329-166">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cf329-167">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-167">See below.</span></span> | <span data-ttu-id="cf329-168">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cf329-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-169">See below.</span></span> | <span data-ttu-id="cf329-170">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="cf329-171">0</span><span class="sxs-lookup"><span data-stu-id="cf329-171">0</span></span> | <span data-ttu-id="cf329-172">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="cf329-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="cf329-173">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="cf329-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="cf329-174">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cf329-175">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-175">Option</span></span> | <span data-ttu-id="cf329-176">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-176">Default Value</span></span> | <span data-ttu-id="cf329-177">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cf329-178">90秒</span><span class="sxs-lookup"><span data-stu-id="cf329-178">90 seconds</span></span> | <span data-ttu-id="cf329-179">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cf329-180">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="cf329-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cf329-181">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cf329-182">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-182">Option</span></span> | <span data-ttu-id="cf329-183">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-183">Default Value</span></span> | <span data-ttu-id="cf329-184">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cf329-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-185">5 seconds</span></span> | <span data-ttu-id="cf329-186">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="cf329-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cf329-187">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="cf329-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cf329-188">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="cf329-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cf329-189">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="cf329-189">Configure client options</span></span>

<span data-ttu-id="cf329-190">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="cf329-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cf329-191">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="cf329-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cf329-192">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="cf329-192">Configure logging</span></span>

<span data-ttu-id="cf329-193">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cf329-194">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="cf329-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cf329-195">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="cf329-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-196">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="cf329-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cf329-197">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="cf329-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cf329-198">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cf329-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cf329-199">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cf329-200">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cf329-201">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="cf329-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cf329-202">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="cf329-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cf329-203">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="cf329-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cf329-204">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cf329-205">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="cf329-205">The following table lists the available log levels.</span></span> <span data-ttu-id="cf329-206">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="cf329-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cf329-207">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="cf329-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cf329-208">字符串</span><span class="sxs-lookup"><span data-stu-id="cf329-208">String</span></span>                      | <span data-ttu-id="cf329-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cf329-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cf329-210">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="cf329-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cf329-211">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="cf329-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cf329-212">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cf329-213">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="cf329-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cf329-214">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="cf329-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cf329-215">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="cf329-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cf329-216">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cf329-217">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="cf329-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cf329-218">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="cf329-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cf329-219">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="cf329-219">Configure allowed transports</span></span>

<span data-ttu-id="cf329-220">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="cf329-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cf329-221">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cf329-222">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-222">All transports are enabled by default.</span></span>

<span data-ttu-id="cf329-223">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf329-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cf329-224">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cf329-225">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cf329-226">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cf329-227">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cf329-228">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="cf329-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cf329-229">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="cf329-229">Configure bearer authentication</span></span>

<span data-ttu-id="cf329-230">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="cf329-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cf329-231">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="cf329-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cf329-232">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cf329-233">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cf329-234">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cf329-235">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cf329-236">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cf329-237">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cf329-238">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cf329-239">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cf329-240">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="cf329-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-241">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-242">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-242">Option</span></span> | <span data-ttu-id="cf329-243">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-243">Default value</span></span> | <span data-ttu-id="cf329-244">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cf329-245">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-246">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-246">Timeout for server activity.</span></span> <span data-ttu-id="cf329-247">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-248">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-249">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cf329-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-250">15 seconds</span></span> | <span data-ttu-id="cf329-251">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-252">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-253">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-254">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-255">15 seconds</span></span> | <span data-ttu-id="cf329-256">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-257">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-258">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cf329-259">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="cf329-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cf329-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-261">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-261">Option</span></span> | <span data-ttu-id="cf329-262">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-262">Default value</span></span> | <span data-ttu-id="cf329-263">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cf329-264">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-265">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-265">Timeout for server activity.</span></span> <span data-ttu-id="cf329-266">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cf329-267">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-268">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cf329-269">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cf329-270">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-271">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-272">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-273">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-273">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-274">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-274">Option</span></span> | <span data-ttu-id="cf329-275">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-275">Default value</span></span> | <span data-ttu-id="cf329-276">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cf329-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cf329-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cf329-278">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-279">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-279">Timeout for server activity.</span></span> <span data-ttu-id="cf329-280">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-281">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-282">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cf329-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-283">15 seconds</span></span> | <span data-ttu-id="cf329-284">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-285">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-286">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-287">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cf329-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cf329-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cf329-289">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cf329-290">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-291">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-292">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cf329-293">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="cf329-293">Configure additional options</span></span>

<span data-ttu-id="cf329-294">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-295">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-296">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-296">.NET Option</span></span> |  <span data-ttu-id="cf329-297">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-297">Default value</span></span> | <span data-ttu-id="cf329-298">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cf329-299">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cf329-300">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-301">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-302">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cf329-303">空</span><span class="sxs-lookup"><span data-stu-id="cf329-303">Empty</span></span> | <span data-ttu-id="cf329-304">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cf329-305">空</span><span class="sxs-lookup"><span data-stu-id="cf329-305">Empty</span></span> | <span data-ttu-id="cf329-306">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cf329-307">空</span><span class="sxs-lookup"><span data-stu-id="cf329-307">Empty</span></span> | <span data-ttu-id="cf329-308">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cf329-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-309">5 seconds</span></span> | <span data-ttu-id="cf329-310">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="cf329-310">WebSockets only.</span></span> <span data-ttu-id="cf329-311">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cf329-312">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cf329-313">空</span><span class="sxs-lookup"><span data-stu-id="cf329-313">Empty</span></span> | <span data-ttu-id="cf329-314">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cf329-315">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="cf329-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cf329-316">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="cf329-317">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="cf329-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cf329-318">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cf329-319">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="cf329-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cf329-320">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="cf329-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cf329-321">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cf329-322">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="cf329-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cf329-323">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="cf329-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cf329-324">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cf329-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-326">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-326">JavaScript Option</span></span> | <span data-ttu-id="cf329-327">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-327">Default Value</span></span> | <span data-ttu-id="cf329-328">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cf329-329">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="cf329-330">每个 HTTP 请求发送的标头的字典。</span><span class="sxs-lookup"><span data-stu-id="cf329-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="cf329-331">在浏览器中发送标头对于 Websocket 或流不起作用 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。</span><span class="sxs-lookup"><span data-stu-id="cf329-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cf329-332">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="cf329-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cf329-333">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-334">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-335">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="cf329-336">指定是否将凭据与 CORS 请求一起发送。</span><span class="sxs-lookup"><span data-stu-id="cf329-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="cf329-337">Azure App Service 使用 cookie 进行粘滞会话，并且需要此选项才能正常工作。</span><span class="sxs-lookup"><span data-stu-id="cf329-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="cf329-338">有关 CORS 的详细信息 SignalR ，请参阅 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="cf329-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-339">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-339">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-340">Java 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-340">Java Option</span></span> | <span data-ttu-id="cf329-341">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-341">Default Value</span></span> | <span data-ttu-id="cf329-342">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cf329-343">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cf329-344">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-345">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-346">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cf329-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cf329-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cf329-348">空</span><span class="sxs-lookup"><span data-stu-id="cf329-348">Empty</span></span> | <span data-ttu-id="cf329-349">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cf329-350">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cf329-351">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cf329-352">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cf329-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cf329-353">其他资源</span><span class="sxs-lookup"><span data-stu-id="cf329-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cf329-354">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cf329-355">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cf329-356">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cf329-357">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cf329-358">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cf329-359">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cf329-360">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cf329-361">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="cf329-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cf329-362">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="cf329-363">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cf329-364">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cf329-365">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cf329-366">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="cf329-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cf329-367">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="cf329-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cf329-368">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-368">MessagePack serialization options</span></span>

<span data-ttu-id="cf329-369">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cf329-370">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="cf329-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-371">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cf329-372">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="cf329-372">Configure server options</span></span>

<span data-ttu-id="cf329-373">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="cf329-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cf329-374">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-374">Option</span></span> | <span data-ttu-id="cf329-375">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-375">Default Value</span></span> | <span data-ttu-id="cf329-376">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cf329-377">30 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-377">30 seconds</span></span> | <span data-ttu-id="cf329-378">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cf329-379">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cf329-380">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cf329-381">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-381">15 seconds</span></span> | <span data-ttu-id="cf329-382">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="cf329-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cf329-383">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-384">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-385">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-385">15 seconds</span></span> | <span data-ttu-id="cf329-386">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="cf329-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cf329-387">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cf329-388">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cf329-389">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="cf329-389">All installed protocols</span></span> | <span data-ttu-id="cf329-390">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-390">Protocols supported by this hub.</span></span> <span data-ttu-id="cf329-391">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cf329-392">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cf329-393">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="cf329-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cf329-394">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="cf329-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cf329-395">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="cf329-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cf329-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-396">32 KB</span></span> | <span data-ttu-id="cf329-397">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="cf329-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cf329-398">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cf329-399">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="cf329-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cf329-400">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="cf329-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cf329-401">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cf329-402">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="cf329-403">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cf329-404">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-404">Option</span></span> | <span data-ttu-id="cf329-405">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-405">Default Value</span></span> | <span data-ttu-id="cf329-406">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cf329-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-407">32 KB</span></span> | <span data-ttu-id="cf329-408">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cf329-409">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="cf329-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cf329-410">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cf329-411">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="cf329-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cf329-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-412">32 KB</span></span> | <span data-ttu-id="cf329-413">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cf329-414">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="cf329-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cf329-415">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="cf329-415">All Transports are enabled.</span></span> | <span data-ttu-id="cf329-416">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cf329-417">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-417">See below.</span></span> | <span data-ttu-id="cf329-418">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cf329-419">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-419">See below.</span></span> | <span data-ttu-id="cf329-420">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="cf329-421">0</span><span class="sxs-lookup"><span data-stu-id="cf329-421">0</span></span> | <span data-ttu-id="cf329-422">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="cf329-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="cf329-423">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="cf329-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="cf329-424">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cf329-425">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-425">Option</span></span> | <span data-ttu-id="cf329-426">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-426">Default Value</span></span> | <span data-ttu-id="cf329-427">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cf329-428">90秒</span><span class="sxs-lookup"><span data-stu-id="cf329-428">90 seconds</span></span> | <span data-ttu-id="cf329-429">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cf329-430">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="cf329-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cf329-431">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cf329-432">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-432">Option</span></span> | <span data-ttu-id="cf329-433">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-433">Default Value</span></span> | <span data-ttu-id="cf329-434">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cf329-435">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-435">5 seconds</span></span> | <span data-ttu-id="cf329-436">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="cf329-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cf329-437">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="cf329-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cf329-438">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="cf329-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cf329-439">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="cf329-439">Configure client options</span></span>

<span data-ttu-id="cf329-440">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="cf329-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cf329-441">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="cf329-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cf329-442">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="cf329-442">Configure logging</span></span>

<span data-ttu-id="cf329-443">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cf329-444">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="cf329-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cf329-445">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="cf329-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-446">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="cf329-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cf329-447">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="cf329-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cf329-448">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cf329-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cf329-449">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cf329-450">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cf329-451">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="cf329-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cf329-452">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="cf329-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cf329-453">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="cf329-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cf329-454">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cf329-455">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="cf329-455">The following table lists the available log levels.</span></span> <span data-ttu-id="cf329-456">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="cf329-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cf329-457">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="cf329-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cf329-458">字符串</span><span class="sxs-lookup"><span data-stu-id="cf329-458">String</span></span>                      | <span data-ttu-id="cf329-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cf329-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cf329-460">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="cf329-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cf329-461">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="cf329-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cf329-462">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cf329-463">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="cf329-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cf329-464">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="cf329-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cf329-465">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="cf329-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cf329-466">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cf329-467">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="cf329-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cf329-468">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="cf329-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cf329-469">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="cf329-469">Configure allowed transports</span></span>

<span data-ttu-id="cf329-470">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="cf329-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cf329-471">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cf329-472">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-472">All transports are enabled by default.</span></span>

<span data-ttu-id="cf329-473">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf329-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cf329-474">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cf329-475">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cf329-476">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cf329-477">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cf329-478">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="cf329-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cf329-479">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="cf329-479">Configure bearer authentication</span></span>

<span data-ttu-id="cf329-480">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="cf329-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cf329-481">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="cf329-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cf329-482">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cf329-483">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cf329-484">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cf329-485">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cf329-486">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cf329-487">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cf329-488">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cf329-489">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cf329-490">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="cf329-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-491">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-492">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-492">Option</span></span> | <span data-ttu-id="cf329-493">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-493">Default value</span></span> | <span data-ttu-id="cf329-494">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cf329-495">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-496">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-496">Timeout for server activity.</span></span> <span data-ttu-id="cf329-497">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-498">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-499">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cf329-500">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-500">15 seconds</span></span> | <span data-ttu-id="cf329-501">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-502">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-503">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-504">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-505">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-505">15 seconds</span></span> | <span data-ttu-id="cf329-506">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-507">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-508">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cf329-509">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="cf329-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cf329-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-511">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-511">Option</span></span> | <span data-ttu-id="cf329-512">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-512">Default value</span></span> | <span data-ttu-id="cf329-513">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cf329-514">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-515">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-515">Timeout for server activity.</span></span> <span data-ttu-id="cf329-516">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cf329-517">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-518">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cf329-519">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cf329-520">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-521">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-522">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-523">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-523">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-524">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-524">Option</span></span> | <span data-ttu-id="cf329-525">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-525">Default value</span></span> | <span data-ttu-id="cf329-526">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cf329-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cf329-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cf329-528">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-529">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-529">Timeout for server activity.</span></span> <span data-ttu-id="cf329-530">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-531">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-532">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cf329-533">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-533">15 seconds</span></span> | <span data-ttu-id="cf329-534">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-535">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-536">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-537">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cf329-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cf329-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cf329-539">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cf329-540">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-541">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-542">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cf329-543">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="cf329-543">Configure additional options</span></span>

<span data-ttu-id="cf329-544">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-545">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-546">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-546">.NET Option</span></span> |  <span data-ttu-id="cf329-547">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-547">Default value</span></span> | <span data-ttu-id="cf329-548">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cf329-549">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cf329-550">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-551">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-552">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cf329-553">空</span><span class="sxs-lookup"><span data-stu-id="cf329-553">Empty</span></span> | <span data-ttu-id="cf329-554">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cf329-555">空</span><span class="sxs-lookup"><span data-stu-id="cf329-555">Empty</span></span> | <span data-ttu-id="cf329-556">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cf329-557">空</span><span class="sxs-lookup"><span data-stu-id="cf329-557">Empty</span></span> | <span data-ttu-id="cf329-558">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cf329-559">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-559">5 seconds</span></span> | <span data-ttu-id="cf329-560">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="cf329-560">WebSockets only.</span></span> <span data-ttu-id="cf329-561">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cf329-562">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cf329-563">空</span><span class="sxs-lookup"><span data-stu-id="cf329-563">Empty</span></span> | <span data-ttu-id="cf329-564">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cf329-565">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="cf329-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cf329-566">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="cf329-567">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="cf329-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cf329-568">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cf329-569">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="cf329-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cf329-570">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="cf329-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cf329-571">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cf329-572">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="cf329-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cf329-573">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="cf329-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cf329-574">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cf329-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-576">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-576">JavaScript Option</span></span> | <span data-ttu-id="cf329-577">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-577">Default Value</span></span> | <span data-ttu-id="cf329-578">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cf329-579">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cf329-580">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="cf329-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cf329-581">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-582">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-583">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-584">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-584">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-585">Java 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-585">Java Option</span></span> | <span data-ttu-id="cf329-586">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-586">Default Value</span></span> | <span data-ttu-id="cf329-587">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cf329-588">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cf329-589">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-590">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-591">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cf329-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cf329-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cf329-593">空</span><span class="sxs-lookup"><span data-stu-id="cf329-593">Empty</span></span> | <span data-ttu-id="cf329-594">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cf329-595">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cf329-596">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cf329-597">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cf329-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cf329-598">其他资源</span><span class="sxs-lookup"><span data-stu-id="cf329-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cf329-599">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cf329-600">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cf329-601">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cf329-602">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="cf329-603">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cf329-604">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cf329-605">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cf329-606">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="cf329-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="cf329-607">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="cf329-608">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cf329-609">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cf329-610">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="cf329-611">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="cf329-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="cf329-612">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="cf329-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cf329-613">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-613">MessagePack serialization options</span></span>

<span data-ttu-id="cf329-614">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cf329-615">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="cf329-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-616">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cf329-617">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="cf329-617">Configure server options</span></span>

<span data-ttu-id="cf329-618">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="cf329-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cf329-619">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-619">Option</span></span> | <span data-ttu-id="cf329-620">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-620">Default Value</span></span> | <span data-ttu-id="cf329-621">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cf329-622">30 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-622">30 seconds</span></span> | <span data-ttu-id="cf329-623">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cf329-624">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cf329-625">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cf329-626">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-626">15 seconds</span></span> | <span data-ttu-id="cf329-627">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="cf329-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cf329-628">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-629">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-630">15 seconds</span></span> | <span data-ttu-id="cf329-631">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="cf329-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cf329-632">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cf329-633">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cf329-634">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="cf329-634">All installed protocols</span></span> | <span data-ttu-id="cf329-635">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-635">Protocols supported by this hub.</span></span> <span data-ttu-id="cf329-636">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cf329-637">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cf329-638">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="cf329-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="cf329-639">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="cf329-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="cf329-640">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="cf329-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="cf329-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-641">32 KB</span></span> | <span data-ttu-id="cf329-642">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="cf329-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="cf329-643">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cf329-644">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="cf329-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cf329-645">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="cf329-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cf329-646">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cf329-647">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="cf329-648">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cf329-649">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-649">Option</span></span> | <span data-ttu-id="cf329-650">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-650">Default Value</span></span> | <span data-ttu-id="cf329-651">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cf329-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-652">32 KB</span></span> | <span data-ttu-id="cf329-653">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="cf329-654">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="cf329-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cf329-655">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cf329-656">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="cf329-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cf329-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-657">32 KB</span></span> | <span data-ttu-id="cf329-658">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="cf329-659">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="cf329-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cf329-660">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="cf329-660">All Transports are enabled.</span></span> | <span data-ttu-id="cf329-661">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cf329-662">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-662">See below.</span></span> | <span data-ttu-id="cf329-663">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cf329-664">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-664">See below.</span></span> | <span data-ttu-id="cf329-665">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cf329-666">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cf329-667">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-667">Option</span></span> | <span data-ttu-id="cf329-668">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-668">Default Value</span></span> | <span data-ttu-id="cf329-669">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cf329-670">90秒</span><span class="sxs-lookup"><span data-stu-id="cf329-670">90 seconds</span></span> | <span data-ttu-id="cf329-671">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cf329-672">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="cf329-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cf329-673">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cf329-674">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-674">Option</span></span> | <span data-ttu-id="cf329-675">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-675">Default Value</span></span> | <span data-ttu-id="cf329-676">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cf329-677">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-677">5 seconds</span></span> | <span data-ttu-id="cf329-678">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="cf329-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cf329-679">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="cf329-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cf329-680">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="cf329-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cf329-681">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="cf329-681">Configure client options</span></span>

<span data-ttu-id="cf329-682">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="cf329-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cf329-683">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="cf329-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cf329-684">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="cf329-684">Configure logging</span></span>

<span data-ttu-id="cf329-685">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cf329-686">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="cf329-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cf329-687">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="cf329-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-688">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="cf329-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cf329-689">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="cf329-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cf329-690">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cf329-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cf329-691">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cf329-692">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cf329-693">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="cf329-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cf329-694">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="cf329-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="cf329-695">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="cf329-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="cf329-696">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="cf329-697">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="cf329-697">The following table lists the available log levels.</span></span> <span data-ttu-id="cf329-698">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="cf329-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="cf329-699">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="cf329-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="cf329-700">字符串</span><span class="sxs-lookup"><span data-stu-id="cf329-700">String</span></span>                      | <span data-ttu-id="cf329-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="cf329-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="cf329-702">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="cf329-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="cf329-703">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="cf329-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="cf329-704">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cf329-705">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="cf329-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cf329-706">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="cf329-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cf329-707">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="cf329-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cf329-708">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cf329-709">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="cf329-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cf329-710">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="cf329-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cf329-711">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="cf329-711">Configure allowed transports</span></span>

<span data-ttu-id="cf329-712">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="cf329-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cf329-713">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cf329-714">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-714">All transports are enabled by default.</span></span>

<span data-ttu-id="cf329-715">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf329-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cf329-716">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cf329-717">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="cf329-718">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="cf329-719">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cf329-720">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="cf329-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cf329-721">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="cf329-721">Configure bearer authentication</span></span>

<span data-ttu-id="cf329-722">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="cf329-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cf329-723">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="cf329-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cf329-724">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cf329-725">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cf329-726">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cf329-727">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cf329-728">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cf329-729">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cf329-730">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cf329-731">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cf329-732">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="cf329-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-733">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-734">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-734">Option</span></span> | <span data-ttu-id="cf329-735">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-735">Default value</span></span> | <span data-ttu-id="cf329-736">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cf329-737">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-738">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-738">Timeout for server activity.</span></span> <span data-ttu-id="cf329-739">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-740">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-741">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cf329-742">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-742">15 seconds</span></span> | <span data-ttu-id="cf329-743">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-744">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-745">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-746">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-747">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-747">15 seconds</span></span> | <span data-ttu-id="cf329-748">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-749">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-750">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cf329-751">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="cf329-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cf329-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-753">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-753">Option</span></span> | <span data-ttu-id="cf329-754">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-754">Default value</span></span> | <span data-ttu-id="cf329-755">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cf329-756">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-757">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-757">Timeout for server activity.</span></span> <span data-ttu-id="cf329-758">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cf329-759">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-760">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cf329-761">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cf329-762">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-763">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-764">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-765">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-765">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-766">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-766">Option</span></span> | <span data-ttu-id="cf329-767">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-767">Default value</span></span> | <span data-ttu-id="cf329-768">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cf329-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cf329-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cf329-770">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-771">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-771">Timeout for server activity.</span></span> <span data-ttu-id="cf329-772">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-773">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-774">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cf329-775">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-775">15 seconds</span></span> | <span data-ttu-id="cf329-776">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-777">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-778">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-779">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cf329-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cf329-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cf329-781">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cf329-782">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-783">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-784">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cf329-785">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="cf329-785">Configure additional options</span></span>

<span data-ttu-id="cf329-786">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-787">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-788">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-788">.NET Option</span></span> |  <span data-ttu-id="cf329-789">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-789">Default value</span></span> | <span data-ttu-id="cf329-790">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cf329-791">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cf329-792">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-793">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-794">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cf329-795">空</span><span class="sxs-lookup"><span data-stu-id="cf329-795">Empty</span></span> | <span data-ttu-id="cf329-796">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cf329-797">空</span><span class="sxs-lookup"><span data-stu-id="cf329-797">Empty</span></span> | <span data-ttu-id="cf329-798">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cf329-799">空</span><span class="sxs-lookup"><span data-stu-id="cf329-799">Empty</span></span> | <span data-ttu-id="cf329-800">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cf329-801">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-801">5 seconds</span></span> | <span data-ttu-id="cf329-802">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="cf329-802">WebSockets only.</span></span> <span data-ttu-id="cf329-803">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cf329-804">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cf329-805">空</span><span class="sxs-lookup"><span data-stu-id="cf329-805">Empty</span></span> | <span data-ttu-id="cf329-806">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cf329-807">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="cf329-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cf329-808">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="cf329-809">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="cf329-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cf329-810">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cf329-811">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="cf329-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cf329-812">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="cf329-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cf329-813">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cf329-814">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="cf329-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cf329-815">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="cf329-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cf329-816">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cf329-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-818">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-818">JavaScript Option</span></span> | <span data-ttu-id="cf329-819">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-819">Default Value</span></span> | <span data-ttu-id="cf329-820">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cf329-821">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cf329-822">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="cf329-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cf329-823">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-824">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-825">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-826">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-826">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-827">Java 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-827">Java Option</span></span> | <span data-ttu-id="cf329-828">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-828">Default Value</span></span> | <span data-ttu-id="cf329-829">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cf329-830">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cf329-831">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-832">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-833">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cf329-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cf329-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cf329-835">空</span><span class="sxs-lookup"><span data-stu-id="cf329-835">Empty</span></span> | <span data-ttu-id="cf329-836">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cf329-837">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cf329-838">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cf329-839">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cf329-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cf329-840">其他资源</span><span class="sxs-lookup"><span data-stu-id="cf329-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cf329-841">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cf329-842">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cf329-843">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cf329-844">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cf329-845">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cf329-846">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cf329-847">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="cf329-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="cf329-848">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="cf329-849">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cf329-850">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cf329-851">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cf329-852">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-852">MessagePack serialization options</span></span>

<span data-ttu-id="cf329-853">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cf329-854">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="cf329-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-855">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cf329-856">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="cf329-856">Configure server options</span></span>

<span data-ttu-id="cf329-857">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="cf329-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cf329-858">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-858">Option</span></span> | <span data-ttu-id="cf329-859">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-859">Default Value</span></span> | <span data-ttu-id="cf329-860">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="cf329-861">30 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-861">30 seconds</span></span> | <span data-ttu-id="cf329-862">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="cf329-863">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="cf329-864">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="cf329-865">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-865">15 seconds</span></span> | <span data-ttu-id="cf329-866">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="cf329-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cf329-867">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-868">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-869">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-869">15 seconds</span></span> | <span data-ttu-id="cf329-870">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="cf329-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cf329-871">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cf329-872">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cf329-873">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="cf329-873">All installed protocols</span></span> | <span data-ttu-id="cf329-874">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-874">Protocols supported by this hub.</span></span> <span data-ttu-id="cf329-875">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cf329-876">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cf329-877">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="cf329-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="cf329-878">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cf329-879">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="cf329-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cf329-880">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="cf329-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cf329-881">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cf329-882">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="cf329-883">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cf329-884">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-884">Option</span></span> | <span data-ttu-id="cf329-885">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-885">Default Value</span></span> | <span data-ttu-id="cf329-886">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cf329-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-887">32 KB</span></span> | <span data-ttu-id="cf329-888">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="cf329-889">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="cf329-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cf329-890">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cf329-891">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="cf329-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cf329-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-892">32 KB</span></span> | <span data-ttu-id="cf329-893">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="cf329-894">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="cf329-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cf329-895">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="cf329-895">All Transports are enabled.</span></span> | <span data-ttu-id="cf329-896">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cf329-897">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-897">See below.</span></span> | <span data-ttu-id="cf329-898">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cf329-899">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-899">See below.</span></span> | <span data-ttu-id="cf329-900">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cf329-901">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cf329-902">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-902">Option</span></span> | <span data-ttu-id="cf329-903">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-903">Default Value</span></span> | <span data-ttu-id="cf329-904">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cf329-905">90秒</span><span class="sxs-lookup"><span data-stu-id="cf329-905">90 seconds</span></span> | <span data-ttu-id="cf329-906">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cf329-907">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="cf329-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cf329-908">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cf329-909">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-909">Option</span></span> | <span data-ttu-id="cf329-910">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-910">Default Value</span></span> | <span data-ttu-id="cf329-911">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cf329-912">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-912">5 seconds</span></span> | <span data-ttu-id="cf329-913">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="cf329-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cf329-914">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="cf329-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cf329-915">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="cf329-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cf329-916">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="cf329-916">Configure client options</span></span>

<span data-ttu-id="cf329-917">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="cf329-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cf329-918">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="cf329-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cf329-919">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="cf329-919">Configure logging</span></span>

<span data-ttu-id="cf329-920">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cf329-921">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="cf329-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cf329-922">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="cf329-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-923">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="cf329-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cf329-924">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="cf329-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cf329-925">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cf329-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cf329-926">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cf329-927">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cf329-928">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="cf329-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cf329-929">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="cf329-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cf329-930">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cf329-931">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="cf329-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cf329-932">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="cf329-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cf329-933">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="cf329-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cf329-934">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cf329-935">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="cf329-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cf329-936">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="cf329-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cf329-937">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="cf329-937">Configure allowed transports</span></span>

<span data-ttu-id="cf329-938">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="cf329-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cf329-939">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cf329-940">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-940">All transports are enabled by default.</span></span>

<span data-ttu-id="cf329-941">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf329-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cf329-942">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="cf329-943">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cf329-944">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="cf329-944">Configure bearer authentication</span></span>

<span data-ttu-id="cf329-945">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="cf329-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cf329-946">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="cf329-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cf329-947">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cf329-948">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cf329-949">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cf329-950">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cf329-951">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cf329-952">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cf329-953">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cf329-954">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cf329-955">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="cf329-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-956">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-957">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-957">Option</span></span> | <span data-ttu-id="cf329-958">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-958">Default value</span></span> | <span data-ttu-id="cf329-959">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cf329-960">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-961">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-961">Timeout for server activity.</span></span> <span data-ttu-id="cf329-962">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-963">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-964">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cf329-965">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-965">15 seconds</span></span> | <span data-ttu-id="cf329-966">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-967">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-968">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-969">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-970">15 seconds</span></span> | <span data-ttu-id="cf329-971">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-972">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-973">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="cf329-974">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="cf329-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cf329-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-976">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-976">Option</span></span> | <span data-ttu-id="cf329-977">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-977">Default value</span></span> | <span data-ttu-id="cf329-978">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cf329-979">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-980">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-980">Timeout for server activity.</span></span> <span data-ttu-id="cf329-981">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cf329-982">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-983">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="cf329-984">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cf329-985">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-986">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-987">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-988">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-988">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-989">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-989">Option</span></span> | <span data-ttu-id="cf329-990">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-990">Default value</span></span> | <span data-ttu-id="cf329-991">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cf329-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cf329-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cf329-993">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-994">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-994">Timeout for server activity.</span></span> <span data-ttu-id="cf329-995">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-996">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-997">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cf329-998">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-998">15 seconds</span></span> | <span data-ttu-id="cf329-999">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-1000">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-1001">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-1002">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="cf329-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="cf329-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="cf329-1004">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="cf329-1005">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="cf329-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="cf329-1006">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="cf329-1007">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cf329-1008">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1008">Configure additional options</span></span>

<span data-ttu-id="cf329-1009">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-1011">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1011">.NET Option</span></span> |  <span data-ttu-id="cf329-1012">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1012">Default value</span></span> | <span data-ttu-id="cf329-1013">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cf329-1014">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cf329-1015">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-1016">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-1017">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cf329-1018">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1018">Empty</span></span> | <span data-ttu-id="cf329-1019">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cf329-1020">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1020">Empty</span></span> | <span data-ttu-id="cf329-1021">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cf329-1022">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1022">Empty</span></span> | <span data-ttu-id="cf329-1023">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cf329-1024">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-1024">5 seconds</span></span> | <span data-ttu-id="cf329-1025">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="cf329-1025">WebSockets only.</span></span> <span data-ttu-id="cf329-1026">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cf329-1027">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cf329-1028">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1028">Empty</span></span> | <span data-ttu-id="cf329-1029">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cf329-1030">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="cf329-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cf329-1031">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="cf329-1032">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="cf329-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cf329-1033">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cf329-1034">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="cf329-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cf329-1035">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="cf329-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cf329-1036">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cf329-1037">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="cf329-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cf329-1038">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="cf329-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cf329-1039">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cf329-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-1041">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1041">JavaScript Option</span></span> | <span data-ttu-id="cf329-1042">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1042">Default Value</span></span> | <span data-ttu-id="cf329-1043">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cf329-1044">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cf329-1045">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="cf329-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cf329-1046">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-1047">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-1048">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-1049">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-1050">Java 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1050">Java Option</span></span> | <span data-ttu-id="cf329-1051">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1051">Default Value</span></span> | <span data-ttu-id="cf329-1052">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cf329-1053">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cf329-1054">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-1055">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-1056">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cf329-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cf329-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cf329-1058">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1058">Empty</span></span> | <span data-ttu-id="cf329-1059">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cf329-1060">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cf329-1061">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cf329-1062">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cf329-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cf329-1063">其他资源</span><span class="sxs-lookup"><span data-stu-id="cf329-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="cf329-1064">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="cf329-1065">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="cf329-1066">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="cf329-1067">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cf329-1068">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="cf329-1069">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="cf329-1070">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="cf329-1071">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="cf329-1072">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="cf329-1073">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="cf329-1074">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="cf329-1075">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1075">MessagePack serialization options</span></span>

<span data-ttu-id="cf329-1076">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="cf329-1077">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-1078">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="cf329-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="cf329-1079">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1079">Configure server options</span></span>

<span data-ttu-id="cf329-1080">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="cf329-1081">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1081">Option</span></span> | <span data-ttu-id="cf329-1082">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1082">Default Value</span></span> | <span data-ttu-id="cf329-1083">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="cf329-1084">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-1084">15 seconds</span></span> | <span data-ttu-id="cf329-1085">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="cf329-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="cf329-1086">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-1087">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="cf329-1088">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-1088">15 seconds</span></span> | <span data-ttu-id="cf329-1089">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="cf329-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="cf329-1090">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="cf329-1091">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="cf329-1092">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="cf329-1092">All installed protocols</span></span> | <span data-ttu-id="cf329-1093">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="cf329-1094">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="cf329-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="cf329-1095">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="cf329-1096">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="cf329-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="cf329-1097">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="cf329-1098">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="cf329-1099">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="cf329-1100">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="cf329-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="cf329-1101">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="cf329-1102">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="cf329-1103">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1103">Option</span></span> | <span data-ttu-id="cf329-1104">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1104">Default Value</span></span> | <span data-ttu-id="cf329-1105">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="cf329-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-1106">32 KB</span></span> | <span data-ttu-id="cf329-1107">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="cf329-1108">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="cf329-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="cf329-1109">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="cf329-1110">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="cf329-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="cf329-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="cf329-1111">32 KB</span></span> | <span data-ttu-id="cf329-1112">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="cf329-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="cf329-1113">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="cf329-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="cf329-1114">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="cf329-1114">All Transports are enabled.</span></span> | <span data-ttu-id="cf329-1115">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="cf329-1116">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-1116">See below.</span></span> | <span data-ttu-id="cf329-1117">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="cf329-1118">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="cf329-1118">See below.</span></span> | <span data-ttu-id="cf329-1119">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="cf329-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="cf329-1120">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="cf329-1121">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1121">Option</span></span> | <span data-ttu-id="cf329-1122">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1122">Default Value</span></span> | <span data-ttu-id="cf329-1123">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="cf329-1124">90秒</span><span class="sxs-lookup"><span data-stu-id="cf329-1124">90 seconds</span></span> | <span data-ttu-id="cf329-1125">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="cf329-1126">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="cf329-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="cf329-1127">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="cf329-1128">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1128">Option</span></span> | <span data-ttu-id="cf329-1129">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1129">Default Value</span></span> | <span data-ttu-id="cf329-1130">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="cf329-1131">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-1131">5 seconds</span></span> | <span data-ttu-id="cf329-1132">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="cf329-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="cf329-1133">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="cf329-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="cf329-1134">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="cf329-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="cf329-1135">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1135">Configure client options</span></span>

<span data-ttu-id="cf329-1136">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="cf329-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="cf329-1137">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="cf329-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="cf329-1138">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="cf329-1138">Configure logging</span></span>

<span data-ttu-id="cf329-1139">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="cf329-1140">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="cf329-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="cf329-1141">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="cf329-1142">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="cf329-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="cf329-1143">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="cf329-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="cf329-1144">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cf329-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="cf329-1145">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cf329-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="cf329-1146">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="cf329-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="cf329-1147">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="cf329-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="cf329-1148">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="cf329-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="cf329-1149">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="cf329-1150">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="cf329-1151">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="cf329-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="cf329-1152">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="cf329-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="cf329-1153">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="cf329-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="cf329-1154">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="cf329-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="cf329-1155">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="cf329-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="cf329-1156">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="cf329-1156">Configure allowed transports</span></span>

<span data-ttu-id="cf329-1157">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="cf329-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="cf329-1158">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="cf329-1159">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="cf329-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="cf329-1160">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf329-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="cf329-1161">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="cf329-1162">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="cf329-1162">Configure bearer authentication</span></span>

<span data-ttu-id="cf329-1163">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="cf329-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="cf329-1164">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="cf329-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="cf329-1165">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="cf329-1166">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="cf329-1167">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="cf329-1168">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="cf329-1169">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="cf329-1170">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="cf329-1171">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="cf329-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="cf329-1172">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="cf329-1173">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="cf329-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-1175">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1175">Option</span></span> | <span data-ttu-id="cf329-1176">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1176">Default value</span></span> | <span data-ttu-id="cf329-1177">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="cf329-1178">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-1179">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-1179">Timeout for server activity.</span></span> <span data-ttu-id="cf329-1180">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-1181">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-1182">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="cf329-1183">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-1183">15 seconds</span></span> | <span data-ttu-id="cf329-1184">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-1185">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="cf329-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="cf329-1186">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-1187">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="cf329-1188">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="cf329-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="cf329-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-1190">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1190">Option</span></span> | <span data-ttu-id="cf329-1191">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1191">Default value</span></span> | <span data-ttu-id="cf329-1192">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="cf329-1193">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-1194">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-1194">Timeout for server activity.</span></span> <span data-ttu-id="cf329-1195">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="cf329-1196">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-1197">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-1198">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-1199">选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1199">Option</span></span> | <span data-ttu-id="cf329-1200">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1200">Default value</span></span> | <span data-ttu-id="cf329-1201">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="cf329-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="cf329-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="cf329-1203">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="cf329-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="cf329-1204">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="cf329-1204">Timeout for server activity.</span></span> <span data-ttu-id="cf329-1205">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-1206">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="cf329-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="cf329-1207">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="cf329-1208">15 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-1208">15 seconds</span></span> | <span data-ttu-id="cf329-1209">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="cf329-1210">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="cf329-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="cf329-1211">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="cf329-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="cf329-1212">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="cf329-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="cf329-1213">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1213">Configure additional options</span></span>

<span data-ttu-id="cf329-1214">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="cf329-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="cf329-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="cf329-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="cf329-1216">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1216">.NET Option</span></span> |  <span data-ttu-id="cf329-1217">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1217">Default value</span></span> | <span data-ttu-id="cf329-1218">说明</span><span class="sxs-lookup"><span data-stu-id="cf329-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="cf329-1219">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="cf329-1220">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-1221">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-1222">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="cf329-1223">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1223">Empty</span></span> | <span data-ttu-id="cf329-1224">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="cf329-1225">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1225">Empty</span></span> | <span data-ttu-id="cf329-1226">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="cf329-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="cf329-1227">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1227">Empty</span></span> | <span data-ttu-id="cf329-1228">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="cf329-1229">5 秒</span><span class="sxs-lookup"><span data-stu-id="cf329-1229">5 seconds</span></span> | <span data-ttu-id="cf329-1230">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="cf329-1230">WebSockets only.</span></span> <span data-ttu-id="cf329-1231">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="cf329-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="cf329-1232">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="cf329-1233">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1233">Empty</span></span> | <span data-ttu-id="cf329-1234">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="cf329-1235">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="cf329-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="cf329-1236">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="cf329-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="cf329-1237">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="cf329-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="cf329-1238">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="cf329-1239">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="cf329-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="cf329-1240">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="cf329-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="cf329-1241">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="cf329-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="cf329-1242">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="cf329-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="cf329-1243">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="cf329-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="cf329-1244">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="cf329-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="cf329-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="cf329-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="cf329-1246">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1246">JavaScript Option</span></span> | <span data-ttu-id="cf329-1247">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1247">Default Value</span></span> | <span data-ttu-id="cf329-1248">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="cf329-1249">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="cf329-1250">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="cf329-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="cf329-1251">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-1252">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-1253">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="cf329-1254">Java</span><span class="sxs-lookup"><span data-stu-id="cf329-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="cf329-1255">Java 选项</span><span class="sxs-lookup"><span data-stu-id="cf329-1255">Java Option</span></span> | <span data-ttu-id="cf329-1256">默认值</span><span class="sxs-lookup"><span data-stu-id="cf329-1256">Default Value</span></span> | <span data-ttu-id="cf329-1257">描述</span><span class="sxs-lookup"><span data-stu-id="cf329-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="cf329-1258">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf329-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="cf329-1259">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="cf329-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="cf329-1260">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="cf329-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="cf329-1261">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="cf329-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="cf329-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="cf329-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="cf329-1263">空</span><span class="sxs-lookup"><span data-stu-id="cf329-1263">Empty</span></span> | <span data-ttu-id="cf329-1264">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="cf329-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="cf329-1265">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="cf329-1266">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="cf329-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="cf329-1267">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="cf329-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="cf329-1268">其他资源</span><span class="sxs-lookup"><span data-stu-id="cf329-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
