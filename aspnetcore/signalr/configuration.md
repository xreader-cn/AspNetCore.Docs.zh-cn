---
title: ASP.NET Core SignalR配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR应用。
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
ms.openlocfilehash: 054462c37fffd1973cbbe4f76ae4a3be5a6c1778
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767299"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="9a895-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="9a895-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a895-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a895-105">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a895-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a895-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9a895-108">`AddJsonProtocol`可以在中`Startup.ConfigureServices`的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加。</span><span class="sxs-lookup"><span data-stu-id="9a895-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9a895-109">`AddJsonProtocol`方法采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a895-110">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个`System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions>对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a895-111">有关详细信息，请参阅[system.object 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="9a895-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9a895-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用`Startup.ConfigureServices`中的以下代码：</span><span class="sxs-lookup"><span data-stu-id="9a895-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9a895-113">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a895-114">必须`Microsoft.Extensions.DependencyInjection`导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9a895-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9a895-116">切换到 Newtonsoft.json</span><span class="sxs-lookup"><span data-stu-id="9a895-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9a895-117">如果需要的功能`Newtonsoft.Json`在中`System.Text.Json`不受支持，请参阅[切换到 newtonsoft.json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="9a895-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a895-118">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-118">MessagePack serialization options</span></span>

<span data-ttu-id="9a895-119">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a895-120">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="9a895-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-121">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a895-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9a895-122">Configure server options</span></span>

<span data-ttu-id="9a895-123">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a895-124">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-124">Option</span></span> | <span data-ttu-id="9a895-125">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-125">Default Value</span></span> | <span data-ttu-id="9a895-126">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9a895-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-127">30 seconds</span></span> | <span data-ttu-id="9a895-128">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9a895-129">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9a895-130">建议值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9a895-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-131">15 seconds</span></span> | <span data-ttu-id="9a895-132">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9a895-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a895-133">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-134">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-135">15 seconds</span></span> | <span data-ttu-id="9a895-136">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="9a895-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a895-137">更改`KeepAliveInterval`时，请更改`ServerTimeout` / `serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a895-138">`ServerTimeout` /建议`serverTimeoutInMilliseconds`值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a895-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9a895-139">All installed protocols</span></span> | <span data-ttu-id="9a895-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-140">Protocols supported by this hub.</span></span> <span data-ttu-id="9a895-141">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a895-142">如果`true`为，则在集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a895-143">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9a895-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9a895-144">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="9a895-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9a895-145">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="9a895-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9a895-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-146">32 KB</span></span> | <span data-ttu-id="9a895-147">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="9a895-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9a895-148">可以通过在中`AddSignalR` `Startup.ConfigureServices`提供对调用的选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9a895-149">单个集线器的选项用于替代和中`AddSignalR`提供的全局选项，可以使用<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="9a895-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a895-150">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9a895-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a895-151">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a895-152">这些选项通过将委托传递给中`Startup.Configure`的[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)进行配置。</span><span class="sxs-lookup"><span data-stu-id="9a895-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9a895-153">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a895-154">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-154">Option</span></span> | <span data-ttu-id="9a895-155">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-155">Default Value</span></span> | <span data-ttu-id="9a895-156">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a895-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-157">32 KB</span></span> | <span data-ttu-id="9a895-158">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9a895-159">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9a895-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a895-160">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="9a895-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a895-161">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9a895-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a895-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-162">32 KB</span></span> | <span data-ttu-id="9a895-163">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9a895-164">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9a895-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a895-165">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="9a895-165">All Transports are enabled.</span></span> | <span data-ttu-id="9a895-166">`HttpTransportType`值的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a895-167">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-167">See below.</span></span> | <span data-ttu-id="9a895-168">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a895-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-169">See below.</span></span> | <span data-ttu-id="9a895-170">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9a895-171">0</span><span class="sxs-lookup"><span data-stu-id="9a895-171">0</span></span> | <span data-ttu-id="9a895-172">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="9a895-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9a895-173">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="9a895-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9a895-174">长轮询传输具有可使用`LongPolling`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a895-175">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-175">Option</span></span> | <span data-ttu-id="9a895-176">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-176">Default Value</span></span> | <span data-ttu-id="9a895-177">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a895-178">90秒</span><span class="sxs-lookup"><span data-stu-id="9a895-178">90 seconds</span></span> | <span data-ttu-id="9a895-179">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a895-180">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="9a895-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a895-181">WebSocket 传输具有可使用`WebSockets`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a895-182">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-182">Option</span></span> | <span data-ttu-id="9a895-183">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-183">Default Value</span></span> | <span data-ttu-id="9a895-184">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a895-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-185">5 seconds</span></span> | <span data-ttu-id="9a895-186">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9a895-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a895-187">一个委托，可用于将`Sec-WebSocket-Protocol`标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="9a895-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a895-188">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9a895-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a895-189">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9a895-189">Configure client options</span></span>

<span data-ttu-id="9a895-190">可以在`HubConnectionBuilder`类型上配置客户端选项（在 .Net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="9a895-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a895-191">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类是包含生成器配置选项的内容，也是其`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9a895-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a895-192">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9a895-192">Configure logging</span></span>

<span data-ttu-id="9a895-193">使用`ConfigureLogging`方法在 .Net 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a895-194">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="9a895-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a895-195">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="9a895-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-196">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="9a895-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a895-197">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9a895-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a895-198">例如，若要启用控制台日志记录，请`Microsoft.Extensions.Logging.Console`安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9a895-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a895-199">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a895-200">在 JavaScript 客户端中，存在`configureLogging`类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a895-201">提供一个`LogLevel`值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="9a895-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a895-202">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="9a895-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9a895-203">您还可以`LogLevel`提供一个`string`表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="9a895-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9a895-204">在无法访问常量的`LogLevel`环境中配置 SignalR 日志记录时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="9a895-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9a895-205">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="9a895-205">The following table lists the available log levels.</span></span> <span data-ttu-id="9a895-206">为`configureLogging`设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="9a895-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9a895-207">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="9a895-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9a895-208">String</span><span class="sxs-lookup"><span data-stu-id="9a895-208">String</span></span>                      | <span data-ttu-id="9a895-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9a895-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9a895-210">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="9a895-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9a895-211">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="9a895-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9a895-212">若要完全禁用日志记录`signalR.LogLevel.None` ，请`configureLogging`在方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9a895-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a895-213">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9a895-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a895-214">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a895-215">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9a895-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a895-216">下面的代码段演示如何将`java.util.logging`用于 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a895-217">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="9a895-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a895-218">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="9a895-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a895-219">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9a895-219">Configure allowed transports</span></span>

<span data-ttu-id="9a895-220">可以在`WithUrl`调用（`withUrl` JavaScript）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a895-221">的值的按位 "或" `HttpTransportType`可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a895-222">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-222">All transports are enabled by default.</span></span>

<span data-ttu-id="9a895-223">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9a895-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a895-224">在 JavaScript 客户端中，通过在提供的选项`transport`对象上设置字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9a895-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9a895-225">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9a895-226">在 Java 客户端中，通过中的`withTransport`方法选择了传输。 `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9a895-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9a895-227">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a895-228">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="9a895-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a895-229">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="9a895-229">Configure bearer authentication</span></span>

