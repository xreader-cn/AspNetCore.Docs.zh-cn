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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c711c2163908e3fdd20e3bb497f333ebd495d921
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406831"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="14533-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="14533-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14533-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14533-105">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14533-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="14533-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14533-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="14533-108">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14533-109">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="14533-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14533-110">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14533-111">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="14533-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="14533-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14533-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="14533-113">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="14533-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14533-114">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14533-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="14533-116">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="14533-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="14533-117">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="14533-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14533-118">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-118">MessagePack serialization options</span></span>

<span data-ttu-id="14533-119">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14533-120">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="14533-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-121">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14533-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="14533-122">Configure server options</span></span>

<span data-ttu-id="14533-123">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="14533-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14533-124">选项</span><span class="sxs-lookup"><span data-stu-id="14533-124">Option</span></span> | <span data-ttu-id="14533-125">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-125">Default Value</span></span> | <span data-ttu-id="14533-126">说明</span><span class="sxs-lookup"><span data-stu-id="14533-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="14533-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="14533-127">30 seconds</span></span> | <span data-ttu-id="14533-128">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="14533-129">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="14533-130">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="14533-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-131">15 seconds</span></span> | <span data-ttu-id="14533-132">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="14533-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14533-133">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-134">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-135">15 seconds</span></span> | <span data-ttu-id="14533-136">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="14533-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14533-137">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="14533-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14533-138">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14533-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="14533-139">All installed protocols</span></span> | <span data-ttu-id="14533-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="14533-140">Protocols supported by this hub.</span></span> <span data-ttu-id="14533-141">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="14533-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14533-142">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14533-143">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="14533-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="14533-144">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="14533-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="14533-145">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="14533-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="14533-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-146">32 KB</span></span> | <span data-ttu-id="14533-147">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="14533-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="14533-148">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14533-149">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="14533-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14533-150">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="14533-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14533-151">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="14533-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14533-152">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="14533-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="14533-153">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="14533-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14533-154">选项</span><span class="sxs-lookup"><span data-stu-id="14533-154">Option</span></span> | <span data-ttu-id="14533-155">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-155">Default Value</span></span> | <span data-ttu-id="14533-156">说明</span><span class="sxs-lookup"><span data-stu-id="14533-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14533-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-157">32 KB</span></span> | <span data-ttu-id="14533-158">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="14533-159">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="14533-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14533-160">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="14533-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14533-161">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="14533-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14533-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-162">32 KB</span></span> | <span data-ttu-id="14533-163">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="14533-164">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="14533-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14533-165">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="14533-165">All Transports are enabled.</span></span> | <span data-ttu-id="14533-166">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14533-167">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-167">See below.</span></span> | <span data-ttu-id="14533-168">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14533-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-169">See below.</span></span> | <span data-ttu-id="14533-170">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="14533-171">0</span><span class="sxs-lookup"><span data-stu-id="14533-171">0</span></span> | <span data-ttu-id="14533-172">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="14533-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="14533-173">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="14533-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="14533-174">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="14533-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14533-175">选项</span><span class="sxs-lookup"><span data-stu-id="14533-175">Option</span></span> | <span data-ttu-id="14533-176">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-176">Default Value</span></span> | <span data-ttu-id="14533-177">说明</span><span class="sxs-lookup"><span data-stu-id="14533-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14533-178">90秒</span><span class="sxs-lookup"><span data-stu-id="14533-178">90 seconds</span></span> | <span data-ttu-id="14533-179">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14533-180">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="14533-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14533-181">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="14533-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14533-182">选项</span><span class="sxs-lookup"><span data-stu-id="14533-182">Option</span></span> | <span data-ttu-id="14533-183">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-183">Default Value</span></span> | <span data-ttu-id="14533-184">说明</span><span class="sxs-lookup"><span data-stu-id="14533-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14533-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-185">5 seconds</span></span> | <span data-ttu-id="14533-186">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="14533-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14533-187">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="14533-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14533-188">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="14533-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14533-189">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="14533-189">Configure client options</span></span>

<span data-ttu-id="14533-190">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="14533-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14533-191">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="14533-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14533-192">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="14533-192">Configure logging</span></span>

<span data-ttu-id="14533-193">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14533-194">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="14533-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14533-195">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="14533-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-196">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="14533-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14533-197">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="14533-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14533-198">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="14533-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14533-199">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14533-200">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="14533-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14533-201">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="14533-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14533-202">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="14533-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="14533-203">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="14533-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="14533-204">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="14533-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="14533-205">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="14533-205">The following table lists the available log levels.</span></span> <span data-ttu-id="14533-206">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="14533-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="14533-207">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="14533-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="14533-208">String</span><span class="sxs-lookup"><span data-stu-id="14533-208">String</span></span>                      | <span data-ttu-id="14533-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="14533-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="14533-210">`info` **或** `information`</span><span class="sxs-lookup"><span data-stu-id="14533-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="14533-211">`warn` **或** `warning`</span><span class="sxs-lookup"><span data-stu-id="14533-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="14533-212">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14533-213">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="14533-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14533-214">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="14533-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14533-215">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="14533-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14533-216">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14533-217">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="14533-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14533-218">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="14533-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14533-219">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="14533-219">Configure allowed transports</span></span>

<span data-ttu-id="14533-220">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="14533-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14533-221">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14533-222">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="14533-222">All transports are enabled by default.</span></span>

<span data-ttu-id="14533-223">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="14533-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14533-224">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="14533-225">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="14533-226">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="14533-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="14533-227">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="14533-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14533-228">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="14533-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14533-229">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="14533-229">Configure bearer authentication</span></span>

