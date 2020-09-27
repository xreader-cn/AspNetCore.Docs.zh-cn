---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393868"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="c1469-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="c1469-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c1469-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c1469-105">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c1469-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c1469-107">可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c1469-108">`AddJsonProtocol`可在[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c1469-109">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c1469-110">该对象上的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c1469-111">有关详细信息，请参阅 [ 文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="c1469-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c1469-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c1469-113">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c1469-114">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c1469-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c1469-116">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c1469-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c1469-117">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅 [切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="c1469-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c1469-118">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-118">MessagePack serialization options</span></span>

<span data-ttu-id="c1469-119">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c1469-120">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-121">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c1469-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c1469-122">Configure server options</span></span>

<span data-ttu-id="c1469-123">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c1469-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c1469-124">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-124">Option</span></span> | <span data-ttu-id="c1469-125">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-125">Default Value</span></span> | <span data-ttu-id="c1469-126">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c1469-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-127">30 seconds</span></span> | <span data-ttu-id="c1469-128">如果客户端未收到消息 (在此时间间隔内包含 keep-alive) ，服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c1469-129">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c1469-130">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c1469-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-131">15 seconds</span></span> | <span data-ttu-id="c1469-132">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c1469-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c1469-133">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-134">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-135">15 seconds</span></span> | <span data-ttu-id="c1469-136">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c1469-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c1469-137">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c1469-138">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c1469-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c1469-139">All installed protocols</span></span> | <span data-ttu-id="c1469-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-140">Protocols supported by this hub.</span></span> <span data-ttu-id="c1469-141">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c1469-142">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c1469-143">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c1469-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c1469-144">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="c1469-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c1469-145">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="c1469-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c1469-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-146">32 KB</span></span> | <span data-ttu-id="c1469-147">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="c1469-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="c1469-148">1</span><span class="sxs-lookup"><span data-stu-id="c1469-148">1</span></span> | <span data-ttu-id="c1469-149">每个客户端可以在进行排队之前并行调用的最大集线器方法数。</span><span class="sxs-lookup"><span data-stu-id="c1469-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="c1469-150">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c1469-151">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c1469-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c1469-152">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c1469-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c1469-153">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c1469-154">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c1469-155">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c1469-156">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-156">Option</span></span> | <span data-ttu-id="c1469-157">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-157">Default Value</span></span> | <span data-ttu-id="c1469-158">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c1469-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-159">32 KB</span></span> | <span data-ttu-id="c1469-160">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c1469-161">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c1469-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c1469-162">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c1469-163">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c1469-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c1469-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-164">32 KB</span></span> | <span data-ttu-id="c1469-165">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c1469-166">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c1469-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c1469-167">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c1469-167">All Transports are enabled.</span></span> | <span data-ttu-id="c1469-168">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c1469-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-169">See below.</span></span> | <span data-ttu-id="c1469-170">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c1469-171">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-171">See below.</span></span> | <span data-ttu-id="c1469-172">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c1469-173">0</span><span class="sxs-lookup"><span data-stu-id="c1469-173">0</span></span> | <span data-ttu-id="c1469-174">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="c1469-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c1469-175">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="c1469-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c1469-176">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c1469-177">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-177">Option</span></span> | <span data-ttu-id="c1469-178">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-178">Default Value</span></span> | <span data-ttu-id="c1469-179">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c1469-180">90秒</span><span class="sxs-lookup"><span data-stu-id="c1469-180">90 seconds</span></span> | <span data-ttu-id="c1469-181">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c1469-182">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c1469-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c1469-183">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c1469-184">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-184">Option</span></span> | <span data-ttu-id="c1469-185">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-185">Default Value</span></span> | <span data-ttu-id="c1469-186">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c1469-187">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-187">5 seconds</span></span> | <span data-ttu-id="c1469-188">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c1469-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c1469-189">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c1469-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c1469-190">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c1469-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c1469-191">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c1469-191">Configure client options</span></span>

<span data-ttu-id="c1469-192">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c1469-193">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c1469-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c1469-194">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c1469-194">Configure logging</span></span>

<span data-ttu-id="c1469-195">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c1469-196">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c1469-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c1469-197">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-198">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c1469-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c1469-199">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="c1469-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c1469-200">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c1469-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c1469-201">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c1469-202">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c1469-203">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c1469-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c1469-204">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c1469-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c1469-205">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="c1469-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c1469-206">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c1469-207">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="c1469-207">The following table lists the available log levels.</span></span> <span data-ttu-id="c1469-208">为 `configureLogging` 设置将记录的 **最小** 日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="c1469-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c1469-209">将记录在此级别上记录的消息 **或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="c1469-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c1469-210">String</span><span class="sxs-lookup"><span data-stu-id="c1469-210">String</span></span>                      | <span data-ttu-id="c1469-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c1469-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c1469-212">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="c1469-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c1469-213">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="c1469-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c1469-214">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c1469-215">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c1469-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c1469-216">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c1469-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c1469-217">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c1469-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c1469-218">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c1469-219">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c1469-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c1469-220">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c1469-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c1469-221">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c1469-221">Configure allowed transports</span></span>

<span data-ttu-id="c1469-222">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c1469-223">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c1469-224">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-224">All transports are enabled by default.</span></span>

<span data-ttu-id="c1469-225">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c1469-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c1469-226">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c1469-227">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c1469-228">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c1469-229">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c1469-230">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="c1469-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c1469-231">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c1469-231">Configure bearer authentication</span></span>

<span data-ttu-id="c1469-232">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c1469-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c1469-233">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c1469-234">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在服务器发送的事件和 websocket 请求) 中应用标 (头的能力。</span><span class="sxs-lookup"><span data-stu-id="c1469-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c1469-235">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c1469-236">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c1469-237">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c1469-238">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c1469-239">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c1469-240">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c1469-241">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c1469-242">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c1469-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-243">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-244">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-244">Option</span></span> | <span data-ttu-id="c1469-245">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-245">Default value</span></span> | <span data-ttu-id="c1469-246">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c1469-247">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-248">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-248">Timeout for server activity.</span></span> <span data-ttu-id="c1469-249">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-250">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-251">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c1469-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-252">15 seconds</span></span> | <span data-ttu-id="c1469-253">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-254">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-255">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-256">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-257">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-257">15 seconds</span></span> | <span data-ttu-id="c1469-258">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-259">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-260">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c1469-261">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c1469-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c1469-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-263">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-263">Option</span></span> | <span data-ttu-id="c1469-264">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-264">Default value</span></span> | <span data-ttu-id="c1469-265">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c1469-266">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-267">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-267">Timeout for server activity.</span></span> <span data-ttu-id="c1469-268">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c1469-269">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-270">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c1469-271">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c1469-272">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-273">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-274">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-275">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-275">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-276">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-276">Option</span></span> | <span data-ttu-id="c1469-277">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-277">Default value</span></span> | <span data-ttu-id="c1469-278">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c1469-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c1469-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c1469-280">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-281">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-281">Timeout for server activity.</span></span> <span data-ttu-id="c1469-282">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-283">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-284">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c1469-285">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-285">15 seconds</span></span> | <span data-ttu-id="c1469-286">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-287">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-288">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-289">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c1469-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c1469-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c1469-291">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c1469-292">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-293">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-294">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c1469-295">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c1469-295">Configure additional options</span></span>

<span data-ttu-id="c1469-296">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-297">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-298">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-298">.NET Option</span></span> |  <span data-ttu-id="c1469-299">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-299">Default value</span></span> | <span data-ttu-id="c1469-300">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c1469-301">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c1469-302">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-303">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-304">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c1469-305">空</span><span class="sxs-lookup"><span data-stu-id="c1469-305">Empty</span></span> | <span data-ttu-id="c1469-306">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c1469-307">空</span><span class="sxs-lookup"><span data-stu-id="c1469-307">Empty</span></span> | <span data-ttu-id="c1469-308">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c1469-309">空</span><span class="sxs-lookup"><span data-stu-id="c1469-309">Empty</span></span> | <span data-ttu-id="c1469-310">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c1469-311">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-311">5 seconds</span></span> | <span data-ttu-id="c1469-312">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c1469-312">WebSockets only.</span></span> <span data-ttu-id="c1469-313">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c1469-314">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c1469-315">空</span><span class="sxs-lookup"><span data-stu-id="c1469-315">Empty</span></span> | <span data-ttu-id="c1469-316">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c1469-317">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c1469-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c1469-318">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="c1469-319">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c1469-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c1469-320">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c1469-321">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c1469-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c1469-322">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c1469-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c1469-323">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c1469-324">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c1469-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c1469-325">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c1469-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c1469-326">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c1469-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-328">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-328">JavaScript Option</span></span> | <span data-ttu-id="c1469-329">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-329">Default Value</span></span> | <span data-ttu-id="c1469-330">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c1469-331">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="c1469-332">每个 HTTP 请求发送的标头的字典。</span><span class="sxs-lookup"><span data-stu-id="c1469-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="c1469-333">在浏览器中发送标头对于 Websocket 或流不起作用 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。</span><span class="sxs-lookup"><span data-stu-id="c1469-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c1469-334">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="c1469-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c1469-335">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-336">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-337">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="c1469-338">指定是否将凭据与 CORS 请求一起发送。</span><span class="sxs-lookup"><span data-stu-id="c1469-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="c1469-339">Azure App Service cookie 将用于粘滞会话，并且需要启用此选项才能正常工作。</span><span class="sxs-lookup"><span data-stu-id="c1469-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="c1469-340">有关 CORS 的详细信息 SignalR ，请参阅 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="c1469-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-341">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-341">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-342">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-342">Java Option</span></span> | <span data-ttu-id="c1469-343">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-343">Default Value</span></span> | <span data-ttu-id="c1469-344">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c1469-345">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c1469-346">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-347">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-348">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c1469-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c1469-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c1469-350">空</span><span class="sxs-lookup"><span data-stu-id="c1469-350">Empty</span></span> | <span data-ttu-id="c1469-351">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c1469-352">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c1469-353">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c1469-354">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c1469-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c1469-355">其他资源</span><span class="sxs-lookup"><span data-stu-id="c1469-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c1469-356">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c1469-357">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c1469-358">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c1469-359">可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c1469-360">`AddJsonProtocol`可在[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c1469-361">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c1469-362">该对象上的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c1469-363">有关详细信息，请参阅 [ 文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="c1469-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c1469-364">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c1469-365">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c1469-366">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c1469-367">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c1469-368">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c1469-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c1469-369">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅 [切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="c1469-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c1469-370">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-370">MessagePack serialization options</span></span>

<span data-ttu-id="c1469-371">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c1469-372">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-373">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c1469-374">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c1469-374">Configure server options</span></span>

<span data-ttu-id="c1469-375">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c1469-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c1469-376">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-376">Option</span></span> | <span data-ttu-id="c1469-377">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-377">Default Value</span></span> | <span data-ttu-id="c1469-378">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c1469-379">30 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-379">30 seconds</span></span> | <span data-ttu-id="c1469-380">如果客户端未收到消息 (在此时间间隔内包含 keep-alive) ，服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c1469-381">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c1469-382">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c1469-383">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-383">15 seconds</span></span> | <span data-ttu-id="c1469-384">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c1469-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c1469-385">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-386">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-387">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-387">15 seconds</span></span> | <span data-ttu-id="c1469-388">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c1469-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c1469-389">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c1469-390">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c1469-391">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c1469-391">All installed protocols</span></span> | <span data-ttu-id="c1469-392">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-392">Protocols supported by this hub.</span></span> <span data-ttu-id="c1469-393">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c1469-394">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c1469-395">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c1469-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c1469-396">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="c1469-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c1469-397">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="c1469-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c1469-398">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-398">32 KB</span></span> | <span data-ttu-id="c1469-399">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="c1469-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c1469-400">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c1469-401">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c1469-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c1469-402">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c1469-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c1469-403">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c1469-404">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c1469-405">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c1469-406">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-406">Option</span></span> | <span data-ttu-id="c1469-407">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-407">Default Value</span></span> | <span data-ttu-id="c1469-408">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c1469-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-409">32 KB</span></span> | <span data-ttu-id="c1469-410">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c1469-411">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c1469-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c1469-412">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c1469-413">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c1469-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c1469-414">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-414">32 KB</span></span> | <span data-ttu-id="c1469-415">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c1469-416">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c1469-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c1469-417">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c1469-417">All Transports are enabled.</span></span> | <span data-ttu-id="c1469-418">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c1469-419">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-419">See below.</span></span> | <span data-ttu-id="c1469-420">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c1469-421">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-421">See below.</span></span> | <span data-ttu-id="c1469-422">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c1469-423">0</span><span class="sxs-lookup"><span data-stu-id="c1469-423">0</span></span> | <span data-ttu-id="c1469-424">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="c1469-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c1469-425">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="c1469-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c1469-426">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c1469-427">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-427">Option</span></span> | <span data-ttu-id="c1469-428">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-428">Default Value</span></span> | <span data-ttu-id="c1469-429">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c1469-430">90秒</span><span class="sxs-lookup"><span data-stu-id="c1469-430">90 seconds</span></span> | <span data-ttu-id="c1469-431">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c1469-432">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c1469-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c1469-433">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c1469-434">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-434">Option</span></span> | <span data-ttu-id="c1469-435">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-435">Default Value</span></span> | <span data-ttu-id="c1469-436">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c1469-437">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-437">5 seconds</span></span> | <span data-ttu-id="c1469-438">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c1469-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c1469-439">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c1469-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c1469-440">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c1469-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c1469-441">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c1469-441">Configure client options</span></span>

<span data-ttu-id="c1469-442">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c1469-443">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c1469-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c1469-444">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c1469-444">Configure logging</span></span>

<span data-ttu-id="c1469-445">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c1469-446">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c1469-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c1469-447">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-448">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c1469-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c1469-449">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="c1469-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c1469-450">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c1469-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c1469-451">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c1469-452">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c1469-453">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c1469-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c1469-454">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c1469-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c1469-455">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="c1469-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c1469-456">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c1469-457">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="c1469-457">The following table lists the available log levels.</span></span> <span data-ttu-id="c1469-458">为 `configureLogging` 设置将记录的 **最小** 日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="c1469-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c1469-459">将记录在此级别上记录的消息 **或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="c1469-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c1469-460">String</span><span class="sxs-lookup"><span data-stu-id="c1469-460">String</span></span>                      | <span data-ttu-id="c1469-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c1469-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c1469-462">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="c1469-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c1469-463">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="c1469-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c1469-464">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c1469-465">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c1469-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c1469-466">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c1469-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c1469-467">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c1469-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c1469-468">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c1469-469">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c1469-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c1469-470">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c1469-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c1469-471">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c1469-471">Configure allowed transports</span></span>

<span data-ttu-id="c1469-472">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c1469-473">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c1469-474">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-474">All transports are enabled by default.</span></span>

<span data-ttu-id="c1469-475">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c1469-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c1469-476">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c1469-477">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c1469-478">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c1469-479">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c1469-480">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="c1469-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c1469-481">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c1469-481">Configure bearer authentication</span></span>

<span data-ttu-id="c1469-482">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c1469-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c1469-483">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c1469-484">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在服务器发送的事件和 websocket 请求) 中应用标 (头的能力。</span><span class="sxs-lookup"><span data-stu-id="c1469-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c1469-485">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c1469-486">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c1469-487">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c1469-488">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c1469-489">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c1469-490">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c1469-491">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c1469-492">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c1469-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-493">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-494">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-494">Option</span></span> | <span data-ttu-id="c1469-495">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-495">Default value</span></span> | <span data-ttu-id="c1469-496">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c1469-497">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-498">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-498">Timeout for server activity.</span></span> <span data-ttu-id="c1469-499">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-500">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-501">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c1469-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-502">15 seconds</span></span> | <span data-ttu-id="c1469-503">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-504">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-505">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-506">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-507">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-507">15 seconds</span></span> | <span data-ttu-id="c1469-508">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-509">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-510">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c1469-511">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c1469-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c1469-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-513">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-513">Option</span></span> | <span data-ttu-id="c1469-514">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-514">Default value</span></span> | <span data-ttu-id="c1469-515">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c1469-516">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-517">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-517">Timeout for server activity.</span></span> <span data-ttu-id="c1469-518">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c1469-519">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-520">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c1469-521">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c1469-522">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-523">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-524">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-525">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-525">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-526">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-526">Option</span></span> | <span data-ttu-id="c1469-527">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-527">Default value</span></span> | <span data-ttu-id="c1469-528">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c1469-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c1469-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c1469-530">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-531">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-531">Timeout for server activity.</span></span> <span data-ttu-id="c1469-532">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-533">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-534">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c1469-535">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-535">15 seconds</span></span> | <span data-ttu-id="c1469-536">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-537">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-538">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-539">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c1469-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c1469-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c1469-541">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c1469-542">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-543">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-544">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c1469-545">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c1469-545">Configure additional options</span></span>

<span data-ttu-id="c1469-546">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-547">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-548">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-548">.NET Option</span></span> |  <span data-ttu-id="c1469-549">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-549">Default value</span></span> | <span data-ttu-id="c1469-550">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c1469-551">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c1469-552">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-553">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-554">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c1469-555">空</span><span class="sxs-lookup"><span data-stu-id="c1469-555">Empty</span></span> | <span data-ttu-id="c1469-556">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c1469-557">空</span><span class="sxs-lookup"><span data-stu-id="c1469-557">Empty</span></span> | <span data-ttu-id="c1469-558">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c1469-559">空</span><span class="sxs-lookup"><span data-stu-id="c1469-559">Empty</span></span> | <span data-ttu-id="c1469-560">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c1469-561">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-561">5 seconds</span></span> | <span data-ttu-id="c1469-562">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c1469-562">WebSockets only.</span></span> <span data-ttu-id="c1469-563">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c1469-564">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c1469-565">空</span><span class="sxs-lookup"><span data-stu-id="c1469-565">Empty</span></span> | <span data-ttu-id="c1469-566">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c1469-567">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c1469-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c1469-568">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="c1469-569">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c1469-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c1469-570">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c1469-571">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c1469-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c1469-572">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c1469-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c1469-573">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c1469-574">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c1469-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c1469-575">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c1469-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c1469-576">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c1469-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-578">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-578">JavaScript Option</span></span> | <span data-ttu-id="c1469-579">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-579">Default Value</span></span> | <span data-ttu-id="c1469-580">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c1469-581">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c1469-582">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="c1469-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c1469-583">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-584">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-585">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-586">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-586">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-587">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-587">Java Option</span></span> | <span data-ttu-id="c1469-588">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-588">Default Value</span></span> | <span data-ttu-id="c1469-589">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c1469-590">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c1469-591">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-592">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-593">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c1469-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c1469-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c1469-595">空</span><span class="sxs-lookup"><span data-stu-id="c1469-595">Empty</span></span> | <span data-ttu-id="c1469-596">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c1469-597">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c1469-598">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c1469-599">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c1469-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c1469-600">其他资源</span><span class="sxs-lookup"><span data-stu-id="c1469-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c1469-601">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c1469-602">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c1469-603">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c1469-604">可以使用 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c1469-605">`AddJsonProtocol`可在[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c1469-606">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c1469-607">该对象上的 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c1469-608">有关详细信息，请参阅 [ 文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="c1469-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c1469-609">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="c1469-610">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c1469-611">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c1469-612">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c1469-613">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c1469-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c1469-614">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅 [切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="c1469-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c1469-615">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-615">MessagePack serialization options</span></span>

<span data-ttu-id="c1469-616">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c1469-617">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-618">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c1469-619">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c1469-619">Configure server options</span></span>

<span data-ttu-id="c1469-620">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c1469-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c1469-621">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-621">Option</span></span> | <span data-ttu-id="c1469-622">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-622">Default Value</span></span> | <span data-ttu-id="c1469-623">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c1469-624">30 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-624">30 seconds</span></span> | <span data-ttu-id="c1469-625">如果客户端未收到消息 (在此时间间隔内包含 keep-alive) ，服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c1469-626">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c1469-627">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c1469-628">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-628">15 seconds</span></span> | <span data-ttu-id="c1469-629">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c1469-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c1469-630">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-631">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-632">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-632">15 seconds</span></span> | <span data-ttu-id="c1469-633">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c1469-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c1469-634">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c1469-635">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c1469-636">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c1469-636">All installed protocols</span></span> | <span data-ttu-id="c1469-637">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-637">Protocols supported by this hub.</span></span> <span data-ttu-id="c1469-638">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c1469-639">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c1469-640">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c1469-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c1469-641">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="c1469-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c1469-642">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="c1469-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c1469-643">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-643">32 KB</span></span> | <span data-ttu-id="c1469-644">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="c1469-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c1469-645">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c1469-646">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c1469-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c1469-647">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c1469-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c1469-648">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c1469-649">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c1469-650">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c1469-651">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-651">Option</span></span> | <span data-ttu-id="c1469-652">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-652">Default Value</span></span> | <span data-ttu-id="c1469-653">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c1469-654">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-654">32 KB</span></span> | <span data-ttu-id="c1469-655">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c1469-656">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c1469-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c1469-657">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c1469-658">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c1469-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c1469-659">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-659">32 KB</span></span> | <span data-ttu-id="c1469-660">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c1469-661">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c1469-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c1469-662">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c1469-662">All Transports are enabled.</span></span> | <span data-ttu-id="c1469-663">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c1469-664">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-664">See below.</span></span> | <span data-ttu-id="c1469-665">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c1469-666">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-666">See below.</span></span> | <span data-ttu-id="c1469-667">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c1469-668">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c1469-669">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-669">Option</span></span> | <span data-ttu-id="c1469-670">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-670">Default Value</span></span> | <span data-ttu-id="c1469-671">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c1469-672">90秒</span><span class="sxs-lookup"><span data-stu-id="c1469-672">90 seconds</span></span> | <span data-ttu-id="c1469-673">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c1469-674">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c1469-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c1469-675">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c1469-676">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-676">Option</span></span> | <span data-ttu-id="c1469-677">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-677">Default Value</span></span> | <span data-ttu-id="c1469-678">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c1469-679">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-679">5 seconds</span></span> | <span data-ttu-id="c1469-680">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c1469-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c1469-681">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c1469-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c1469-682">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c1469-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c1469-683">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c1469-683">Configure client options</span></span>

<span data-ttu-id="c1469-684">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c1469-685">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c1469-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c1469-686">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c1469-686">Configure logging</span></span>

<span data-ttu-id="c1469-687">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c1469-688">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c1469-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c1469-689">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-690">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c1469-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c1469-691">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="c1469-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c1469-692">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c1469-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c1469-693">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c1469-694">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c1469-695">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c1469-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c1469-696">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c1469-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c1469-697">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="c1469-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c1469-698">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c1469-699">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="c1469-699">The following table lists the available log levels.</span></span> <span data-ttu-id="c1469-700">为 `configureLogging` 设置将记录的 **最小** 日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="c1469-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c1469-701">将记录在此级别上记录的消息 **或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="c1469-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c1469-702">String</span><span class="sxs-lookup"><span data-stu-id="c1469-702">String</span></span>                      | <span data-ttu-id="c1469-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c1469-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c1469-704">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="c1469-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c1469-705">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="c1469-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c1469-706">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c1469-707">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c1469-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c1469-708">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c1469-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c1469-709">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c1469-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c1469-710">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c1469-711">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c1469-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c1469-712">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c1469-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c1469-713">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c1469-713">Configure allowed transports</span></span>

<span data-ttu-id="c1469-714">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c1469-715">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c1469-716">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-716">All transports are enabled by default.</span></span>

<span data-ttu-id="c1469-717">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c1469-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c1469-718">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c1469-719">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c1469-720">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c1469-721">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c1469-722">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="c1469-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c1469-723">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c1469-723">Configure bearer authentication</span></span>

<span data-ttu-id="c1469-724">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c1469-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c1469-725">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c1469-726">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在服务器发送的事件和 websocket 请求) 中应用标 (头的能力。</span><span class="sxs-lookup"><span data-stu-id="c1469-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c1469-727">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c1469-728">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c1469-729">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c1469-730">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c1469-731">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c1469-732">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c1469-733">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c1469-734">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c1469-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-735">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-736">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-736">Option</span></span> | <span data-ttu-id="c1469-737">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-737">Default value</span></span> | <span data-ttu-id="c1469-738">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c1469-739">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-740">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-740">Timeout for server activity.</span></span> <span data-ttu-id="c1469-741">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-742">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-743">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c1469-744">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-744">15 seconds</span></span> | <span data-ttu-id="c1469-745">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-746">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-747">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-748">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-749">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-749">15 seconds</span></span> | <span data-ttu-id="c1469-750">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-751">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-752">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c1469-753">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c1469-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c1469-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-755">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-755">Option</span></span> | <span data-ttu-id="c1469-756">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-756">Default value</span></span> | <span data-ttu-id="c1469-757">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c1469-758">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-759">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-759">Timeout for server activity.</span></span> <span data-ttu-id="c1469-760">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c1469-761">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-762">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c1469-763">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c1469-764">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-765">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-766">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-767">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-767">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-768">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-768">Option</span></span> | <span data-ttu-id="c1469-769">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-769">Default value</span></span> | <span data-ttu-id="c1469-770">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c1469-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c1469-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c1469-772">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-773">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-773">Timeout for server activity.</span></span> <span data-ttu-id="c1469-774">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-775">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-776">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c1469-777">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-777">15 seconds</span></span> | <span data-ttu-id="c1469-778">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-779">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-780">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-781">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c1469-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c1469-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c1469-783">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c1469-784">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-785">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-786">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c1469-787">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c1469-787">Configure additional options</span></span>

<span data-ttu-id="c1469-788">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-789">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-790">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-790">.NET Option</span></span> |  <span data-ttu-id="c1469-791">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-791">Default value</span></span> | <span data-ttu-id="c1469-792">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c1469-793">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c1469-794">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-795">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-796">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c1469-797">空</span><span class="sxs-lookup"><span data-stu-id="c1469-797">Empty</span></span> | <span data-ttu-id="c1469-798">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c1469-799">空</span><span class="sxs-lookup"><span data-stu-id="c1469-799">Empty</span></span> | <span data-ttu-id="c1469-800">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c1469-801">空</span><span class="sxs-lookup"><span data-stu-id="c1469-801">Empty</span></span> | <span data-ttu-id="c1469-802">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c1469-803">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-803">5 seconds</span></span> | <span data-ttu-id="c1469-804">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c1469-804">WebSockets only.</span></span> <span data-ttu-id="c1469-805">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c1469-806">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c1469-807">空</span><span class="sxs-lookup"><span data-stu-id="c1469-807">Empty</span></span> | <span data-ttu-id="c1469-808">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c1469-809">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c1469-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c1469-810">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="c1469-811">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c1469-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c1469-812">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c1469-813">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c1469-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c1469-814">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c1469-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c1469-815">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c1469-816">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c1469-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c1469-817">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c1469-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c1469-818">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c1469-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-820">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-820">JavaScript Option</span></span> | <span data-ttu-id="c1469-821">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-821">Default Value</span></span> | <span data-ttu-id="c1469-822">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c1469-823">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c1469-824">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="c1469-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c1469-825">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-826">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-827">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-828">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-828">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-829">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-829">Java Option</span></span> | <span data-ttu-id="c1469-830">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-830">Default Value</span></span> | <span data-ttu-id="c1469-831">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c1469-832">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c1469-833">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-834">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-835">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c1469-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c1469-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c1469-837">空</span><span class="sxs-lookup"><span data-stu-id="c1469-837">Empty</span></span> | <span data-ttu-id="c1469-838">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c1469-839">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c1469-840">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c1469-841">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c1469-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c1469-842">其他资源</span><span class="sxs-lookup"><span data-stu-id="c1469-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c1469-843">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c1469-844">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c1469-845">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c1469-846">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c1469-847">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c1469-848">该对象的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c1469-849">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="c1469-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c1469-850">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c1469-851">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c1469-852">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c1469-853">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c1469-854">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-854">MessagePack serialization options</span></span>

<span data-ttu-id="c1469-855">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c1469-856">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-857">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c1469-858">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c1469-858">Configure server options</span></span>

<span data-ttu-id="c1469-859">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c1469-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c1469-860">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-860">Option</span></span> | <span data-ttu-id="c1469-861">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-861">Default Value</span></span> | <span data-ttu-id="c1469-862">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c1469-863">30 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-863">30 seconds</span></span> | <span data-ttu-id="c1469-864">如果客户端未收到消息 (在此时间间隔内包含 keep-alive) ，服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c1469-865">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c1469-866">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c1469-867">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-867">15 seconds</span></span> | <span data-ttu-id="c1469-868">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c1469-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c1469-869">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-870">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-871">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-871">15 seconds</span></span> | <span data-ttu-id="c1469-872">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c1469-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c1469-873">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c1469-874">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c1469-875">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c1469-875">All installed protocols</span></span> | <span data-ttu-id="c1469-876">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-876">Protocols supported by this hub.</span></span> <span data-ttu-id="c1469-877">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c1469-878">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c1469-879">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c1469-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c1469-880">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c1469-881">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c1469-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c1469-882">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c1469-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c1469-883">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c1469-884">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c1469-885">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c1469-886">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-886">Option</span></span> | <span data-ttu-id="c1469-887">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-887">Default Value</span></span> | <span data-ttu-id="c1469-888">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c1469-889">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-889">32 KB</span></span> | <span data-ttu-id="c1469-890">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c1469-891">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="c1469-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c1469-892">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c1469-893">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c1469-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c1469-894">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-894">32 KB</span></span> | <span data-ttu-id="c1469-895">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c1469-896">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="c1469-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c1469-897">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c1469-897">All Transports are enabled.</span></span> | <span data-ttu-id="c1469-898">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c1469-899">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-899">See below.</span></span> | <span data-ttu-id="c1469-900">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c1469-901">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-901">See below.</span></span> | <span data-ttu-id="c1469-902">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c1469-903">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c1469-904">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-904">Option</span></span> | <span data-ttu-id="c1469-905">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-905">Default Value</span></span> | <span data-ttu-id="c1469-906">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c1469-907">90秒</span><span class="sxs-lookup"><span data-stu-id="c1469-907">90 seconds</span></span> | <span data-ttu-id="c1469-908">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c1469-909">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c1469-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c1469-910">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c1469-911">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-911">Option</span></span> | <span data-ttu-id="c1469-912">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-912">Default Value</span></span> | <span data-ttu-id="c1469-913">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c1469-914">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-914">5 seconds</span></span> | <span data-ttu-id="c1469-915">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c1469-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c1469-916">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c1469-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c1469-917">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c1469-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c1469-918">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c1469-918">Configure client options</span></span>

<span data-ttu-id="c1469-919">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c1469-920">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c1469-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c1469-921">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c1469-921">Configure logging</span></span>

<span data-ttu-id="c1469-922">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c1469-923">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c1469-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c1469-924">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-925">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c1469-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c1469-926">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="c1469-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c1469-927">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c1469-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c1469-928">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c1469-929">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c1469-930">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c1469-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c1469-931">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c1469-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c1469-932">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c1469-933">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c1469-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c1469-934">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c1469-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c1469-935">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c1469-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c1469-936">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c1469-937">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c1469-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c1469-938">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c1469-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c1469-939">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c1469-939">Configure allowed transports</span></span>

<span data-ttu-id="c1469-940">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c1469-941">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c1469-942">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-942">All transports are enabled by default.</span></span>

<span data-ttu-id="c1469-943">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c1469-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c1469-944">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c1469-945">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c1469-946">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c1469-946">Configure bearer authentication</span></span>

<span data-ttu-id="c1469-947">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c1469-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c1469-948">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c1469-949">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在服务器发送的事件和 websocket 请求) 中应用标 (头的能力。</span><span class="sxs-lookup"><span data-stu-id="c1469-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c1469-950">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c1469-951">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c1469-952">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c1469-953">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c1469-954">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c1469-955">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c1469-956">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c1469-957">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c1469-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-958">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-959">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-959">Option</span></span> | <span data-ttu-id="c1469-960">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-960">Default value</span></span> | <span data-ttu-id="c1469-961">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c1469-962">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-963">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-963">Timeout for server activity.</span></span> <span data-ttu-id="c1469-964">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-965">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-966">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c1469-967">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-967">15 seconds</span></span> | <span data-ttu-id="c1469-968">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-969">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-970">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-971">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-972">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-972">15 seconds</span></span> | <span data-ttu-id="c1469-973">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-974">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-975">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c1469-976">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c1469-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c1469-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-978">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-978">Option</span></span> | <span data-ttu-id="c1469-979">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-979">Default value</span></span> | <span data-ttu-id="c1469-980">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c1469-981">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-982">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-982">Timeout for server activity.</span></span> <span data-ttu-id="c1469-983">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c1469-984">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-985">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c1469-986">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c1469-987">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-988">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-989">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-990">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-990">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-991">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-991">Option</span></span> | <span data-ttu-id="c1469-992">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-992">Default value</span></span> | <span data-ttu-id="c1469-993">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c1469-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c1469-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c1469-995">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-996">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-996">Timeout for server activity.</span></span> <span data-ttu-id="c1469-997">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-998">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-999">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c1469-1000">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1000">15 seconds</span></span> | <span data-ttu-id="c1469-1001">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-1002">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-1003">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-1004">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c1469-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c1469-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c1469-1006">15秒 (15000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c1469-1007">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c1469-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c1469-1008">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c1469-1009">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c1469-1010">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1010">Configure additional options</span></span>

<span data-ttu-id="c1469-1011">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-1013">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1013">.NET Option</span></span> |  <span data-ttu-id="c1469-1014">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1014">Default value</span></span> | <span data-ttu-id="c1469-1015">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c1469-1016">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c1469-1017">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-1018">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-1019">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c1469-1020">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1020">Empty</span></span> | <span data-ttu-id="c1469-1021">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c1469-1022">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1022">Empty</span></span> | <span data-ttu-id="c1469-1023">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c1469-1024">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1024">Empty</span></span> | <span data-ttu-id="c1469-1025">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c1469-1026">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1026">5 seconds</span></span> | <span data-ttu-id="c1469-1027">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c1469-1027">WebSockets only.</span></span> <span data-ttu-id="c1469-1028">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c1469-1029">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c1469-1030">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1030">Empty</span></span> | <span data-ttu-id="c1469-1031">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c1469-1032">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c1469-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c1469-1033">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="c1469-1034">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c1469-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c1469-1035">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c1469-1036">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c1469-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c1469-1037">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c1469-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c1469-1038">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c1469-1039">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c1469-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c1469-1040">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c1469-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c1469-1041">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c1469-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-1043">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1043">JavaScript Option</span></span> | <span data-ttu-id="c1469-1044">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1044">Default Value</span></span> | <span data-ttu-id="c1469-1045">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c1469-1046">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c1469-1047">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="c1469-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c1469-1048">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-1049">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-1050">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-1051">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-1052">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1052">Java Option</span></span> | <span data-ttu-id="c1469-1053">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1053">Default Value</span></span> | <span data-ttu-id="c1469-1054">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c1469-1055">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c1469-1056">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-1057">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-1058">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c1469-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c1469-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c1469-1060">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1060">Empty</span></span> | <span data-ttu-id="c1469-1061">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c1469-1062">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c1469-1063">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c1469-1064">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c1469-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c1469-1065">其他资源</span><span class="sxs-lookup"><span data-stu-id="c1469-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c1469-1066">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c1469-1067">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/) 和 [MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c1469-1068">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c1469-1069">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法[添加 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c1469-1070">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c1469-1071">该对象的 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c1469-1072">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="c1469-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c1469-1073">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c1469-1074">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c1469-1075">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c1469-1076">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c1469-1077">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1077">MessagePack serialization options</span></span>

<span data-ttu-id="c1469-1078">可以通过向 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c1469-1079">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-1080">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c1469-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c1469-1081">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1081">Configure server options</span></span>

<span data-ttu-id="c1469-1082">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c1469-1083">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1083">Option</span></span> | <span data-ttu-id="c1469-1084">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1084">Default Value</span></span> | <span data-ttu-id="c1469-1085">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="c1469-1086">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1086">15 seconds</span></span> | <span data-ttu-id="c1469-1087">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c1469-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c1469-1088">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-1089">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c1469-1090">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1090">15 seconds</span></span> | <span data-ttu-id="c1469-1091">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c1469-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c1469-1092">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c1469-1093">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c1469-1094">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c1469-1094">All installed protocols</span></span> | <span data-ttu-id="c1469-1095">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="c1469-1096">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c1469-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c1469-1097">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c1469-1098">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c1469-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c1469-1099">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c1469-1100">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c1469-1101">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c1469-1102">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c1469-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c1469-1103">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c1469-1104">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c1469-1105">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1105">Option</span></span> | <span data-ttu-id="c1469-1106">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1106">Default Value</span></span> | <span data-ttu-id="c1469-1107">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c1469-1108">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-1108">32 KB</span></span> | <span data-ttu-id="c1469-1109">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c1469-1110">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="c1469-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c1469-1111">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c1469-1112">用于确定客户端是否有权连接到集线器的 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c1469-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c1469-1113">32 KB</span><span class="sxs-lookup"><span data-stu-id="c1469-1113">32 KB</span></span> | <span data-ttu-id="c1469-1114">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c1469-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c1469-1115">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="c1469-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c1469-1116">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c1469-1116">All Transports are enabled.</span></span> | <span data-ttu-id="c1469-1117">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c1469-1118">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-1118">See below.</span></span> | <span data-ttu-id="c1469-1119">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c1469-1120">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c1469-1120">See below.</span></span> | <span data-ttu-id="c1469-1121">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c1469-1122">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c1469-1123">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1123">Option</span></span> | <span data-ttu-id="c1469-1124">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1124">Default Value</span></span> | <span data-ttu-id="c1469-1125">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c1469-1126">90秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1126">90 seconds</span></span> | <span data-ttu-id="c1469-1127">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c1469-1128">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c1469-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c1469-1129">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c1469-1130">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1130">Option</span></span> | <span data-ttu-id="c1469-1131">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1131">Default Value</span></span> | <span data-ttu-id="c1469-1132">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c1469-1133">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1133">5 seconds</span></span> | <span data-ttu-id="c1469-1134">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c1469-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c1469-1135">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c1469-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c1469-1136">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c1469-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c1469-1137">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1137">Configure client options</span></span>

<span data-ttu-id="c1469-1138">可以在 `HubConnectionBuilder` .net 和 JavaScript 客户端) 中可用的类型 (上配置客户端选项。</span><span class="sxs-lookup"><span data-stu-id="c1469-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c1469-1139">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c1469-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c1469-1140">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c1469-1140">Configure logging</span></span>

<span data-ttu-id="c1469-1141">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c1469-1142">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c1469-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c1469-1143">有关详细信息，请参阅 [ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index) 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c1469-1144">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c1469-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c1469-1145">有关完整列表，请参阅文档的 [内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers) 部分。</span><span class="sxs-lookup"><span data-stu-id="c1469-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c1469-1146">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c1469-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c1469-1147">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c1469-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c1469-1148">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c1469-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c1469-1149">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c1469-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c1469-1150">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c1469-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c1469-1151">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c1469-1152">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c1469-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c1469-1153">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c1469-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c1469-1154">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c1469-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c1469-1155">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c1469-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c1469-1156">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c1469-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c1469-1157">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c1469-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c1469-1158">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c1469-1158">Configure allowed transports</span></span>

<span data-ttu-id="c1469-1159">使用的传输 SignalR 可在 `WithUrl` JavaScript) 的调用 (中进行配置 `withUrl` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c1469-1160">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c1469-1161">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c1469-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="c1469-1162">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c1469-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c1469-1163">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c1469-1164">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c1469-1164">Configure bearer authentication</span></span>

<span data-ttu-id="c1469-1165">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` `accessTokenFactory` JavaScript) 中 (选项来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c1469-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c1469-1166">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入 (使用 `Authorization`) 类型的标头 `Bearer` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c1469-1167">在 JavaScript 客户端中，访问令牌用作持有者令牌， **但** 在某些情况下，浏览器 api 会限制在服务器发送的事件和 websocket 请求) 中应用标 (头的能力。</span><span class="sxs-lookup"><span data-stu-id="c1469-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c1469-1168">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c1469-1169">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c1469-1170">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c1469-1171">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c1469-1172">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c1469-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c1469-1173">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c1469-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c1469-1174">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c1469-1175">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c1469-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-1177">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1177">Option</span></span> | <span data-ttu-id="c1469-1178">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1178">Default value</span></span> | <span data-ttu-id="c1469-1179">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c1469-1180">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-1181">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-1181">Timeout for server activity.</span></span> <span data-ttu-id="c1469-1182">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-1183">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-1184">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c1469-1185">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1185">15 seconds</span></span> | <span data-ttu-id="c1469-1186">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-1187">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并 `Closed` `onclose` 在 JavaScript) 中触发事件 (。</span><span class="sxs-lookup"><span data-stu-id="c1469-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c1469-1188">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-1189">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="c1469-1190">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c1469-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c1469-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-1192">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1192">Option</span></span> | <span data-ttu-id="c1469-1193">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1193">Default value</span></span> | <span data-ttu-id="c1469-1194">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c1469-1195">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-1196">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-1196">Timeout for server activity.</span></span> <span data-ttu-id="c1469-1197">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c1469-1198">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-1199">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-1200">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-1201">选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1201">Option</span></span> | <span data-ttu-id="c1469-1202">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1202">Default value</span></span> | <span data-ttu-id="c1469-1203">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c1469-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c1469-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c1469-1205">30秒 (30000 毫秒) </span><span class="sxs-lookup"><span data-stu-id="c1469-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c1469-1206">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c1469-1206">Timeout for server activity.</span></span> <span data-ttu-id="c1469-1207">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-1208">此值必须足够大，以便从服务器发送 ping 消息 **，并** 在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c1469-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c1469-1209">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c1469-1210">15 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1210">15 seconds</span></span> | <span data-ttu-id="c1469-1211">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="c1469-1212">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c1469-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c1469-1213">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c1469-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c1469-1214">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c1469-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c1469-1215">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1215">Configure additional options</span></span>

<span data-ttu-id="c1469-1216">可以在中的 `WithUrl` `withUrl` JavaScript) 方法 (上 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c1469-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c1469-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="c1469-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c1469-1218">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1218">.NET Option</span></span> |  <span data-ttu-id="c1469-1219">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1219">Default value</span></span> | <span data-ttu-id="c1469-1220">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c1469-1221">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c1469-1222">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-1223">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-1224">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c1469-1225">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1225">Empty</span></span> | <span data-ttu-id="c1469-1226">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c1469-1227">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1227">Empty</span></span> | <span data-ttu-id="c1469-1228">cookie要随每个 http 请求一起发送的 http 的集合。</span><span class="sxs-lookup"><span data-stu-id="c1469-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c1469-1229">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1229">Empty</span></span> | <span data-ttu-id="c1469-1230">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c1469-1231">5 秒</span><span class="sxs-lookup"><span data-stu-id="c1469-1231">5 seconds</span></span> | <span data-ttu-id="c1469-1232">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c1469-1232">WebSockets only.</span></span> <span data-ttu-id="c1469-1233">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c1469-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c1469-1234">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c1469-1235">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1235">Empty</span></span> | <span data-ttu-id="c1469-1236">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c1469-1237">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c1469-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c1469-1238">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c1469-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="c1469-1239">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c1469-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c1469-1240">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c1469-1241">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，所配置的选项 (如 Cookie s 和标头) 将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c1469-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c1469-1242">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c1469-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c1469-1243">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c1469-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c1469-1244">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c1469-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c1469-1245">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c1469-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c1469-1246">接收可用于配置选项的 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 实例。</span><span class="sxs-lookup"><span data-stu-id="c1469-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c1469-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c1469-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c1469-1248">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1248">JavaScript Option</span></span> | <span data-ttu-id="c1469-1249">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1249">Default Value</span></span> | <span data-ttu-id="c1469-1250">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c1469-1251">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="c1469-1252">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="c1469-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c1469-1253">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-1254">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-1255">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c1469-1256">Java</span><span class="sxs-lookup"><span data-stu-id="c1469-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="c1469-1257">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c1469-1257">Java Option</span></span> | <span data-ttu-id="c1469-1258">默认值</span><span class="sxs-lookup"><span data-stu-id="c1469-1258">Default Value</span></span> | <span data-ttu-id="c1469-1259">说明</span><span class="sxs-lookup"><span data-stu-id="c1469-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c1469-1260">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c1469-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c1469-1261">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c1469-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c1469-1262">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c1469-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c1469-1263">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c1469-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c1469-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c1469-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c1469-1265">空</span><span class="sxs-lookup"><span data-stu-id="c1469-1265">Empty</span></span> | <span data-ttu-id="c1469-1266">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c1469-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c1469-1267">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c1469-1268">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c1469-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c1469-1269">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c1469-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c1469-1270">其他资源</span><span class="sxs-lookup"><span data-stu-id="c1469-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