<span data-ttu-id="9a895-230">若要将身份验证数据与 SignalR 请求一起提供`AccessTokenProvider` ，请`accessTokenFactory`使用选项（在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9a895-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a895-231">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用类型`Authorization`为的`Bearer`标头）。</span><span class="sxs-lookup"><span data-stu-id="9a895-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a895-232">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a895-233">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9a895-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a895-234">在 .NET 客户端中， `AccessTokenProvider`可使用中`WithUrl`的选项委托指定选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a895-235">在 JavaScript 客户端中，通过在中`accessTokenFactory` `withUrl`设置 "选项" 对象上的字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="9a895-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9a895-236">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a895-237">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [>的单个\<字符串](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a895-238">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a895-239">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a895-240">用于配置超时和保持活动状态的其他选项可用于`HubConnection`对象本身：</span><span class="sxs-lookup"><span data-stu-id="9a895-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-241">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-242">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-242">Option</span></span> | <span data-ttu-id="9a895-243">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-243">Default value</span></span> | <span data-ttu-id="9a895-244">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a895-245">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-246">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-246">Timeout for server activity.</span></span> <span data-ttu-id="9a895-247">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`Closed` ，并`onclose`触发事件（在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-248">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-249">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a895-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-250">15 seconds</span></span> | <span data-ttu-id="9a895-251">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-252">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`Closed`触发事件`onclose` （在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-253">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-254">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-255">15 seconds</span></span> | <span data-ttu-id="9a895-256">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-257">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-258">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9a895-259">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9a895-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a895-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-261">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-261">Option</span></span> | <span data-ttu-id="9a895-262">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-262">Default value</span></span> | <span data-ttu-id="9a895-263">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a895-264">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-265">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-265">Timeout for server activity.</span></span> <span data-ttu-id="9a895-266">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onclose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a895-267">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-268">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9a895-269">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a895-270">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-271">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-272">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-273">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-273">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-274">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-274">Option</span></span> | <span data-ttu-id="9a895-275">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-275">Default value</span></span> | <span data-ttu-id="9a895-276">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a895-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a895-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a895-278">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-279">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-279">Timeout for server activity.</span></span> <span data-ttu-id="9a895-280">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onClose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-281">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-282">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a895-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-283">15 seconds</span></span> | <span data-ttu-id="9a895-284">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-285">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-286">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-287">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9a895-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9a895-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9a895-289">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a895-290">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-291">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-292">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a895-293">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9a895-293">Configure additional options</span></span>