<span data-ttu-id="14533-230">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="14533-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14533-231">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="14533-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14533-232">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="14533-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14533-233">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="14533-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14533-234">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14533-235">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="14533-236">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14533-237">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14533-238">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14533-239">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="14533-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14533-240">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="14533-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-241">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-242">选项</span><span class="sxs-lookup"><span data-stu-id="14533-242">Option</span></span> | <span data-ttu-id="14533-243">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-243">Default value</span></span> | <span data-ttu-id="14533-244">说明</span><span class="sxs-lookup"><span data-stu-id="14533-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14533-245">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-246">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-246">Timeout for server activity.</span></span> <span data-ttu-id="14533-247">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-248">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-249">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14533-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-250">15 seconds</span></span> | <span data-ttu-id="14533-251">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-252">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-253">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-254">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-255">15 seconds</span></span> | <span data-ttu-id="14533-256">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-257">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-258">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="14533-259">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="14533-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14533-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-261">选项</span><span class="sxs-lookup"><span data-stu-id="14533-261">Option</span></span> | <span data-ttu-id="14533-262">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-262">Default value</span></span> | <span data-ttu-id="14533-263">说明</span><span class="sxs-lookup"><span data-stu-id="14533-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14533-264">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-265">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-265">Timeout for server activity.</span></span> <span data-ttu-id="14533-266">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14533-267">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-268">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="14533-269">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14533-270">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-271">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-272">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-273">Java</span><span class="sxs-lookup"><span data-stu-id="14533-273">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-274">选项</span><span class="sxs-lookup"><span data-stu-id="14533-274">Option</span></span> | <span data-ttu-id="14533-275">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-275">Default value</span></span> | <span data-ttu-id="14533-276">说明</span><span class="sxs-lookup"><span data-stu-id="14533-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14533-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14533-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14533-278">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-279">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-279">Timeout for server activity.</span></span> <span data-ttu-id="14533-280">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-281">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-282">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14533-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-283">15 seconds</span></span> | <span data-ttu-id="14533-284">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-285">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-286">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-287">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="14533-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="14533-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="14533-289">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14533-290">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-291">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-292">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14533-293">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="14533-293">Configure additional options</span></span>

<span data-ttu-id="14533-294">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="14533-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-295">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-296">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="14533-296">.NET Option</span></span> |  <span data-ttu-id="14533-297">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-297">Default value</span></span> | <span data-ttu-id="14533-298">说明</span><span class="sxs-lookup"><span data-stu-id="14533-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14533-299">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14533-300">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-301">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-302">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14533-303">空</span><span class="sxs-lookup"><span data-stu-id="14533-303">Empty</span></span> | <span data-ttu-id="14533-304">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14533-305">空</span><span class="sxs-lookup"><span data-stu-id="14533-305">Empty</span></span> | <span data-ttu-id="14533-306">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14533-307">空</span><span class="sxs-lookup"><span data-stu-id="14533-307">Empty</span></span> | <span data-ttu-id="14533-308">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14533-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-309">5 seconds</span></span> | <span data-ttu-id="14533-310">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="14533-310">WebSockets only.</span></span> <span data-ttu-id="14533-311">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14533-312">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14533-313">空</span><span class="sxs-lookup"><span data-stu-id="14533-313">Empty</span></span> | <span data-ttu-id="14533-314">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14533-315">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="14533-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14533-316">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="14533-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="14533-317">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="14533-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14533-318">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="14533-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14533-319">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="14533-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14533-320">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="14533-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14533-321">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14533-322">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="14533-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14533-323">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="14533-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14533-324">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="14533-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14533-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-326">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="14533-326">JavaScript Option</span></span> | <span data-ttu-id="14533-327">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-327">Default Value</span></span> | <span data-ttu-id="14533-328">说明</span><span class="sxs-lookup"><span data-stu-id="14533-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14533-329">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="14533-330">每个 HTTP 请求发送的标头的字典。</span><span class="sxs-lookup"><span data-stu-id="14533-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="14533-331">在浏览器中发送标头对于 Websocket 或流不起作用 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。</span><span class="sxs-lookup"><span data-stu-id="14533-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="14533-332">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="14533-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14533-333">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-334">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-335">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="14533-336">指定是否将凭据与 CORS 请求一起发送。</span><span class="sxs-lookup"><span data-stu-id="14533-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="14533-337">Azure App Service 使用 cookie 进行粘滞会话，并且需要此选项才能正常工作。</span><span class="sxs-lookup"><span data-stu-id="14533-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="14533-338">有关 CORS 的详细信息 SignalR ，请参阅 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="14533-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-339">Java</span><span class="sxs-lookup"><span data-stu-id="14533-339">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-340">Java 选项</span><span class="sxs-lookup"><span data-stu-id="14533-340">Java Option</span></span> | <span data-ttu-id="14533-341">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-341">Default Value</span></span> | <span data-ttu-id="14533-342">说明</span><span class="sxs-lookup"><span data-stu-id="14533-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14533-343">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14533-344">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-345">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-346">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14533-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14533-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14533-348">空</span><span class="sxs-lookup"><span data-stu-id="14533-348">Empty</span></span> | <span data-ttu-id="14533-349">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14533-350">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14533-351">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14533-352">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14533-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14533-353">其他资源</span><span class="sxs-lookup"><span data-stu-id="14533-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14533-354">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14533-355">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14533-356">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="14533-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14533-357">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="14533-358">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14533-359">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="14533-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14533-360">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14533-361">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="14533-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="14533-362">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14533-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="14533-363">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="14533-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14533-364">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14533-365">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="14533-366">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="14533-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="14533-367">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="14533-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14533-368">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-368">MessagePack serialization options</span></span>

<span data-ttu-id="14533-369">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14533-370">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="14533-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-371">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14533-372">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="14533-372">Configure server options</span></span>

<span data-ttu-id="14533-373">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="14533-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14533-374">选项</span><span class="sxs-lookup"><span data-stu-id="14533-374">Option</span></span> | <span data-ttu-id="14533-375">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-375">Default Value</span></span> | <span data-ttu-id="14533-376">说明</span><span class="sxs-lookup"><span data-stu-id="14533-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="14533-377">30 秒</span><span class="sxs-lookup"><span data-stu-id="14533-377">30 seconds</span></span> | <span data-ttu-id="14533-378">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="14533-379">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="14533-380">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="14533-381">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-381">15 seconds</span></span> | <span data-ttu-id="14533-382">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="14533-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14533-383">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-384">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-385">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-385">15 seconds</span></span> | <span data-ttu-id="14533-386">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="14533-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14533-387">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="14533-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14533-388">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14533-389">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="14533-389">All installed protocols</span></span> | <span data-ttu-id="14533-390">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="14533-390">Protocols supported by this hub.</span></span> <span data-ttu-id="14533-391">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="14533-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14533-392">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14533-393">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="14533-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="14533-394">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="14533-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="14533-395">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="14533-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="14533-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-396">32 KB</span></span> | <span data-ttu-id="14533-397">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="14533-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="14533-398">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14533-399">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="14533-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14533-400">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="14533-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14533-401">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="14533-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14533-402">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="14533-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="14533-403">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="14533-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14533-404">选项</span><span class="sxs-lookup"><span data-stu-id="14533-404">Option</span></span> | <span data-ttu-id="14533-405">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-405">Default Value</span></span> | <span data-ttu-id="14533-406">描述</span><span class="sxs-lookup"><span data-stu-id="14533-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14533-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-407">32 KB</span></span> | <span data-ttu-id="14533-408">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="14533-409">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="14533-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14533-410">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="14533-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14533-411">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="14533-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14533-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-412">32 KB</span></span> | <span data-ttu-id="14533-413">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="14533-414">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="14533-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14533-415">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="14533-415">All Transports are enabled.</span></span> | <span data-ttu-id="14533-416">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14533-417">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-417">See below.</span></span> | <span data-ttu-id="14533-418">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14533-419">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-419">See below.</span></span> | <span data-ttu-id="14533-420">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="14533-421">0</span><span class="sxs-lookup"><span data-stu-id="14533-421">0</span></span> | <span data-ttu-id="14533-422">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="14533-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="14533-423">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="14533-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="14533-424">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="14533-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14533-425">选项</span><span class="sxs-lookup"><span data-stu-id="14533-425">Option</span></span> | <span data-ttu-id="14533-426">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-426">Default Value</span></span> | <span data-ttu-id="14533-427">描述</span><span class="sxs-lookup"><span data-stu-id="14533-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14533-428">90秒</span><span class="sxs-lookup"><span data-stu-id="14533-428">90 seconds</span></span> | <span data-ttu-id="14533-429">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14533-430">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="14533-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14533-431">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="14533-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14533-432">选项</span><span class="sxs-lookup"><span data-stu-id="14533-432">Option</span></span> | <span data-ttu-id="14533-433">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-433">Default Value</span></span> | <span data-ttu-id="14533-434">描述</span><span class="sxs-lookup"><span data-stu-id="14533-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14533-435">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-435">5 seconds</span></span> | <span data-ttu-id="14533-436">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="14533-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14533-437">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="14533-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14533-438">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="14533-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14533-439">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="14533-439">Configure client options</span></span>

<span data-ttu-id="14533-440">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="14533-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14533-441">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="14533-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14533-442">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="14533-442">Configure logging</span></span>

<span data-ttu-id="14533-443">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14533-444">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="14533-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14533-445">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="14533-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-446">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="14533-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14533-447">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="14533-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14533-448">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="14533-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14533-449">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14533-450">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="14533-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14533-451">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="14533-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14533-452">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="14533-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="14533-453">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="14533-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="14533-454">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="14533-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="14533-455">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="14533-455">The following table lists the available log levels.</span></span> <span data-ttu-id="14533-456">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="14533-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="14533-457">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="14533-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="14533-458">String</span><span class="sxs-lookup"><span data-stu-id="14533-458">String</span></span>                      | <span data-ttu-id="14533-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="14533-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="14533-460">`info` **或** `information`</span><span class="sxs-lookup"><span data-stu-id="14533-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="14533-461">`warn` **或** `warning`</span><span class="sxs-lookup"><span data-stu-id="14533-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="14533-462">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14533-463">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="14533-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14533-464">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="14533-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14533-465">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="14533-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14533-466">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14533-467">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="14533-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14533-468">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="14533-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14533-469">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="14533-469">Configure allowed transports</span></span>

<span data-ttu-id="14533-470">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="14533-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14533-471">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14533-472">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="14533-472">All transports are enabled by default.</span></span>

<span data-ttu-id="14533-473">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="14533-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14533-474">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="14533-475">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="14533-476">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="14533-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="14533-477">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="14533-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14533-478">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="14533-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14533-479">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="14533-479">Configure bearer authentication</span></span>

<span data-ttu-id="14533-480">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="14533-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14533-481">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="14533-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14533-482">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="14533-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14533-483">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="14533-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14533-484">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14533-485">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="14533-486">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14533-487">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14533-488">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14533-489">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="14533-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14533-490">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="14533-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-491">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-492">选项</span><span class="sxs-lookup"><span data-stu-id="14533-492">Option</span></span> | <span data-ttu-id="14533-493">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-493">Default value</span></span> | <span data-ttu-id="14533-494">说明</span><span class="sxs-lookup"><span data-stu-id="14533-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14533-495">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-496">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-496">Timeout for server activity.</span></span> <span data-ttu-id="14533-497">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-498">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-499">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14533-500">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-500">15 seconds</span></span> | <span data-ttu-id="14533-501">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-502">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-503">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-504">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-505">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-505">15 seconds</span></span> | <span data-ttu-id="14533-506">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-507">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-508">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="14533-509">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="14533-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14533-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-511">选项</span><span class="sxs-lookup"><span data-stu-id="14533-511">Option</span></span> | <span data-ttu-id="14533-512">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-512">Default value</span></span> | <span data-ttu-id="14533-513">说明</span><span class="sxs-lookup"><span data-stu-id="14533-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14533-514">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-515">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-515">Timeout for server activity.</span></span> <span data-ttu-id="14533-516">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14533-517">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-518">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="14533-519">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14533-520">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-521">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-522">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-523">Java</span><span class="sxs-lookup"><span data-stu-id="14533-523">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-524">选项</span><span class="sxs-lookup"><span data-stu-id="14533-524">Option</span></span> | <span data-ttu-id="14533-525">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-525">Default value</span></span> | <span data-ttu-id="14533-526">说明</span><span class="sxs-lookup"><span data-stu-id="14533-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14533-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14533-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14533-528">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-529">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-529">Timeout for server activity.</span></span> <span data-ttu-id="14533-530">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-531">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-532">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14533-533">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-533">15 seconds</span></span> | <span data-ttu-id="14533-534">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-535">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-536">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-537">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="14533-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="14533-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="14533-539">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14533-540">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-541">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-542">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14533-543">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="14533-543">Configure additional options</span></span>