<span data-ttu-id="9a895-294">可在上`WithUrl`的（`withUrl` JavaScript）方法中`HubConnectionBuilder`或在 Java 客户端`HttpHubConnectionBuilder`中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-295">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-296">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-296">.NET Option</span></span> |  <span data-ttu-id="9a895-297">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-297">Default value</span></span> | <span data-ttu-id="9a895-298">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a895-299">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a895-300">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-301">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-302">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a895-303">空</span><span class="sxs-lookup"><span data-stu-id="9a895-303">Empty</span></span> | <span data-ttu-id="9a895-304">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a895-305">空</span><span class="sxs-lookup"><span data-stu-id="9a895-305">Empty</span></span> | <span data-ttu-id="9a895-306">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a895-307">空</span><span class="sxs-lookup"><span data-stu-id="9a895-307">Empty</span></span> | <span data-ttu-id="9a895-308">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a895-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-309">5 seconds</span></span> | <span data-ttu-id="9a895-310">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="9a895-310">WebSockets only.</span></span> <span data-ttu-id="9a895-311">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a895-312">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a895-313">空</span><span class="sxs-lookup"><span data-stu-id="9a895-313">Empty</span></span> | <span data-ttu-id="9a895-314">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a895-315">一个委托，可用于配置或替换用于发送 HTTP `HttpMessageHandler`请求的。</span><span class="sxs-lookup"><span data-stu-id="9a895-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a895-316">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a895-317">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="9a895-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a895-318">修改该默认值的设置并将其返回，或返回一个新`HttpMessageHandler`的实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a895-319">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9a895-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a895-320">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9a895-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a895-321">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a895-322">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9a895-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a895-323">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a895-324">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a895-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-326">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-326">JavaScript Option</span></span> | <span data-ttu-id="9a895-327">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-327">Default Value</span></span> | <span data-ttu-id="9a895-328">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a895-329">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a895-330">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-331">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-332">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="9a895-333">指定是否将凭据与 CORS 请求一起发送。</span><span class="sxs-lookup"><span data-stu-id="9a895-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="9a895-334">Azure App Service 使用 cookie 进行粘滞会话，并且需要此选项才能正常工作。</span><span class="sxs-lookup"><span data-stu-id="9a895-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="9a895-335">有关与 SignalR 的 CORS 的详细信息， <xref:signalr/security#cross-origin-resource-sharing>请参阅。</span><span class="sxs-lookup"><span data-stu-id="9a895-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-336">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-336">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-337">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-337">Java Option</span></span> | <span data-ttu-id="9a895-338">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-338">Default Value</span></span> | <span data-ttu-id="9a895-339">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a895-340">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a895-341">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-342">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-343">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a895-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a895-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a895-345">空</span><span class="sxs-lookup"><span data-stu-id="9a895-345">Empty</span></span> | <span data-ttu-id="9a895-346">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a895-347">在 .NET 客户端中，可以通过提供给的 options 委托来`WithUrl`修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a895-348">在 JavaScript 客户端中，可以在提供给`withUrl`的 javascript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a895-349">在 Java 客户端中，这些选项可以`HttpHubConnectionBuilder`在从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a895-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a895-350">其他资源</span><span class="sxs-lookup"><span data-stu-id="9a895-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a895-351">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a895-352">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a895-353">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a895-354">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9a895-355">`AddJsonProtocol`可以在中`Startup.ConfigureServices`的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加。</span><span class="sxs-lookup"><span data-stu-id="9a895-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9a895-356">`AddJsonProtocol`方法采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a895-357">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个`System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions>对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a895-358">有关详细信息，请参阅[system.object 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="9a895-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9a895-359">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用`Startup.ConfigureServices`中的以下代码：</span><span class="sxs-lookup"><span data-stu-id="9a895-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9a895-360">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a895-361">必须`Microsoft.Extensions.DependencyInjection`导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9a895-362">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9a895-363">切换到 Newtonsoft.json</span><span class="sxs-lookup"><span data-stu-id="9a895-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9a895-364">如果需要的功能`Newtonsoft.Json`在中`System.Text.Json`不受支持，请参阅[切换到 newtonsoft.json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="9a895-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a895-365">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-365">MessagePack serialization options</span></span>

<span data-ttu-id="9a895-366">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a895-367">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="9a895-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-368">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a895-369">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9a895-369">Configure server options</span></span>

<span data-ttu-id="9a895-370">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a895-371">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-371">Option</span></span> | <span data-ttu-id="9a895-372">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-372">Default Value</span></span> | <span data-ttu-id="9a895-373">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9a895-374">30 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-374">30 seconds</span></span> | <span data-ttu-id="9a895-375">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9a895-376">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9a895-377">建议值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9a895-378">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-378">15 seconds</span></span> | <span data-ttu-id="9a895-379">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9a895-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a895-380">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-381">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-382">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-382">15 seconds</span></span> | <span data-ttu-id="9a895-383">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="9a895-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a895-384">更改`KeepAliveInterval`时，请更改`ServerTimeout` / `serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a895-385">`ServerTimeout` /建议`serverTimeoutInMilliseconds`值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a895-386">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9a895-386">All installed protocols</span></span> | <span data-ttu-id="9a895-387">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-387">Protocols supported by this hub.</span></span> <span data-ttu-id="9a895-388">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a895-389">如果`true`为，则在集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a895-390">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9a895-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9a895-391">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="9a895-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9a895-392">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="9a895-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9a895-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-393">32 KB</span></span> | <span data-ttu-id="9a895-394">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="9a895-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9a895-395">可以通过在中`AddSignalR` `Startup.ConfigureServices`提供对调用的选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9a895-396">单个集线器的选项用于替代和中`AddSignalR`提供的全局选项，可以使用<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="9a895-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a895-397">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9a895-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a895-398">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a895-399">这些选项通过将委托传递给中`Startup.Configure`的[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)进行配置。</span><span class="sxs-lookup"><span data-stu-id="9a895-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9a895-400">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a895-401">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-401">Option</span></span> | <span data-ttu-id="9a895-402">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-402">Default Value</span></span> | <span data-ttu-id="9a895-403">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a895-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-404">32 KB</span></span> | <span data-ttu-id="9a895-405">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9a895-406">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9a895-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a895-407">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="9a895-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a895-408">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9a895-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a895-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-409">32 KB</span></span> | <span data-ttu-id="9a895-410">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9a895-411">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9a895-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a895-412">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="9a895-412">All Transports are enabled.</span></span> | <span data-ttu-id="9a895-413">`HttpTransportType`值的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a895-414">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-414">See below.</span></span> | <span data-ttu-id="9a895-415">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a895-416">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-416">See below.</span></span> | <span data-ttu-id="9a895-417">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9a895-418">0</span><span class="sxs-lookup"><span data-stu-id="9a895-418">0</span></span> | <span data-ttu-id="9a895-419">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="9a895-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9a895-420">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="9a895-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9a895-421">长轮询传输具有可使用`LongPolling`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a895-422">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-422">Option</span></span> | <span data-ttu-id="9a895-423">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-423">Default Value</span></span> | <span data-ttu-id="9a895-424">描述</span><span class="sxs-lookup"><span data-stu-id="9a895-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a895-425">90秒</span><span class="sxs-lookup"><span data-stu-id="9a895-425">90 seconds</span></span> | <span data-ttu-id="9a895-426">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a895-427">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="9a895-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a895-428">WebSocket 传输具有可使用`WebSockets`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a895-429">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-429">Option</span></span> | <span data-ttu-id="9a895-430">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-430">Default Value</span></span> | <span data-ttu-id="9a895-431">描述</span><span class="sxs-lookup"><span data-stu-id="9a895-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a895-432">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-432">5 seconds</span></span> | <span data-ttu-id="9a895-433">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9a895-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a895-434">一个委托，可用于将`Sec-WebSocket-Protocol`标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="9a895-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a895-435">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9a895-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a895-436">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9a895-436">Configure client options</span></span>

<span data-ttu-id="9a895-437">可以在`HubConnectionBuilder`类型上配置客户端选项（在 .Net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="9a895-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a895-438">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类是包含生成器配置选项的内容，也是其`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9a895-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a895-439">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9a895-439">Configure logging</span></span>

<span data-ttu-id="9a895-440">使用`ConfigureLogging`方法在 .Net 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a895-441">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="9a895-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a895-442">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="9a895-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-443">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="9a895-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a895-444">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9a895-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a895-445">例如，若要启用控制台日志记录，请`Microsoft.Extensions.Logging.Console`安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9a895-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a895-446">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a895-447">在 JavaScript 客户端中，存在`configureLogging`类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a895-448">提供一个`LogLevel`值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="9a895-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a895-449">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="9a895-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9a895-450">您还可以`LogLevel`提供一个`string`表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="9a895-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9a895-451">在无法访问常量的`LogLevel`环境中配置 SignalR 日志记录时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="9a895-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9a895-452">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="9a895-452">The following table lists the available log levels.</span></span> <span data-ttu-id="9a895-453">为`configureLogging`设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="9a895-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9a895-454">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="9a895-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9a895-455">String</span><span class="sxs-lookup"><span data-stu-id="9a895-455">String</span></span>                      | <span data-ttu-id="9a895-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9a895-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9a895-457">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="9a895-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9a895-458">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="9a895-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9a895-459">若要完全禁用日志记录`signalR.LogLevel.None` ，请`configureLogging`在方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9a895-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a895-460">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9a895-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a895-461">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a895-462">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9a895-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a895-463">下面的代码段演示如何将`java.util.logging`用于 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a895-464">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="9a895-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a895-465">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="9a895-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a895-466">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9a895-466">Configure allowed transports</span></span>

<span data-ttu-id="9a895-467">可以在`WithUrl`调用（`withUrl` JavaScript）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a895-468">的值的按位 "或" `HttpTransportType`可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a895-469">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-469">All transports are enabled by default.</span></span>

<span data-ttu-id="9a895-470">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9a895-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a895-471">在 JavaScript 客户端中，通过在提供的选项`transport`对象上设置字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9a895-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9a895-472">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9a895-473">在 Java 客户端中，通过中的`withTransport`方法选择了传输。 `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9a895-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9a895-474">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a895-475">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="9a895-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a895-476">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="9a895-476">Configure bearer authentication</span></span>

<span data-ttu-id="9a895-477">若要将身份验证数据与 SignalR 请求一起提供`AccessTokenProvider` ，请`accessTokenFactory`使用选项（在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9a895-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a895-478">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用类型`Authorization`为的`Bearer`标头）。</span><span class="sxs-lookup"><span data-stu-id="9a895-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a895-479">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a895-480">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9a895-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a895-481">在 .NET 客户端中， `AccessTokenProvider`可使用中`WithUrl`的选项委托指定选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a895-482">在 JavaScript 客户端中，通过在中`accessTokenFactory` `withUrl`设置 "选项" 对象上的字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="9a895-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9a895-483">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a895-484">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [>的单个\<字符串](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a895-485">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a895-486">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a895-487">用于配置超时和保持活动状态的其他选项可用于`HubConnection`对象本身：</span><span class="sxs-lookup"><span data-stu-id="9a895-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-488">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-489">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-489">Option</span></span> | <span data-ttu-id="9a895-490">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-490">Default value</span></span> | <span data-ttu-id="9a895-491">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a895-492">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-493">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-493">Timeout for server activity.</span></span> <span data-ttu-id="9a895-494">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`Closed` ，并`onclose`触发事件（在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-495">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-496">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a895-497">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-497">15 seconds</span></span> | <span data-ttu-id="9a895-498">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-499">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`Closed`触发事件`onclose` （在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-500">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-501">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-502">15 seconds</span></span> | <span data-ttu-id="9a895-503">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-504">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-505">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9a895-506">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9a895-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a895-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-508">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-508">Option</span></span> | <span data-ttu-id="9a895-509">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-509">Default value</span></span> | <span data-ttu-id="9a895-510">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a895-511">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-512">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-512">Timeout for server activity.</span></span> <span data-ttu-id="9a895-513">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onclose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a895-514">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-515">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9a895-516">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a895-517">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-518">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-519">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-520">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-520">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-521">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-521">Option</span></span> | <span data-ttu-id="9a895-522">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-522">Default value</span></span> | <span data-ttu-id="9a895-523">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a895-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a895-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a895-525">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-526">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-526">Timeout for server activity.</span></span> <span data-ttu-id="9a895-527">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onClose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-528">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-529">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a895-530">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-530">15 seconds</span></span> | <span data-ttu-id="9a895-531">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-532">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-533">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-534">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9a895-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9a895-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9a895-536">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a895-537">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-538">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-539">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a895-540">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9a895-540">Configure additional options</span></span>

<span data-ttu-id="9a895-541">可在上`WithUrl`的（`withUrl` JavaScript）方法中`HubConnectionBuilder`或在 Java 客户端`HttpHubConnectionBuilder`中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-542">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-543">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-543">.NET Option</span></span> |  <span data-ttu-id="9a895-544">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-544">Default value</span></span> | <span data-ttu-id="9a895-545">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a895-546">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a895-547">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-548">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-549">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a895-550">空</span><span class="sxs-lookup"><span data-stu-id="9a895-550">Empty</span></span> | <span data-ttu-id="9a895-551">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a895-552">空</span><span class="sxs-lookup"><span data-stu-id="9a895-552">Empty</span></span> | <span data-ttu-id="9a895-553">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a895-554">空</span><span class="sxs-lookup"><span data-stu-id="9a895-554">Empty</span></span> | <span data-ttu-id="9a895-555">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a895-556">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-556">5 seconds</span></span> | <span data-ttu-id="9a895-557">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="9a895-557">WebSockets only.</span></span> <span data-ttu-id="9a895-558">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a895-559">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a895-560">空</span><span class="sxs-lookup"><span data-stu-id="9a895-560">Empty</span></span> | <span data-ttu-id="9a895-561">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a895-562">一个委托，可用于配置或替换用于发送 HTTP `HttpMessageHandler`请求的。</span><span class="sxs-lookup"><span data-stu-id="9a895-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a895-563">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a895-564">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="9a895-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a895-565">修改该默认值的设置并将其返回，或返回一个新`HttpMessageHandler`的实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a895-566">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9a895-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a895-567">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9a895-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a895-568">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a895-569">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9a895-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a895-570">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a895-571">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a895-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-573">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-573">JavaScript Option</span></span> | <span data-ttu-id="9a895-574">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-574">Default Value</span></span> | <span data-ttu-id="9a895-575">描述</span><span class="sxs-lookup"><span data-stu-id="9a895-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a895-576">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a895-577">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-578">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-579">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-580">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-580">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-581">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-581">Java Option</span></span> | <span data-ttu-id="9a895-582">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-582">Default Value</span></span> | <span data-ttu-id="9a895-583">描述</span><span class="sxs-lookup"><span data-stu-id="9a895-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a895-584">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a895-585">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-586">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-587">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a895-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a895-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a895-589">空</span><span class="sxs-lookup"><span data-stu-id="9a895-589">Empty</span></span> | <span data-ttu-id="9a895-590">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a895-591">在 .NET 客户端中，可以通过提供给的 options 委托来`WithUrl`修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a895-592">在 JavaScript 客户端中，可以在提供给`withUrl`的 javascript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a895-593">在 Java 客户端中，这些选项可以`HttpHubConnectionBuilder`在从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a895-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a895-594">其他资源</span><span class="sxs-lookup"><span data-stu-id="9a895-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a895-595">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a895-596">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a895-597">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a895-598">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9a895-599">`AddJsonProtocol`可以在中`Startup.ConfigureServices`的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加。</span><span class="sxs-lookup"><span data-stu-id="9a895-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9a895-600">`AddJsonProtocol`方法采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a895-601">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个`System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions>对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a895-602">有关详细信息，请参阅[system.object 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="9a895-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9a895-603">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用`Startup.ConfigureServices`中的以下代码：</span><span class="sxs-lookup"><span data-stu-id="9a895-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="9a895-604">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a895-605">必须`Microsoft.Extensions.DependencyInjection`导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9a895-606">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9a895-607">切换到 Newtonsoft.json</span><span class="sxs-lookup"><span data-stu-id="9a895-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9a895-608">如果需要的功能`Newtonsoft.Json`在中`System.Text.Json`不受支持，请参阅[切换到 newtonsoft.json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="9a895-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a895-609">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-609">MessagePack serialization options</span></span>

<span data-ttu-id="9a895-610">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a895-611">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="9a895-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-612">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a895-613">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9a895-613">Configure server options</span></span>

<span data-ttu-id="9a895-614">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a895-615">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-615">Option</span></span> | <span data-ttu-id="9a895-616">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-616">Default Value</span></span> | <span data-ttu-id="9a895-617">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9a895-618">30 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-618">30 seconds</span></span> | <span data-ttu-id="9a895-619">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9a895-620">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9a895-621">建议值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9a895-622">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-622">15 seconds</span></span> | <span data-ttu-id="9a895-623">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9a895-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a895-624">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-625">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-626">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-626">15 seconds</span></span> | <span data-ttu-id="9a895-627">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="9a895-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a895-628">更改`KeepAliveInterval`时，请更改`ServerTimeout` / `serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a895-629">`ServerTimeout` /建议`serverTimeoutInMilliseconds`值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a895-630">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9a895-630">All installed protocols</span></span> | <span data-ttu-id="9a895-631">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-631">Protocols supported by this hub.</span></span> <span data-ttu-id="9a895-632">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a895-633">如果`true`为，则在集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a895-634">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9a895-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9a895-635">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="9a895-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9a895-636">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="9a895-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9a895-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-637">32 KB</span></span> | <span data-ttu-id="9a895-638">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="9a895-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9a895-639">可以通过在中`AddSignalR` `Startup.ConfigureServices`提供对调用的选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9a895-640">单个集线器的选项用于替代和中`AddSignalR`提供的全局选项，可以使用<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="9a895-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a895-641">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9a895-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a895-642">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a895-643">这些选项通过将委托传递给中`Startup.Configure`的[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)进行配置。</span><span class="sxs-lookup"><span data-stu-id="9a895-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9a895-644">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a895-645">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-645">Option</span></span> | <span data-ttu-id="9a895-646">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-646">Default Value</span></span> | <span data-ttu-id="9a895-647">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a895-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-648">32 KB</span></span> | <span data-ttu-id="9a895-649">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9a895-650">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9a895-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a895-651">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="9a895-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a895-652">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9a895-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a895-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-653">32 KB</span></span> | <span data-ttu-id="9a895-654">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9a895-655">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9a895-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a895-656">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="9a895-656">All Transports are enabled.</span></span> | <span data-ttu-id="9a895-657">`HttpTransportType`值的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a895-658">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-658">See below.</span></span> | <span data-ttu-id="9a895-659">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a895-660">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-660">See below.</span></span> | <span data-ttu-id="9a895-661">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9a895-662">长轮询传输具有可使用`LongPolling`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a895-663">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-663">Option</span></span> | <span data-ttu-id="9a895-664">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-664">Default Value</span></span> | <span data-ttu-id="9a895-665">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a895-666">90秒</span><span class="sxs-lookup"><span data-stu-id="9a895-666">90 seconds</span></span> | <span data-ttu-id="9a895-667">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a895-668">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="9a895-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a895-669">WebSocket 传输具有可使用`WebSockets`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a895-670">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-670">Option</span></span> | <span data-ttu-id="9a895-671">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-671">Default Value</span></span> | <span data-ttu-id="9a895-672">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a895-673">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-673">5 seconds</span></span> | <span data-ttu-id="9a895-674">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9a895-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a895-675">一个委托，可用于将`Sec-WebSocket-Protocol`标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="9a895-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a895-676">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9a895-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a895-677">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9a895-677">Configure client options</span></span>

<span data-ttu-id="9a895-678">可以在`HubConnectionBuilder`类型上配置客户端选项（在 .Net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="9a895-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a895-679">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类是包含生成器配置选项的内容，也是其`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9a895-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a895-680">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9a895-680">Configure logging</span></span>

<span data-ttu-id="9a895-681">使用`ConfigureLogging`方法在 .Net 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a895-682">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="9a895-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a895-683">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="9a895-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-684">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="9a895-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a895-685">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9a895-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a895-686">例如，若要启用控制台日志记录，请`Microsoft.Extensions.Logging.Console`安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9a895-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a895-687">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a895-688">在 JavaScript 客户端中，存在`configureLogging`类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a895-689">提供一个`LogLevel`值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="9a895-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a895-690">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="9a895-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9a895-691">您还可以`LogLevel`提供一个`string`表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="9a895-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9a895-692">在无法访问常量的`LogLevel`环境中配置 SignalR 日志记录时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="9a895-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9a895-693">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="9a895-693">The following table lists the available log levels.</span></span> <span data-ttu-id="9a895-694">为`configureLogging`设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="9a895-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9a895-695">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="9a895-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9a895-696">String</span><span class="sxs-lookup"><span data-stu-id="9a895-696">String</span></span>                      | <span data-ttu-id="9a895-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9a895-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9a895-698">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="9a895-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9a895-699">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="9a895-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9a895-700">若要完全禁用日志记录`signalR.LogLevel.None` ，请`configureLogging`在方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9a895-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a895-701">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9a895-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a895-702">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a895-703">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9a895-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a895-704">下面的代码段演示如何将`java.util.logging`用于 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a895-705">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="9a895-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a895-706">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="9a895-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a895-707">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9a895-707">Configure allowed transports</span></span>

<span data-ttu-id="9a895-708">可以在`WithUrl`调用（`withUrl` JavaScript）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a895-709">的值的按位 "或" `HttpTransportType`可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a895-710">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-710">All transports are enabled by default.</span></span>

<span data-ttu-id="9a895-711">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9a895-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a895-712">在 JavaScript 客户端中，通过在提供的选项`transport`对象上设置字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9a895-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9a895-713">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9a895-714">在 Java 客户端中，通过中的`withTransport`方法选择了传输。 `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9a895-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9a895-715">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a895-716">SignalR Java 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="9a895-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a895-717">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="9a895-717">Configure bearer authentication</span></span>

<span data-ttu-id="9a895-718">若要将身份验证数据与 SignalR 请求一起提供`AccessTokenProvider` ，请`accessTokenFactory`使用选项（在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9a895-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a895-719">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用类型`Authorization`为的`Bearer`标头）。</span><span class="sxs-lookup"><span data-stu-id="9a895-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a895-720">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a895-721">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9a895-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a895-722">在 .NET 客户端中， `AccessTokenProvider`可使用中`WithUrl`的选项委托指定选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a895-723">在 JavaScript 客户端中，通过在中`accessTokenFactory` `withUrl`设置 "选项" 对象上的字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="9a895-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9a895-724">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a895-725">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [>的单个\<字符串](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a895-726">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a895-727">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a895-728">用于配置超时和保持活动状态的其他选项可用于`HubConnection`对象本身：</span><span class="sxs-lookup"><span data-stu-id="9a895-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-729">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-730">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-730">Option</span></span> | <span data-ttu-id="9a895-731">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-731">Default value</span></span> | <span data-ttu-id="9a895-732">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a895-733">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-734">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-734">Timeout for server activity.</span></span> <span data-ttu-id="9a895-735">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`Closed` ，并`onclose`触发事件（在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-736">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-737">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a895-738">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-738">15 seconds</span></span> | <span data-ttu-id="9a895-739">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-740">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`Closed`触发事件`onclose` （在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-741">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-742">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-743">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-743">15 seconds</span></span> | <span data-ttu-id="9a895-744">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-745">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-746">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9a895-747">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9a895-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a895-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-749">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-749">Option</span></span> | <span data-ttu-id="9a895-750">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-750">Default value</span></span> | <span data-ttu-id="9a895-751">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a895-752">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-753">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-753">Timeout for server activity.</span></span> <span data-ttu-id="9a895-754">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onclose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a895-755">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-756">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9a895-757">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a895-758">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-759">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-760">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-761">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-761">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-762">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-762">Option</span></span> | <span data-ttu-id="9a895-763">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-763">Default value</span></span> | <span data-ttu-id="9a895-764">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a895-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a895-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a895-766">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-767">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-767">Timeout for server activity.</span></span> <span data-ttu-id="9a895-768">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onClose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-769">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-770">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a895-771">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-771">15 seconds</span></span> | <span data-ttu-id="9a895-772">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-773">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-774">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-775">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9a895-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9a895-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9a895-777">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a895-778">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-779">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-780">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a895-781">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9a895-781">Configure additional options</span></span>

<span data-ttu-id="9a895-782">可在上`WithUrl`的（`withUrl` JavaScript）方法中`HubConnectionBuilder`或在 Java 客户端`HttpHubConnectionBuilder`中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-783">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-784">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-784">.NET Option</span></span> |  <span data-ttu-id="9a895-785">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-785">Default value</span></span> | <span data-ttu-id="9a895-786">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a895-787">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a895-788">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-789">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-790">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a895-791">空</span><span class="sxs-lookup"><span data-stu-id="9a895-791">Empty</span></span> | <span data-ttu-id="9a895-792">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a895-793">空</span><span class="sxs-lookup"><span data-stu-id="9a895-793">Empty</span></span> | <span data-ttu-id="9a895-794">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a895-795">空</span><span class="sxs-lookup"><span data-stu-id="9a895-795">Empty</span></span> | <span data-ttu-id="9a895-796">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a895-797">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-797">5 seconds</span></span> | <span data-ttu-id="9a895-798">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="9a895-798">WebSockets only.</span></span> <span data-ttu-id="9a895-799">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a895-800">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a895-801">空</span><span class="sxs-lookup"><span data-stu-id="9a895-801">Empty</span></span> | <span data-ttu-id="9a895-802">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a895-803">一个委托，可用于配置或替换用于发送 HTTP `HttpMessageHandler`请求的。</span><span class="sxs-lookup"><span data-stu-id="9a895-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a895-804">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a895-805">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="9a895-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a895-806">修改该默认值的设置并将其返回，或返回一个新`HttpMessageHandler`的实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a895-807">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9a895-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a895-808">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9a895-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a895-809">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a895-810">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9a895-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a895-811">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a895-812">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a895-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-814">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-814">JavaScript Option</span></span> | <span data-ttu-id="9a895-815">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-815">Default Value</span></span> | <span data-ttu-id="9a895-816">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a895-817">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a895-818">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-819">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-820">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-821">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-821">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-822">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-822">Java Option</span></span> | <span data-ttu-id="9a895-823">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-823">Default Value</span></span> | <span data-ttu-id="9a895-824">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a895-825">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a895-826">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-827">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-828">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a895-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a895-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a895-830">空</span><span class="sxs-lookup"><span data-stu-id="9a895-830">Empty</span></span> | <span data-ttu-id="9a895-831">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a895-832">在 .NET 客户端中，可以通过提供给的 options 委托来`WithUrl`修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a895-833">在 JavaScript 客户端中，可以在提供给`withUrl`的 javascript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a895-834">在 Java 客户端中，这些选项可以`HttpHubConnectionBuilder`在从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a895-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a895-835">其他资源</span><span class="sxs-lookup"><span data-stu-id="9a895-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a895-836">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a895-837">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a895-838">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a895-839">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在`Startup.ConfigureServices`方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="9a895-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a895-840">`AddJsonProtocol`方法采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a895-841">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings`对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a895-842">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="9a895-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9a895-843">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中`Startup.ConfigureServices`使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="9a895-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9a895-844">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a895-845">必须`Microsoft.Extensions.DependencyInjection`导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9a895-846">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a895-847">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-847">MessagePack serialization options</span></span>

<span data-ttu-id="9a895-848">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a895-849">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="9a895-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-850">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a895-851">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9a895-851">Configure server options</span></span>

<span data-ttu-id="9a895-852">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a895-853">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-853">Option</span></span> | <span data-ttu-id="9a895-854">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-854">Default Value</span></span> | <span data-ttu-id="9a895-855">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9a895-856">30 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-856">30 seconds</span></span> | <span data-ttu-id="9a895-857">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9a895-858">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9a895-859">建议值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9a895-860">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-860">15 seconds</span></span> | <span data-ttu-id="9a895-861">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9a895-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a895-862">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-863">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-864">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-864">15 seconds</span></span> | <span data-ttu-id="9a895-865">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="9a895-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a895-866">更改`KeepAliveInterval`时，请更改`ServerTimeout` / `serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a895-867">`ServerTimeout` /建议`serverTimeoutInMilliseconds`值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a895-868">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9a895-868">All installed protocols</span></span> | <span data-ttu-id="9a895-869">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-869">Protocols supported by this hub.</span></span> <span data-ttu-id="9a895-870">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a895-871">如果`true`为，则在集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a895-872">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9a895-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9a895-873">可以通过在中`AddSignalR` `Startup.ConfigureServices`提供对调用的选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9a895-874">单个集线器的选项用于替代和中`AddSignalR`提供的全局选项，可以使用<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="9a895-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a895-875">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9a895-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a895-876">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a895-877">这些选项通过将委托传递给中`Startup.Configure`的[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)进行配置。</span><span class="sxs-lookup"><span data-stu-id="9a895-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9a895-878">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a895-879">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-879">Option</span></span> | <span data-ttu-id="9a895-880">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-880">Default Value</span></span> | <span data-ttu-id="9a895-881">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a895-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-882">32 KB</span></span> | <span data-ttu-id="9a895-883">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9a895-884">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="9a895-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a895-885">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="9a895-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a895-886">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9a895-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a895-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-887">32 KB</span></span> | <span data-ttu-id="9a895-888">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9a895-889">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="9a895-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a895-890">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="9a895-890">All Transports are enabled.</span></span> | <span data-ttu-id="9a895-891">`HttpTransportType`值的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a895-892">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-892">See below.</span></span> | <span data-ttu-id="9a895-893">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a895-894">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-894">See below.</span></span> | <span data-ttu-id="9a895-895">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9a895-896">长轮询传输具有可使用`LongPolling`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a895-897">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-897">Option</span></span> | <span data-ttu-id="9a895-898">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-898">Default Value</span></span> | <span data-ttu-id="9a895-899">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a895-900">90秒</span><span class="sxs-lookup"><span data-stu-id="9a895-900">90 seconds</span></span> | <span data-ttu-id="9a895-901">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a895-902">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="9a895-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a895-903">WebSocket 传输具有可使用`WebSockets`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a895-904">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-904">Option</span></span> | <span data-ttu-id="9a895-905">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-905">Default Value</span></span> | <span data-ttu-id="9a895-906">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a895-907">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-907">5 seconds</span></span> | <span data-ttu-id="9a895-908">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9a895-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a895-909">一个委托，可用于将`Sec-WebSocket-Protocol`标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="9a895-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a895-910">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9a895-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a895-911">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9a895-911">Configure client options</span></span>

<span data-ttu-id="9a895-912">可以在`HubConnectionBuilder`类型上配置客户端选项（在 .Net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="9a895-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a895-913">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类是包含生成器配置选项的内容，也是其`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9a895-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a895-914">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9a895-914">Configure logging</span></span>

<span data-ttu-id="9a895-915">使用`ConfigureLogging`方法在 .Net 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a895-916">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="9a895-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a895-917">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="9a895-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-918">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="9a895-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a895-919">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9a895-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a895-920">例如，若要启用控制台日志记录，请`Microsoft.Extensions.Logging.Console`安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9a895-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a895-921">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a895-922">在 JavaScript 客户端中，存在`configureLogging`类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a895-923">提供一个`LogLevel`值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="9a895-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a895-924">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="9a895-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a895-925">若要完全禁用日志记录`signalR.LogLevel.None` ，请`configureLogging`在方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9a895-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a895-926">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9a895-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a895-927">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a895-928">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9a895-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a895-929">下面的代码段演示如何将`java.util.logging`用于 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a895-930">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="9a895-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a895-931">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="9a895-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a895-932">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9a895-932">Configure allowed transports</span></span>

<span data-ttu-id="9a895-933">可以在`WithUrl`调用（`withUrl` JavaScript）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a895-934">的值的按位 "或" `HttpTransportType`可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a895-935">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-935">All transports are enabled by default.</span></span>

<span data-ttu-id="9a895-936">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9a895-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a895-937">在 JavaScript 客户端中，通过在提供的选项`transport`对象上设置字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9a895-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9a895-938">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a895-939">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="9a895-939">Configure bearer authentication</span></span>

<span data-ttu-id="9a895-940">若要将身份验证数据与 SignalR 请求一起提供`AccessTokenProvider` ，请`accessTokenFactory`使用选项（在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9a895-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a895-941">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用类型`Authorization`为的`Bearer`标头）。</span><span class="sxs-lookup"><span data-stu-id="9a895-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a895-942">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a895-943">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9a895-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a895-944">在 .NET 客户端中， `AccessTokenProvider`可使用中`WithUrl`的选项委托指定选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a895-945">在 JavaScript 客户端中，通过在中`accessTokenFactory` `withUrl`设置 "选项" 对象上的字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="9a895-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9a895-946">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a895-947">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [>的单个\<字符串](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a895-948">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a895-949">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a895-950">用于配置超时和保持活动状态的其他选项可用于`HubConnection`对象本身：</span><span class="sxs-lookup"><span data-stu-id="9a895-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-951">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-952">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-952">Option</span></span> | <span data-ttu-id="9a895-953">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-953">Default value</span></span> | <span data-ttu-id="9a895-954">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a895-955">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-956">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-956">Timeout for server activity.</span></span> <span data-ttu-id="9a895-957">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`Closed` ，并`onclose`触发事件（在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-958">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-959">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a895-960">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-960">15 seconds</span></span> | <span data-ttu-id="9a895-961">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-962">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`Closed`触发事件`onclose` （在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-963">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-964">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-965">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-965">15 seconds</span></span> | <span data-ttu-id="9a895-966">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-967">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-968">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9a895-969">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9a895-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a895-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-971">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-971">Option</span></span> | <span data-ttu-id="9a895-972">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-972">Default value</span></span> | <span data-ttu-id="9a895-973">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a895-974">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-975">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-975">Timeout for server activity.</span></span> <span data-ttu-id="9a895-976">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onclose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a895-977">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-978">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9a895-979">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a895-980">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-981">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-982">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-983">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-983">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-984">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-984">Option</span></span> | <span data-ttu-id="9a895-985">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-985">Default value</span></span> | <span data-ttu-id="9a895-986">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a895-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a895-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a895-988">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-989">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-989">Timeout for server activity.</span></span> <span data-ttu-id="9a895-990">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onClose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-991">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-992">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a895-993">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-993">15 seconds</span></span> | <span data-ttu-id="9a895-994">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-995">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-996">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-997">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9a895-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9a895-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9a895-999">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9a895-1000">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="9a895-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9a895-1001">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9a895-1002">如果客户端没有在服务器上的`ClientTimeoutInterval`设置中发送消息，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a895-1003">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1003">Configure additional options</span></span>

<span data-ttu-id="9a895-1004">可在上`WithUrl`的（`withUrl` JavaScript）方法中`HubConnectionBuilder`或在 Java 客户端`HttpHubConnectionBuilder`中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-1006">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1006">.NET Option</span></span> |  <span data-ttu-id="9a895-1007">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1007">Default value</span></span> | <span data-ttu-id="9a895-1008">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a895-1009">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a895-1010">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-1011">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-1012">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a895-1013">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1013">Empty</span></span> | <span data-ttu-id="9a895-1014">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a895-1015">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1015">Empty</span></span> | <span data-ttu-id="9a895-1016">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a895-1017">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1017">Empty</span></span> | <span data-ttu-id="9a895-1018">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a895-1019">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-1019">5 seconds</span></span> | <span data-ttu-id="9a895-1020">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="9a895-1020">WebSockets only.</span></span> <span data-ttu-id="9a895-1021">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a895-1022">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a895-1023">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1023">Empty</span></span> | <span data-ttu-id="9a895-1024">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a895-1025">一个委托，可用于配置或替换用于发送 HTTP `HttpMessageHandler`请求的。</span><span class="sxs-lookup"><span data-stu-id="9a895-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a895-1026">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a895-1027">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="9a895-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a895-1028">修改该默认值的设置并将其返回，或返回一个新`HttpMessageHandler`的实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a895-1029">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9a895-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a895-1030">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9a895-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a895-1031">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a895-1032">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9a895-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a895-1033">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a895-1034">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a895-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-1036">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1036">JavaScript Option</span></span> | <span data-ttu-id="9a895-1037">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1037">Default Value</span></span> | <span data-ttu-id="9a895-1038">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a895-1039">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a895-1040">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-1041">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-1042">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-1043">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-1044">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1044">Java Option</span></span> | <span data-ttu-id="9a895-1045">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1045">Default Value</span></span> | <span data-ttu-id="9a895-1046">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a895-1047">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a895-1048">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-1049">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-1050">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a895-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a895-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a895-1052">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1052">Empty</span></span> | <span data-ttu-id="9a895-1053">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a895-1054">在 .NET 客户端中，可以通过提供给的 options 委托来`WithUrl`修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a895-1055">在 JavaScript 客户端中，可以在提供给`withUrl`的 javascript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a895-1056">在 Java 客户端中，这些选项可以`HttpHubConnectionBuilder`在从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a895-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a895-1057">其他资源</span><span class="sxs-lookup"><span data-stu-id="9a895-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9a895-1058">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9a895-1059">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9a895-1060">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9a895-1061">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在`Startup.ConfigureServices`方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="9a895-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9a895-1062">`AddJsonProtocol`方法采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9a895-1063">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings`对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9a895-1064">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="9a895-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9a895-1065">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中`Startup.ConfigureServices`使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="9a895-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9a895-1066">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9a895-1067">必须`Microsoft.Extensions.DependencyInjection`导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9a895-1068">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9a895-1069">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1069">MessagePack serialization options</span></span>

<span data-ttu-id="9a895-1070">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9a895-1071">有关更多详细信息，请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="9a895-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-1072">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9a895-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9a895-1073">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1073">Configure server options</span></span>

<span data-ttu-id="9a895-1074">下表描述了用于配置 SignalR 中心的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9a895-1075">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1075">Option</span></span> | <span data-ttu-id="9a895-1076">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1076">Default Value</span></span> | <span data-ttu-id="9a895-1077">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9a895-1078">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-1078">15 seconds</span></span> | <span data-ttu-id="9a895-1079">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9a895-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9a895-1080">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-1081">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9a895-1082">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-1082">15 seconds</span></span> | <span data-ttu-id="9a895-1083">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="9a895-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9a895-1084">更改`KeepAliveInterval`时，请更改`ServerTimeout` / `serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9a895-1085">`ServerTimeout` /建议`serverTimeoutInMilliseconds`值为值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9a895-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9a895-1086">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9a895-1086">All installed protocols</span></span> | <span data-ttu-id="9a895-1087">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="9a895-1088">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9a895-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9a895-1089">如果`true`为，则在集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9a895-1090">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9a895-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9a895-1091">可以通过在中`AddSignalR` `Startup.ConfigureServices`提供对调用的选项委托，为所有中心配置选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9a895-1092">单个集线器的选项用于替代和中`AddSignalR`提供的全局选项，可以使用<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>进行配置：</span><span class="sxs-lookup"><span data-stu-id="9a895-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9a895-1093">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9a895-1094">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9a895-1095">这些选项通过将委托传递给中`Startup.Configure`的[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)进行配置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9a895-1096">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9a895-1097">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1097">Option</span></span> | <span data-ttu-id="9a895-1098">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1098">Default Value</span></span> | <span data-ttu-id="9a895-1099">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9a895-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-1100">32 KB</span></span> | <span data-ttu-id="9a895-1101">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9a895-1102">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="9a895-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9a895-1103">从应用于 Hub 类`Authorize`的属性中自动收集的数据。</span><span class="sxs-lookup"><span data-stu-id="9a895-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9a895-1104">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9a895-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9a895-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="9a895-1105">32 KB</span></span> | <span data-ttu-id="9a895-1106">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9a895-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9a895-1107">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="9a895-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9a895-1108">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="9a895-1108">All Transports are enabled.</span></span> | <span data-ttu-id="9a895-1109">`HttpTransportType`值的位标志枚举，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9a895-1110">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-1110">See below.</span></span> | <span data-ttu-id="9a895-1111">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9a895-1112">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9a895-1112">See below.</span></span> | <span data-ttu-id="9a895-1113">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9a895-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9a895-1114">长轮询传输具有可使用`LongPolling`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9a895-1115">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1115">Option</span></span> | <span data-ttu-id="9a895-1116">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1116">Default Value</span></span> | <span data-ttu-id="9a895-1117">描述</span><span class="sxs-lookup"><span data-stu-id="9a895-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9a895-1118">90秒</span><span class="sxs-lookup"><span data-stu-id="9a895-1118">90 seconds</span></span> | <span data-ttu-id="9a895-1119">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9a895-1120">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="9a895-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9a895-1121">WebSocket 传输具有可使用`WebSockets`属性配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9a895-1122">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1122">Option</span></span> | <span data-ttu-id="9a895-1123">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1123">Default Value</span></span> | <span data-ttu-id="9a895-1124">描述</span><span class="sxs-lookup"><span data-stu-id="9a895-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9a895-1125">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-1125">5 seconds</span></span> | <span data-ttu-id="9a895-1126">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9a895-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9a895-1127">一个委托，可用于将`Sec-WebSocket-Protocol`标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="9a895-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9a895-1128">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9a895-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9a895-1129">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1129">Configure client options</span></span>

<span data-ttu-id="9a895-1130">可以在`HubConnectionBuilder`类型上配置客户端选项（在 .Net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="9a895-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9a895-1131">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类是包含生成器配置选项的内容，也是其`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9a895-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9a895-1132">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9a895-1132">Configure logging</span></span>

<span data-ttu-id="9a895-1133">使用`ConfigureLogging`方法在 .Net 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9a895-1134">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="9a895-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9a895-1135">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="9a895-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9a895-1136">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="9a895-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9a895-1137">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9a895-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9a895-1138">例如，若要启用控制台日志记录，请`Microsoft.Extensions.Logging.Console`安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9a895-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9a895-1139">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9a895-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9a895-1140">在 JavaScript 客户端中，存在`configureLogging`类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9a895-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9a895-1141">提供一个`LogLevel`值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="9a895-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9a895-1142">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="9a895-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9a895-1143">若要完全禁用日志记录`signalR.LogLevel.None` ，请`configureLogging`在方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9a895-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9a895-1144">有关日志记录的详细信息，请参阅[SignalR Diagnostics 文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9a895-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9a895-1145">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9a895-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9a895-1146">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9a895-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9a895-1147">下面的代码段演示如何将`java.util.logging`用于 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="9a895-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9a895-1148">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="9a895-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9a895-1149">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="9a895-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9a895-1150">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9a895-1150">Configure allowed transports</span></span>

<span data-ttu-id="9a895-1151">可以在`WithUrl`调用（`withUrl` JavaScript）中配置 SignalR 使用的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9a895-1152">的值的按位 "或" `HttpTransportType`可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9a895-1153">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="9a895-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="9a895-1154">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9a895-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9a895-1155">在 JavaScript 客户端中，通过在提供的选项`transport`对象上设置字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9a895-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9a895-1156">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="9a895-1156">Configure bearer authentication</span></span>

<span data-ttu-id="9a895-1157">若要将身份验证数据与 SignalR 请求一起提供`AccessTokenProvider` ，请`accessTokenFactory`使用选项（在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9a895-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9a895-1158">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用类型`Authorization`为的`Bearer`标头）。</span><span class="sxs-lookup"><span data-stu-id="9a895-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9a895-1159">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9a895-1160">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9a895-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9a895-1161">在 .NET 客户端中， `AccessTokenProvider`可使用中`WithUrl`的选项委托指定选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9a895-1162">在 JavaScript 客户端中，通过在中`accessTokenFactory` `withUrl`设置 "选项" 对象上的字段来配置访问令牌：</span><span class="sxs-lookup"><span data-stu-id="9a895-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9a895-1163">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9a895-1164">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [>的单个\<字符串](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="9a895-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9a895-1165">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9a895-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9a895-1166">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9a895-1167">用于配置超时和保持活动状态的其他选项可用于`HubConnection`对象本身：</span><span class="sxs-lookup"><span data-stu-id="9a895-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-1169">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1169">Option</span></span> | <span data-ttu-id="9a895-1170">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1170">Default value</span></span> | <span data-ttu-id="9a895-1171">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9a895-1172">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-1173">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-1173">Timeout for server activity.</span></span> <span data-ttu-id="9a895-1174">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`Closed` ，并`onclose`触发事件（在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-1175">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-1176">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9a895-1177">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-1177">15 seconds</span></span> | <span data-ttu-id="9a895-1178">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-1179">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`Closed`触发事件`onclose` （在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9a895-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9a895-1180">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-1181">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9a895-1182">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9a895-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9a895-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-1184">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1184">Option</span></span> | <span data-ttu-id="9a895-1185">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1185">Default value</span></span> | <span data-ttu-id="9a895-1186">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9a895-1187">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-1188">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-1188">Timeout for server activity.</span></span> <span data-ttu-id="9a895-1189">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onclose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9a895-1190">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-1191">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-1192">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-1193">选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1193">Option</span></span> | <span data-ttu-id="9a895-1194">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1194">Default value</span></span> | <span data-ttu-id="9a895-1195">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9a895-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9a895-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9a895-1197">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="9a895-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9a895-1198">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="9a895-1198">Timeout for server activity.</span></span> <span data-ttu-id="9a895-1199">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接`onClose` ，并触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-1200">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="9a895-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9a895-1201">建议值至少为服务器`KeepAliveInterval`值的两倍，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9a895-1202">15 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-1202">15 seconds</span></span> | <span data-ttu-id="9a895-1203">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="9a895-1204">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并`onClose`触发事件。</span><span class="sxs-lookup"><span data-stu-id="9a895-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9a895-1205">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="9a895-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9a895-1206">有关握手过程的详细信息，请参阅[SignalR Hub 协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9a895-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9a895-1207">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1207">Configure additional options</span></span>

<span data-ttu-id="9a895-1208">可在上`WithUrl`的（`withUrl` JavaScript）方法中`HubConnectionBuilder`或在 Java 客户端`HttpHubConnectionBuilder`中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9a895-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="9a895-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9a895-1210">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1210">.NET Option</span></span> |  <span data-ttu-id="9a895-1211">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1211">Default value</span></span> | <span data-ttu-id="9a895-1212">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9a895-1213">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9a895-1214">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-1215">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-1216">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9a895-1217">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1217">Empty</span></span> | <span data-ttu-id="9a895-1218">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9a895-1219">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1219">Empty</span></span> | <span data-ttu-id="9a895-1220">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="9a895-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9a895-1221">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1221">Empty</span></span> | <span data-ttu-id="9a895-1222">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9a895-1223">5 秒</span><span class="sxs-lookup"><span data-stu-id="9a895-1223">5 seconds</span></span> | <span data-ttu-id="9a895-1224">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="9a895-1224">WebSockets only.</span></span> <span data-ttu-id="9a895-1225">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="9a895-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9a895-1226">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9a895-1227">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1227">Empty</span></span> | <span data-ttu-id="9a895-1228">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9a895-1229">一个委托，可用于配置或替换用于发送 HTTP `HttpMessageHandler`请求的。</span><span class="sxs-lookup"><span data-stu-id="9a895-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9a895-1230">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="9a895-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="9a895-1231">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="9a895-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9a895-1232">修改该默认值的设置并将其返回，或返回一个新`HttpMessageHandler`的实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9a895-1233">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9a895-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9a895-1234">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9a895-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9a895-1235">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9a895-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9a895-1236">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9a895-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9a895-1237">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9a895-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9a895-1238">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="9a895-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9a895-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9a895-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9a895-1240">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1240">JavaScript Option</span></span> | <span data-ttu-id="9a895-1241">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1241">Default Value</span></span> | <span data-ttu-id="9a895-1242">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9a895-1243">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9a895-1244">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-1245">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-1246">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9a895-1247">Java</span><span class="sxs-lookup"><span data-stu-id="9a895-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="9a895-1248">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9a895-1248">Java Option</span></span> | <span data-ttu-id="9a895-1249">默认值</span><span class="sxs-lookup"><span data-stu-id="9a895-1249">Default Value</span></span> | <span data-ttu-id="9a895-1250">说明</span><span class="sxs-lookup"><span data-stu-id="9a895-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9a895-1251">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="9a895-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9a895-1252">将此设置`true`为以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9a895-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9a895-1253">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9a895-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9a895-1254">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9a895-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9a895-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9a895-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9a895-1256">空</span><span class="sxs-lookup"><span data-stu-id="9a895-1256">Empty</span></span> | <span data-ttu-id="9a895-1257">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9a895-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9a895-1258">在 .NET 客户端中，可以通过提供给的 options 委托来`WithUrl`修改这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9a895-1259">在 JavaScript 客户端中，可以在提供给`withUrl`的 javascript 对象中提供这些选项：</span><span class="sxs-lookup"><span data-stu-id="9a895-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9a895-1260">在 Java 客户端中，这些选项可以`HttpHubConnectionBuilder`在从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9a895-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9a895-1261">其他资源</span><span class="sxs-lookup"><span data-stu-id="9a895-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