<span data-ttu-id="14533-544">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="14533-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-545">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-546">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="14533-546">.NET Option</span></span> |  <span data-ttu-id="14533-547">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-547">Default value</span></span> | <span data-ttu-id="14533-548">说明</span><span class="sxs-lookup"><span data-stu-id="14533-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14533-549">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14533-550">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-551">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-552">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14533-553">空</span><span class="sxs-lookup"><span data-stu-id="14533-553">Empty</span></span> | <span data-ttu-id="14533-554">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14533-555">空</span><span class="sxs-lookup"><span data-stu-id="14533-555">Empty</span></span> | <span data-ttu-id="14533-556">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14533-557">空</span><span class="sxs-lookup"><span data-stu-id="14533-557">Empty</span></span> | <span data-ttu-id="14533-558">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14533-559">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-559">5 seconds</span></span> | <span data-ttu-id="14533-560">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="14533-560">WebSockets only.</span></span> <span data-ttu-id="14533-561">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14533-562">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14533-563">空</span><span class="sxs-lookup"><span data-stu-id="14533-563">Empty</span></span> | <span data-ttu-id="14533-564">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14533-565">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="14533-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14533-566">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="14533-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="14533-567">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="14533-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14533-568">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="14533-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14533-569">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="14533-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14533-570">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="14533-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14533-571">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14533-572">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="14533-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14533-573">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="14533-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14533-574">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="14533-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14533-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-576">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="14533-576">JavaScript Option</span></span> | <span data-ttu-id="14533-577">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-577">Default Value</span></span> | <span data-ttu-id="14533-578">描述</span><span class="sxs-lookup"><span data-stu-id="14533-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14533-579">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="14533-580">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="14533-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14533-581">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-582">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-583">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-584">Java</span><span class="sxs-lookup"><span data-stu-id="14533-584">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-585">Java 选项</span><span class="sxs-lookup"><span data-stu-id="14533-585">Java Option</span></span> | <span data-ttu-id="14533-586">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-586">Default Value</span></span> | <span data-ttu-id="14533-587">描述</span><span class="sxs-lookup"><span data-stu-id="14533-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14533-588">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14533-589">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-590">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-591">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14533-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14533-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14533-593">空</span><span class="sxs-lookup"><span data-stu-id="14533-593">Empty</span></span> | <span data-ttu-id="14533-594">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14533-595">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14533-596">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14533-597">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14533-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14533-598">其他资源</span><span class="sxs-lookup"><span data-stu-id="14533-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14533-599">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14533-600">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14533-601">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="14533-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14533-602">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="14533-603">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14533-604">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="14533-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14533-605">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14533-606">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="14533-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="14533-607">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14533-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="14533-608">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="14533-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14533-609">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14533-610">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="14533-611">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="14533-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="14533-612">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="14533-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14533-613">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-613">MessagePack serialization options</span></span>

<span data-ttu-id="14533-614">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14533-615">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="14533-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-616">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14533-617">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="14533-617">Configure server options</span></span>

<span data-ttu-id="14533-618">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="14533-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14533-619">选项</span><span class="sxs-lookup"><span data-stu-id="14533-619">Option</span></span> | <span data-ttu-id="14533-620">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-620">Default Value</span></span> | <span data-ttu-id="14533-621">说明</span><span class="sxs-lookup"><span data-stu-id="14533-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="14533-622">30 秒</span><span class="sxs-lookup"><span data-stu-id="14533-622">30 seconds</span></span> | <span data-ttu-id="14533-623">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="14533-624">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="14533-625">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="14533-626">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-626">15 seconds</span></span> | <span data-ttu-id="14533-627">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="14533-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14533-628">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-629">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-630">15 seconds</span></span> | <span data-ttu-id="14533-631">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="14533-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14533-632">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="14533-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14533-633">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14533-634">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="14533-634">All installed protocols</span></span> | <span data-ttu-id="14533-635">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="14533-635">Protocols supported by this hub.</span></span> <span data-ttu-id="14533-636">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="14533-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14533-637">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14533-638">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="14533-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="14533-639">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="14533-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="14533-640">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="14533-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="14533-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-641">32 KB</span></span> | <span data-ttu-id="14533-642">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="14533-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="14533-643">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14533-644">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="14533-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14533-645">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="14533-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14533-646">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="14533-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14533-647">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="14533-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="14533-648">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="14533-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14533-649">选项</span><span class="sxs-lookup"><span data-stu-id="14533-649">Option</span></span> | <span data-ttu-id="14533-650">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-650">Default Value</span></span> | <span data-ttu-id="14533-651">说明</span><span class="sxs-lookup"><span data-stu-id="14533-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14533-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-652">32 KB</span></span> | <span data-ttu-id="14533-653">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="14533-654">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="14533-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14533-655">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="14533-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14533-656">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="14533-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14533-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-657">32 KB</span></span> | <span data-ttu-id="14533-658">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="14533-659">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="14533-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14533-660">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="14533-660">All Transports are enabled.</span></span> | <span data-ttu-id="14533-661">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14533-662">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-662">See below.</span></span> | <span data-ttu-id="14533-663">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14533-664">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-664">See below.</span></span> | <span data-ttu-id="14533-665">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="14533-666">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="14533-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14533-667">选项</span><span class="sxs-lookup"><span data-stu-id="14533-667">Option</span></span> | <span data-ttu-id="14533-668">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-668">Default Value</span></span> | <span data-ttu-id="14533-669">说明</span><span class="sxs-lookup"><span data-stu-id="14533-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14533-670">90秒</span><span class="sxs-lookup"><span data-stu-id="14533-670">90 seconds</span></span> | <span data-ttu-id="14533-671">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14533-672">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="14533-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14533-673">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="14533-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14533-674">选项</span><span class="sxs-lookup"><span data-stu-id="14533-674">Option</span></span> | <span data-ttu-id="14533-675">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-675">Default Value</span></span> | <span data-ttu-id="14533-676">说明</span><span class="sxs-lookup"><span data-stu-id="14533-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14533-677">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-677">5 seconds</span></span> | <span data-ttu-id="14533-678">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="14533-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14533-679">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="14533-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14533-680">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="14533-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14533-681">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="14533-681">Configure client options</span></span>

<span data-ttu-id="14533-682">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="14533-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14533-683">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="14533-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14533-684">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="14533-684">Configure logging</span></span>

<span data-ttu-id="14533-685">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14533-686">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="14533-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14533-687">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="14533-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-688">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="14533-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14533-689">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="14533-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14533-690">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="14533-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14533-691">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14533-692">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="14533-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14533-693">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="14533-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14533-694">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="14533-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="14533-695">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="14533-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="14533-696">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="14533-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="14533-697">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="14533-697">The following table lists the available log levels.</span></span> <span data-ttu-id="14533-698">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="14533-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="14533-699">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="14533-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="14533-700">String</span><span class="sxs-lookup"><span data-stu-id="14533-700">String</span></span>                      | <span data-ttu-id="14533-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="14533-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="14533-702">`info` **或** `information`</span><span class="sxs-lookup"><span data-stu-id="14533-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="14533-703">`warn` **或** `warning`</span><span class="sxs-lookup"><span data-stu-id="14533-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="14533-704">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14533-705">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="14533-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14533-706">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="14533-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14533-707">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="14533-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14533-708">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14533-709">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="14533-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14533-710">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="14533-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14533-711">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="14533-711">Configure allowed transports</span></span>

<span data-ttu-id="14533-712">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="14533-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14533-713">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14533-714">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="14533-714">All transports are enabled by default.</span></span>

<span data-ttu-id="14533-715">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="14533-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14533-716">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="14533-717">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="14533-718">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="14533-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="14533-719">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="14533-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14533-720">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="14533-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14533-721">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="14533-721">Configure bearer authentication</span></span>

<span data-ttu-id="14533-722">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="14533-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14533-723">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="14533-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14533-724">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="14533-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14533-725">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="14533-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14533-726">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14533-727">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="14533-728">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14533-729">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14533-730">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14533-731">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="14533-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14533-732">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="14533-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-733">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-734">选项</span><span class="sxs-lookup"><span data-stu-id="14533-734">Option</span></span> | <span data-ttu-id="14533-735">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-735">Default value</span></span> | <span data-ttu-id="14533-736">说明</span><span class="sxs-lookup"><span data-stu-id="14533-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14533-737">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-738">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-738">Timeout for server activity.</span></span> <span data-ttu-id="14533-739">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-740">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-741">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14533-742">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-742">15 seconds</span></span> | <span data-ttu-id="14533-743">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-744">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-745">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-746">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-747">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-747">15 seconds</span></span> | <span data-ttu-id="14533-748">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-749">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-750">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="14533-751">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="14533-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14533-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-753">选项</span><span class="sxs-lookup"><span data-stu-id="14533-753">Option</span></span> | <span data-ttu-id="14533-754">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-754">Default value</span></span> | <span data-ttu-id="14533-755">说明</span><span class="sxs-lookup"><span data-stu-id="14533-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14533-756">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-757">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-757">Timeout for server activity.</span></span> <span data-ttu-id="14533-758">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14533-759">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-760">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="14533-761">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14533-762">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-763">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-764">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-765">Java</span><span class="sxs-lookup"><span data-stu-id="14533-765">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-766">选项</span><span class="sxs-lookup"><span data-stu-id="14533-766">Option</span></span> | <span data-ttu-id="14533-767">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-767">Default value</span></span> | <span data-ttu-id="14533-768">说明</span><span class="sxs-lookup"><span data-stu-id="14533-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14533-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14533-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14533-770">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-771">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-771">Timeout for server activity.</span></span> <span data-ttu-id="14533-772">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-773">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-774">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14533-775">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-775">15 seconds</span></span> | <span data-ttu-id="14533-776">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-777">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-778">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-779">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="14533-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="14533-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="14533-781">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14533-782">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-783">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-784">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14533-785">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="14533-785">Configure additional options</span></span>

<span data-ttu-id="14533-786">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="14533-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-787">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-788">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="14533-788">.NET Option</span></span> |  <span data-ttu-id="14533-789">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-789">Default value</span></span> | <span data-ttu-id="14533-790">说明</span><span class="sxs-lookup"><span data-stu-id="14533-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14533-791">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14533-792">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-793">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-794">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14533-795">空</span><span class="sxs-lookup"><span data-stu-id="14533-795">Empty</span></span> | <span data-ttu-id="14533-796">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14533-797">空</span><span class="sxs-lookup"><span data-stu-id="14533-797">Empty</span></span> | <span data-ttu-id="14533-798">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14533-799">空</span><span class="sxs-lookup"><span data-stu-id="14533-799">Empty</span></span> | <span data-ttu-id="14533-800">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14533-801">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-801">5 seconds</span></span> | <span data-ttu-id="14533-802">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="14533-802">WebSockets only.</span></span> <span data-ttu-id="14533-803">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14533-804">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14533-805">空</span><span class="sxs-lookup"><span data-stu-id="14533-805">Empty</span></span> | <span data-ttu-id="14533-806">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14533-807">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="14533-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14533-808">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="14533-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="14533-809">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="14533-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14533-810">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="14533-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14533-811">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="14533-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14533-812">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="14533-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14533-813">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14533-814">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="14533-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14533-815">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="14533-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14533-816">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="14533-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14533-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-818">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="14533-818">JavaScript Option</span></span> | <span data-ttu-id="14533-819">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-819">Default Value</span></span> | <span data-ttu-id="14533-820">说明</span><span class="sxs-lookup"><span data-stu-id="14533-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14533-821">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="14533-822">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="14533-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14533-823">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-824">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-825">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-826">Java</span><span class="sxs-lookup"><span data-stu-id="14533-826">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-827">Java 选项</span><span class="sxs-lookup"><span data-stu-id="14533-827">Java Option</span></span> | <span data-ttu-id="14533-828">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-828">Default Value</span></span> | <span data-ttu-id="14533-829">说明</span><span class="sxs-lookup"><span data-stu-id="14533-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14533-830">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14533-831">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-832">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-833">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14533-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14533-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14533-835">空</span><span class="sxs-lookup"><span data-stu-id="14533-835">Empty</span></span> | <span data-ttu-id="14533-836">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14533-837">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14533-838">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14533-839">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14533-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14533-840">其他资源</span><span class="sxs-lookup"><span data-stu-id="14533-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14533-841">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14533-842">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14533-843">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="14533-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14533-844">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="14533-845">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="14533-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14533-846">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14533-847">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="14533-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="14533-848">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14533-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="14533-849">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="14533-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14533-850">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14533-851">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14533-852">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-852">MessagePack serialization options</span></span>

<span data-ttu-id="14533-853">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14533-854">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="14533-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-855">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14533-856">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="14533-856">Configure server options</span></span>

<span data-ttu-id="14533-857">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="14533-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14533-858">选项</span><span class="sxs-lookup"><span data-stu-id="14533-858">Option</span></span> | <span data-ttu-id="14533-859">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-859">Default Value</span></span> | <span data-ttu-id="14533-860">说明</span><span class="sxs-lookup"><span data-stu-id="14533-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="14533-861">30 秒</span><span class="sxs-lookup"><span data-stu-id="14533-861">30 seconds</span></span> | <span data-ttu-id="14533-862">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="14533-863">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="14533-864">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="14533-865">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-865">15 seconds</span></span> | <span data-ttu-id="14533-866">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="14533-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14533-867">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-868">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-869">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-869">15 seconds</span></span> | <span data-ttu-id="14533-870">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="14533-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14533-871">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="14533-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14533-872">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14533-873">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="14533-873">All installed protocols</span></span> | <span data-ttu-id="14533-874">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="14533-874">Protocols supported by this hub.</span></span> <span data-ttu-id="14533-875">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="14533-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14533-876">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14533-877">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="14533-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="14533-878">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14533-879">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="14533-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14533-880">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="14533-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14533-881">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="14533-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14533-882">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="14533-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="14533-883">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="14533-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14533-884">选项</span><span class="sxs-lookup"><span data-stu-id="14533-884">Option</span></span> | <span data-ttu-id="14533-885">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-885">Default Value</span></span> | <span data-ttu-id="14533-886">说明</span><span class="sxs-lookup"><span data-stu-id="14533-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14533-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-887">32 KB</span></span> | <span data-ttu-id="14533-888">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="14533-889">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="14533-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14533-890">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="14533-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14533-891">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="14533-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14533-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-892">32 KB</span></span> | <span data-ttu-id="14533-893">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="14533-894">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="14533-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14533-895">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="14533-895">All Transports are enabled.</span></span> | <span data-ttu-id="14533-896">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14533-897">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-897">See below.</span></span> | <span data-ttu-id="14533-898">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14533-899">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-899">See below.</span></span> | <span data-ttu-id="14533-900">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="14533-901">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="14533-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14533-902">选项</span><span class="sxs-lookup"><span data-stu-id="14533-902">Option</span></span> | <span data-ttu-id="14533-903">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-903">Default Value</span></span> | <span data-ttu-id="14533-904">说明</span><span class="sxs-lookup"><span data-stu-id="14533-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14533-905">90秒</span><span class="sxs-lookup"><span data-stu-id="14533-905">90 seconds</span></span> | <span data-ttu-id="14533-906">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14533-907">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="14533-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14533-908">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="14533-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14533-909">选项</span><span class="sxs-lookup"><span data-stu-id="14533-909">Option</span></span> | <span data-ttu-id="14533-910">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-910">Default Value</span></span> | <span data-ttu-id="14533-911">说明</span><span class="sxs-lookup"><span data-stu-id="14533-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14533-912">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-912">5 seconds</span></span> | <span data-ttu-id="14533-913">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="14533-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14533-914">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="14533-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14533-915">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="14533-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14533-916">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="14533-916">Configure client options</span></span>

<span data-ttu-id="14533-917">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="14533-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14533-918">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="14533-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14533-919">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="14533-919">Configure logging</span></span>

<span data-ttu-id="14533-920">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14533-921">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="14533-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14533-922">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="14533-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-923">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="14533-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14533-924">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="14533-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14533-925">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="14533-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14533-926">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14533-927">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="14533-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14533-928">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="14533-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14533-929">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="14533-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14533-930">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14533-931">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="14533-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14533-932">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="14533-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14533-933">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="14533-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14533-934">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14533-935">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="14533-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14533-936">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="14533-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14533-937">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="14533-937">Configure allowed transports</span></span>

<span data-ttu-id="14533-938">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="14533-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14533-939">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14533-940">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="14533-940">All transports are enabled by default.</span></span>

<span data-ttu-id="14533-941">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="14533-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14533-942">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="14533-943">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14533-944">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="14533-944">Configure bearer authentication</span></span>

<span data-ttu-id="14533-945">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="14533-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14533-946">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="14533-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14533-947">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="14533-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14533-948">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="14533-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14533-949">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14533-950">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="14533-951">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14533-952">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14533-953">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14533-954">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="14533-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14533-955">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="14533-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-956">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-957">选项</span><span class="sxs-lookup"><span data-stu-id="14533-957">Option</span></span> | <span data-ttu-id="14533-958">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-958">Default value</span></span> | <span data-ttu-id="14533-959">说明</span><span class="sxs-lookup"><span data-stu-id="14533-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14533-960">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-961">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-961">Timeout for server activity.</span></span> <span data-ttu-id="14533-962">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-963">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-964">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14533-965">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-965">15 seconds</span></span> | <span data-ttu-id="14533-966">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-967">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-968">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-969">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-970">15 seconds</span></span> | <span data-ttu-id="14533-971">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-972">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-973">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="14533-974">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="14533-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14533-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-976">选项</span><span class="sxs-lookup"><span data-stu-id="14533-976">Option</span></span> | <span data-ttu-id="14533-977">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-977">Default value</span></span> | <span data-ttu-id="14533-978">说明</span><span class="sxs-lookup"><span data-stu-id="14533-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14533-979">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-980">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-980">Timeout for server activity.</span></span> <span data-ttu-id="14533-981">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14533-982">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-983">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="14533-984">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14533-985">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-986">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-987">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-988">Java</span><span class="sxs-lookup"><span data-stu-id="14533-988">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-989">选项</span><span class="sxs-lookup"><span data-stu-id="14533-989">Option</span></span> | <span data-ttu-id="14533-990">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-990">Default value</span></span> | <span data-ttu-id="14533-991">说明</span><span class="sxs-lookup"><span data-stu-id="14533-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14533-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14533-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14533-993">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-994">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-994">Timeout for server activity.</span></span> <span data-ttu-id="14533-995">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-996">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-997">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14533-998">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-998">15 seconds</span></span> | <span data-ttu-id="14533-999">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-1000">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-1001">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-1002">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="14533-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="14533-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="14533-1004">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="14533-1005">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="14533-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="14533-1006">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="14533-1007">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14533-1008">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="14533-1008">Configure additional options</span></span>

<span data-ttu-id="14533-1009">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="14533-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-1011">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="14533-1011">.NET Option</span></span> |  <span data-ttu-id="14533-1012">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1012">Default value</span></span> | <span data-ttu-id="14533-1013">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14533-1014">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14533-1015">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-1016">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-1017">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14533-1018">空</span><span class="sxs-lookup"><span data-stu-id="14533-1018">Empty</span></span> | <span data-ttu-id="14533-1019">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14533-1020">空</span><span class="sxs-lookup"><span data-stu-id="14533-1020">Empty</span></span> | <span data-ttu-id="14533-1021">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14533-1022">空</span><span class="sxs-lookup"><span data-stu-id="14533-1022">Empty</span></span> | <span data-ttu-id="14533-1023">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14533-1024">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-1024">5 seconds</span></span> | <span data-ttu-id="14533-1025">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="14533-1025">WebSockets only.</span></span> <span data-ttu-id="14533-1026">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14533-1027">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14533-1028">空</span><span class="sxs-lookup"><span data-stu-id="14533-1028">Empty</span></span> | <span data-ttu-id="14533-1029">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14533-1030">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="14533-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14533-1031">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="14533-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="14533-1032">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="14533-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14533-1033">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="14533-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14533-1034">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="14533-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14533-1035">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="14533-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14533-1036">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14533-1037">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="14533-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14533-1038">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="14533-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14533-1039">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="14533-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14533-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-1041">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="14533-1041">JavaScript Option</span></span> | <span data-ttu-id="14533-1042">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1042">Default Value</span></span> | <span data-ttu-id="14533-1043">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14533-1044">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="14533-1045">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="14533-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14533-1046">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-1047">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-1048">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-1049">Java</span><span class="sxs-lookup"><span data-stu-id="14533-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-1050">Java 选项</span><span class="sxs-lookup"><span data-stu-id="14533-1050">Java Option</span></span> | <span data-ttu-id="14533-1051">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1051">Default Value</span></span> | <span data-ttu-id="14533-1052">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14533-1053">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14533-1054">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-1055">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-1056">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14533-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14533-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14533-1058">空</span><span class="sxs-lookup"><span data-stu-id="14533-1058">Empty</span></span> | <span data-ttu-id="14533-1059">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14533-1060">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14533-1061">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14533-1062">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14533-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14533-1063">其他资源</span><span class="sxs-lookup"><span data-stu-id="14533-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="14533-1064">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="14533-1065">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="14533-1066">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="14533-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="14533-1067">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="14533-1068">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="14533-1069">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="14533-1070">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="14533-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="14533-1071">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="14533-1072">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="14533-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="14533-1073">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="14533-1074">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="14533-1075">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="14533-1075">MessagePack serialization options</span></span>

<span data-ttu-id="14533-1076">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="14533-1077">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="14533-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-1078">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="14533-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="14533-1079">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="14533-1079">Configure server options</span></span>

<span data-ttu-id="14533-1080">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="14533-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="14533-1081">选项</span><span class="sxs-lookup"><span data-stu-id="14533-1081">Option</span></span> | <span data-ttu-id="14533-1082">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1082">Default Value</span></span> | <span data-ttu-id="14533-1083">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="14533-1084">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-1084">15 seconds</span></span> | <span data-ttu-id="14533-1085">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="14533-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="14533-1086">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-1087">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="14533-1088">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-1088">15 seconds</span></span> | <span data-ttu-id="14533-1089">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="14533-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="14533-1090">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="14533-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="14533-1091">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="14533-1092">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="14533-1092">All installed protocols</span></span> | <span data-ttu-id="14533-1093">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="14533-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="14533-1094">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="14533-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="14533-1095">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="14533-1096">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="14533-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="14533-1097">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="14533-1098">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="14533-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="14533-1099">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="14533-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="14533-1100">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="14533-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="14533-1101">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="14533-1102">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="14533-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="14533-1103">选项</span><span class="sxs-lookup"><span data-stu-id="14533-1103">Option</span></span> | <span data-ttu-id="14533-1104">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1104">Default Value</span></span> | <span data-ttu-id="14533-1105">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="14533-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-1106">32 KB</span></span> | <span data-ttu-id="14533-1107">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="14533-1108">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="14533-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="14533-1109">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="14533-1110">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="14533-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="14533-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="14533-1111">32 KB</span></span> | <span data-ttu-id="14533-1112">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="14533-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="14533-1113">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="14533-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="14533-1114">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="14533-1114">All Transports are enabled.</span></span> | <span data-ttu-id="14533-1115">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="14533-1116">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-1116">See below.</span></span> | <span data-ttu-id="14533-1117">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="14533-1118">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="14533-1118">See below.</span></span> | <span data-ttu-id="14533-1119">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="14533-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="14533-1120">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="14533-1121">选项</span><span class="sxs-lookup"><span data-stu-id="14533-1121">Option</span></span> | <span data-ttu-id="14533-1122">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1122">Default Value</span></span> | <span data-ttu-id="14533-1123">描述</span><span class="sxs-lookup"><span data-stu-id="14533-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="14533-1124">90秒</span><span class="sxs-lookup"><span data-stu-id="14533-1124">90 seconds</span></span> | <span data-ttu-id="14533-1125">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="14533-1126">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="14533-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="14533-1127">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="14533-1128">选项</span><span class="sxs-lookup"><span data-stu-id="14533-1128">Option</span></span> | <span data-ttu-id="14533-1129">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1129">Default Value</span></span> | <span data-ttu-id="14533-1130">描述</span><span class="sxs-lookup"><span data-stu-id="14533-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="14533-1131">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-1131">5 seconds</span></span> | <span data-ttu-id="14533-1132">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="14533-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="14533-1133">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="14533-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="14533-1134">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="14533-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="14533-1135">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="14533-1135">Configure client options</span></span>

<span data-ttu-id="14533-1136">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="14533-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="14533-1137">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="14533-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="14533-1138">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="14533-1138">Configure logging</span></span>

<span data-ttu-id="14533-1139">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="14533-1140">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="14533-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="14533-1141">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="14533-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="14533-1142">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="14533-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="14533-1143">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="14533-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="14533-1144">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="14533-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="14533-1145">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="14533-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="14533-1146">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="14533-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="14533-1147">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="14533-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="14533-1148">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="14533-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="14533-1149">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="14533-1150">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="14533-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="14533-1151">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="14533-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14533-1152">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="14533-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14533-1153">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="14533-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14533-1154">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="14533-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14533-1155">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="14533-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="14533-1156">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="14533-1156">Configure allowed transports</span></span>

<span data-ttu-id="14533-1157">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="14533-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="14533-1158">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="14533-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="14533-1159">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="14533-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="14533-1160">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="14533-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="14533-1161">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="14533-1162">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="14533-1162">Configure bearer authentication</span></span>

<span data-ttu-id="14533-1163">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="14533-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="14533-1164">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="14533-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="14533-1165">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="14533-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="14533-1166">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="14533-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="14533-1167">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="14533-1168">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="14533-1169">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14533-1170">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="14533-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14533-1171">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="14533-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="14533-1172">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="14533-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="14533-1173">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="14533-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-1175">选项</span><span class="sxs-lookup"><span data-stu-id="14533-1175">Option</span></span> | <span data-ttu-id="14533-1176">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1176">Default value</span></span> | <span data-ttu-id="14533-1177">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="14533-1178">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-1179">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-1179">Timeout for server activity.</span></span> <span data-ttu-id="14533-1180">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-1181">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-1182">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="14533-1183">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-1183">15 seconds</span></span> | <span data-ttu-id="14533-1184">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-1185">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="14533-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="14533-1186">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-1187">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="14533-1188">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="14533-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="14533-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-1190">选项</span><span class="sxs-lookup"><span data-stu-id="14533-1190">Option</span></span> | <span data-ttu-id="14533-1191">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1191">Default value</span></span> | <span data-ttu-id="14533-1192">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="14533-1193">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-1194">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-1194">Timeout for server activity.</span></span> <span data-ttu-id="14533-1195">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="14533-1196">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-1197">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-1198">Java</span><span class="sxs-lookup"><span data-stu-id="14533-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-1199">选项</span><span class="sxs-lookup"><span data-stu-id="14533-1199">Option</span></span> | <span data-ttu-id="14533-1200">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1200">Default value</span></span> | <span data-ttu-id="14533-1201">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="14533-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="14533-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="14533-1203">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="14533-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="14533-1204">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="14533-1204">Timeout for server activity.</span></span> <span data-ttu-id="14533-1205">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-1206">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="14533-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="14533-1207">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="14533-1208">15 秒</span><span class="sxs-lookup"><span data-stu-id="14533-1208">15 seconds</span></span> | <span data-ttu-id="14533-1209">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="14533-1210">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="14533-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="14533-1211">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="14533-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="14533-1212">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="14533-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="14533-1213">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="14533-1213">Configure additional options</span></span>

<span data-ttu-id="14533-1214">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="14533-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="14533-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="14533-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="14533-1216">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="14533-1216">.NET Option</span></span> |  <span data-ttu-id="14533-1217">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1217">Default value</span></span> | <span data-ttu-id="14533-1218">说明</span><span class="sxs-lookup"><span data-stu-id="14533-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="14533-1219">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="14533-1220">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-1221">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-1222">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="14533-1223">空</span><span class="sxs-lookup"><span data-stu-id="14533-1223">Empty</span></span> | <span data-ttu-id="14533-1224">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="14533-1225">空</span><span class="sxs-lookup"><span data-stu-id="14533-1225">Empty</span></span> | <span data-ttu-id="14533-1226">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="14533-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="14533-1227">空</span><span class="sxs-lookup"><span data-stu-id="14533-1227">Empty</span></span> | <span data-ttu-id="14533-1228">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="14533-1229">5 秒</span><span class="sxs-lookup"><span data-stu-id="14533-1229">5 seconds</span></span> | <span data-ttu-id="14533-1230">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="14533-1230">WebSockets only.</span></span> <span data-ttu-id="14533-1231">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="14533-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="14533-1232">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="14533-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="14533-1233">空</span><span class="sxs-lookup"><span data-stu-id="14533-1233">Empty</span></span> | <span data-ttu-id="14533-1234">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="14533-1235">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="14533-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="14533-1236">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="14533-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="14533-1237">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="14533-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="14533-1238">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="14533-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="14533-1239">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="14533-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="14533-1240">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="14533-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="14533-1241">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="14533-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="14533-1242">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="14533-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="14533-1243">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="14533-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="14533-1244">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="14533-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="14533-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14533-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="14533-1246">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="14533-1246">JavaScript Option</span></span> | <span data-ttu-id="14533-1247">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1247">Default Value</span></span> | <span data-ttu-id="14533-1248">描述</span><span class="sxs-lookup"><span data-stu-id="14533-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="14533-1249">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="14533-1250">设置为 `true` 可记录客户端发送和接收的消息的字节数/字符数。</span><span class="sxs-lookup"><span data-stu-id="14533-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="14533-1251">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-1252">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-1253">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="14533-1254">Java</span><span class="sxs-lookup"><span data-stu-id="14533-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="14533-1255">Java 选项</span><span class="sxs-lookup"><span data-stu-id="14533-1255">Java Option</span></span> | <span data-ttu-id="14533-1256">默认值</span><span class="sxs-lookup"><span data-stu-id="14533-1256">Default Value</span></span> | <span data-ttu-id="14533-1257">描述</span><span class="sxs-lookup"><span data-stu-id="14533-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="14533-1258">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="14533-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="14533-1259">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="14533-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="14533-1260">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="14533-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="14533-1261">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="14533-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="14533-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="14533-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="14533-1263">空</span><span class="sxs-lookup"><span data-stu-id="14533-1263">Empty</span></span> | <span data-ttu-id="14533-1264">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="14533-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="14533-1265">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="14533-1266">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="14533-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="14533-1267">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="14533-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="14533-1268">其他资源</span><span class="sxs-lookup"><span data-stu-id="14533-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
