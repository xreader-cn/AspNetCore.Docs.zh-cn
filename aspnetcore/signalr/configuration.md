---
title: ASP.NET核心SignalR配置
author: bradygaster
description: 了解如何配置ASP.NET核心SignalR应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228135"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="9b2cd-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="9b2cd-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9b2cd-104">JSON/消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9b2cd-105">ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9b2cd-106">每个协议都有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9b2cd-107">JSON 序列化可以使用[AddJson 协议](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9b2cd-108">`AddJsonProtocol`可以在`Startup.ConfigureServices`[中添加SignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9b2cd-109">该方法`AddJsonProtocol`采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9b2cd-110">该对象的[Payload 序列化器选项](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是`System.Text.Json`<xref:System.Text.Json.JsonSerializerOptions>可用于配置参数和返回值序列化的对象。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9b2cd-111">有关详细信息，请参阅[系统.Text.Json 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9b2cd-112">例如，要将序列化程序配置为不更改属性名称的大小写，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="9b2cd-113">在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9b2cd-114">必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9b2cd-115">此时无法在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9b2cd-116">切换到牛顿软. Json</span><span class="sxs-lookup"><span data-stu-id="9b2cd-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9b2cd-117">如果您需要在 中不支持`Newtonsoft.Json`的功能`System.Text.Json`，请参阅[切换到 Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9b2cd-118">消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-118">MessagePack serialization options</span></span>

<span data-ttu-id="9b2cd-119">消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9b2cd-120">有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2cd-121">此时无法在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9b2cd-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-122">Configure server options</span></span>

<span data-ttu-id="9b2cd-123">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9b2cd-124">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-124">Option</span></span> | <span data-ttu-id="9b2cd-125">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-125">Default Value</span></span> | <span data-ttu-id="9b2cd-126">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9b2cd-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-127">30 seconds</span></span> | <span data-ttu-id="9b2cd-128">如果客户端未在此间隔内收到消息（包括保持活动状态），服务器将考虑客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9b2cd-129">由于如何实现，实际将客户端标记为断开连接的时间可能比此超时间隔长。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9b2cd-130">建议的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9b2cd-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-131">15 seconds</span></span> | <span data-ttu-id="9b2cd-132">如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9b2cd-133">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-134">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b2cd-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-135">15 seconds</span></span> | <span data-ttu-id="9b2cd-136">如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9b2cd-137">更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9b2cd-138">`ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9b2cd-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9b2cd-139">All installed protocols</span></span> | <span data-ttu-id="9b2cd-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-140">Protocols supported by this hub.</span></span> <span data-ttu-id="9b2cd-141">默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9b2cd-142">如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9b2cd-143">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9b2cd-144">可缓冲客户端上载流的最大项数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9b2cd-145">如果达到此限制，则在服务器处理流项之前将阻止调用的处理。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9b2cd-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-146">32 KB</span></span> | <span data-ttu-id="9b2cd-147">单个传入中心消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9b2cd-148">可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9b2cd-149">单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9b2cd-150">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9b2cd-151">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9b2cd-152">这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)在`Startup.Configure`中配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9b2cd-153">下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9b2cd-154">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-154">Option</span></span> | <span data-ttu-id="9b2cd-155">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-155">Default Value</span></span> | <span data-ttu-id="9b2cd-156">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9b2cd-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-157">32 KB</span></span> | <span data-ttu-id="9b2cd-158">在应用背压之前，服务器缓冲的客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9b2cd-159">增加此值允许服务器在不施加背压的情况下更快地接收更大的消息，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9b2cd-160">数据自动从应用于中心`Authorize`类的属性中收集。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9b2cd-161">用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9b2cd-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-162">32 KB</span></span> | <span data-ttu-id="9b2cd-163">在观察背压之前，服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9b2cd-164">增加此值允许服务器更快地缓冲较大的邮件，而无需等待回压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9b2cd-165">所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-165">All Transports are enabled.</span></span> | <span data-ttu-id="9b2cd-166">位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9b2cd-167">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-167">See below.</span></span> | <span data-ttu-id="9b2cd-168">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9b2cd-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-169">See below.</span></span> | <span data-ttu-id="9b2cd-170">特定于 WebSocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="9b2cd-171">0</span><span class="sxs-lookup"><span data-stu-id="9b2cd-171">0</span></span> | <span data-ttu-id="9b2cd-172">指定协商协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="9b2cd-173">这用于将客户端限制为较新版本。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="9b2cd-174">长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9b2cd-175">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-175">Option</span></span> | <span data-ttu-id="9b2cd-176">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-176">Default Value</span></span> | <span data-ttu-id="9b2cd-177">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9b2cd-178">90 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-178">90 seconds</span></span> | <span data-ttu-id="9b2cd-179">服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9b2cd-180">减小此值会导致客户端更频繁地发出新的轮询请求。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9b2cd-181">WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9b2cd-182">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-182">Option</span></span> | <span data-ttu-id="9b2cd-183">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-183">Default Value</span></span> | <span data-ttu-id="9b2cd-184">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9b2cd-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-185">5 seconds</span></span> | <span data-ttu-id="9b2cd-186">服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9b2cd-187">可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9b2cd-188">委托接收客户端请求的值作为输入，并预期返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9b2cd-189">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-189">Configure client options</span></span>

<span data-ttu-id="9b2cd-190">可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9b2cd-191">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9b2cd-192">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9b2cd-192">Configure logging</span></span>

<span data-ttu-id="9b2cd-193">使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9b2cd-194">日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9b2cd-195">有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2cd-196">要注册日志记录提供程序，必须安装必要的包。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9b2cd-197">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9b2cd-198">例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9b2cd-199">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9b2cd-200">在 JavaScript 客户端中，`configureLogging`存在类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9b2cd-201">提供指示`LogLevel`要生产的日志消息的最小级别的值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9b2cd-202">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9b2cd-203">还可以提供表示`LogLevel`日志级别名称`string`的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9b2cd-204">这在无法访问`LogLevel`常量的环境中配置 SignalR 日志记录时非常有用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9b2cd-205">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-205">The following table lists the available log levels.</span></span> <span data-ttu-id="9b2cd-206">您提供的值来`configureLogging`设置将记录的**最小**日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9b2cd-207">将记录在此级别记录的消息，**或表中列出的消息级别**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9b2cd-208">字符串</span><span class="sxs-lookup"><span data-stu-id="9b2cd-208">String</span></span>                      | <span data-ttu-id="9b2cd-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9b2cd-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9b2cd-210">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9b2cd-211">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9b2cd-212">要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9b2cd-213">有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9b2cd-214">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9b2cd-215">它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9b2cd-216">以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9b2cd-217">如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9b2cd-218">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9b2cd-219">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9b2cd-219">Configure allowed transports</span></span>

<span data-ttu-id="9b2cd-220">SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9b2cd-221">值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9b2cd-222">默认情况下，所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-222">All transports are enabled by default.</span></span>

<span data-ttu-id="9b2cd-223">例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9b2cd-224">在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9b2cd-225">在此版本中，Java 客户端 Websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9b2cd-226">在 Java 客户端中，使用 上`withTransport`的方法选择传输。 `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9b2cd-227">Java 客户端默认使用 WebSocket 传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9b2cd-228">SignalR Java 客户端还不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9b2cd-229">配置无记名身份验证</span><span class="sxs-lookup"><span data-stu-id="9b2cd-229">Configure bearer authentication</span></span>

<span data-ttu-id="9b2cd-230">要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9b2cd-231">在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9b2cd-232">在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9b2cd-233">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9b2cd-234">在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9b2cd-235">在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9b2cd-236">在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9b2cd-237">[与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="9b2cd-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9b2cd-238">调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9b2cd-239">配置超时和保持活动选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9b2cd-240">`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b2cd-241">.NET</span><span class="sxs-lookup"><span data-stu-id="9b2cd-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b2cd-242">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-242">Option</span></span> | <span data-ttu-id="9b2cd-243">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-243">Default value</span></span> | <span data-ttu-id="9b2cd-244">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9b2cd-245">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-246">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-246">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-247">如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b2cd-248">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-249">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9b2cd-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-250">15 seconds</span></span> | <span data-ttu-id="9b2cd-251">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b2cd-252">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b2cd-253">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-254">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b2cd-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-255">15 seconds</span></span> | <span data-ttu-id="9b2cd-256">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-257">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-258">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9b2cd-259">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9b2cd-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b2cd-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b2cd-261">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-261">Option</span></span> | <span data-ttu-id="9b2cd-262">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-262">Default value</span></span> | <span data-ttu-id="9b2cd-263">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9b2cd-264">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-265">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-265">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-266">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9b2cd-267">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-268">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9b2cd-269">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-270">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-271">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-272">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b2cd-273">Java</span><span class="sxs-lookup"><span data-stu-id="9b2cd-273">Java</span></span>](#tab/java)

| <span data-ttu-id="9b2cd-274">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-274">Option</span></span> | <span data-ttu-id="9b2cd-275">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-275">Default value</span></span> | <span data-ttu-id="9b2cd-276">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9b2cd-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9b2cd-278">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-279">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-279">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-280">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9b2cd-281">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-282">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9b2cd-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-283">15 seconds</span></span> | <span data-ttu-id="9b2cd-284">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b2cd-285">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9b2cd-286">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-287">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9b2cd-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9b2cd-289">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-290">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-291">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-292">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9b2cd-293">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-293">Configure additional options</span></span>

<span data-ttu-id="9b2cd-294">其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b2cd-295">.NET</span><span class="sxs-lookup"><span data-stu-id="9b2cd-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b2cd-296">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-296">.NET Option</span></span> |  <span data-ttu-id="9b2cd-297">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-297">Default value</span></span> | <span data-ttu-id="9b2cd-298">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9b2cd-299">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9b2cd-300">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-301">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-302">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9b2cd-303">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-303">Empty</span></span> | <span data-ttu-id="9b2cd-304">要发送到身份验证请求的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9b2cd-305">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-305">Empty</span></span> | <span data-ttu-id="9b2cd-306">要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9b2cd-307">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-307">Empty</span></span> | <span data-ttu-id="9b2cd-308">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9b2cd-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-309">5 seconds</span></span> | <span data-ttu-id="9b2cd-310">仅限 Web 插座。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-310">WebSockets only.</span></span> <span data-ttu-id="9b2cd-311">客户端关闭后等待服务器确认关闭请求的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9b2cd-312">如果服务器在此时间内未确认关闭，则客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9b2cd-313">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-313">Empty</span></span> | <span data-ttu-id="9b2cd-314">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9b2cd-315">可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9b2cd-316">不用于 Web 套接字连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="9b2cd-317">此委托必须返回一个非空值，并且它将作为参数接收默认值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9b2cd-318">修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9b2cd-319">**替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9b2cd-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9b2cd-320">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9b2cd-321">设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9b2cd-322">这支持使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9b2cd-323">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9b2cd-324">接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9b2cd-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b2cd-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b2cd-326">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-326">JavaScript Option</span></span> | <span data-ttu-id="9b2cd-327">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-327">Default Value</span></span> | <span data-ttu-id="9b2cd-328">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9b2cd-329">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9b2cd-330">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-331">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-332">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b2cd-333">Java</span><span class="sxs-lookup"><span data-stu-id="9b2cd-333">Java</span></span>](#tab/java)

| <span data-ttu-id="9b2cd-334">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-334">Java Option</span></span> | <span data-ttu-id="9b2cd-335">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-335">Default Value</span></span> | <span data-ttu-id="9b2cd-336">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9b2cd-337">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9b2cd-338">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-339">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-340">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9b2cd-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9b2cd-342">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-342">Empty</span></span> | <span data-ttu-id="9b2cd-343">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9b2cd-344">在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9b2cd-345">在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9b2cd-346">在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9b2cd-347">其他资源</span><span class="sxs-lookup"><span data-stu-id="9b2cd-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9b2cd-348">JSON/消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9b2cd-349">ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9b2cd-350">每个协议都有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9b2cd-351">JSON 序列化可以使用[AddJson 协议](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9b2cd-352">`AddJsonProtocol`可以在`Startup.ConfigureServices`[中添加SignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9b2cd-353">该方法`AddJsonProtocol`采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9b2cd-354">该对象的[Payload 序列化器选项](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是`System.Text.Json`<xref:System.Text.Json.JsonSerializerOptions>可用于配置参数和返回值序列化的对象。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9b2cd-355">有关详细信息，请参阅[系统.Text.Json 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9b2cd-356">例如，要将序列化程序配置为不更改属性名称的大小写，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="9b2cd-357">在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9b2cd-358">必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9b2cd-359">此时无法在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9b2cd-360">切换到牛顿软. Json</span><span class="sxs-lookup"><span data-stu-id="9b2cd-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9b2cd-361">如果您需要在 中不支持`Newtonsoft.Json`的功能`System.Text.Json`，请参阅[切换到 Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9b2cd-362">消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-362">MessagePack serialization options</span></span>

<span data-ttu-id="9b2cd-363">消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9b2cd-364">有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2cd-365">此时无法在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9b2cd-366">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-366">Configure server options</span></span>

<span data-ttu-id="9b2cd-367">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9b2cd-368">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-368">Option</span></span> | <span data-ttu-id="9b2cd-369">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-369">Default Value</span></span> | <span data-ttu-id="9b2cd-370">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9b2cd-371">30 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-371">30 seconds</span></span> | <span data-ttu-id="9b2cd-372">如果客户端未在此间隔内收到消息（包括保持活动状态），服务器将考虑客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9b2cd-373">由于如何实现，实际将客户端标记为断开连接的时间可能比此超时间隔长。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9b2cd-374">建议的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9b2cd-375">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-375">15 seconds</span></span> | <span data-ttu-id="9b2cd-376">如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9b2cd-377">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-378">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b2cd-379">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-379">15 seconds</span></span> | <span data-ttu-id="9b2cd-380">如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9b2cd-381">更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9b2cd-382">`ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9b2cd-383">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9b2cd-383">All installed protocols</span></span> | <span data-ttu-id="9b2cd-384">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-384">Protocols supported by this hub.</span></span> <span data-ttu-id="9b2cd-385">默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9b2cd-386">如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9b2cd-387">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9b2cd-388">可缓冲客户端上载流的最大项数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9b2cd-389">如果达到此限制，则在服务器处理流项之前将阻止调用的处理。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9b2cd-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-390">32 KB</span></span> | <span data-ttu-id="9b2cd-391">单个传入中心消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9b2cd-392">可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9b2cd-393">单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9b2cd-394">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9b2cd-395">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9b2cd-396">这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)在`Startup.Configure`中配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9b2cd-397">下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9b2cd-398">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-398">Option</span></span> | <span data-ttu-id="9b2cd-399">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-399">Default Value</span></span> | <span data-ttu-id="9b2cd-400">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9b2cd-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-401">32 KB</span></span> | <span data-ttu-id="9b2cd-402">在应用背压之前，服务器缓冲的客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9b2cd-403">增加此值允许服务器在不施加背压的情况下更快地接收更大的消息，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9b2cd-404">数据自动从应用于中心`Authorize`类的属性中收集。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9b2cd-405">用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9b2cd-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-406">32 KB</span></span> | <span data-ttu-id="9b2cd-407">在观察背压之前，服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9b2cd-408">增加此值允许服务器更快地缓冲较大的邮件，而无需等待回压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9b2cd-409">所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-409">All Transports are enabled.</span></span> | <span data-ttu-id="9b2cd-410">位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9b2cd-411">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-411">See below.</span></span> | <span data-ttu-id="9b2cd-412">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9b2cd-413">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-413">See below.</span></span> | <span data-ttu-id="9b2cd-414">特定于 WebSocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9b2cd-415">长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9b2cd-416">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-416">Option</span></span> | <span data-ttu-id="9b2cd-417">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-417">Default Value</span></span> | <span data-ttu-id="9b2cd-418">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9b2cd-419">90 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-419">90 seconds</span></span> | <span data-ttu-id="9b2cd-420">服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9b2cd-421">减小此值会导致客户端更频繁地发出新的轮询请求。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9b2cd-422">WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9b2cd-423">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-423">Option</span></span> | <span data-ttu-id="9b2cd-424">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-424">Default Value</span></span> | <span data-ttu-id="9b2cd-425">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9b2cd-426">5 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-426">5 seconds</span></span> | <span data-ttu-id="9b2cd-427">服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9b2cd-428">可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9b2cd-429">委托接收客户端请求的值作为输入，并预期返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9b2cd-430">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-430">Configure client options</span></span>

<span data-ttu-id="9b2cd-431">可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9b2cd-432">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9b2cd-433">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9b2cd-433">Configure logging</span></span>

<span data-ttu-id="9b2cd-434">使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9b2cd-435">日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9b2cd-436">有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2cd-437">要注册日志记录提供程序，必须安装必要的包。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9b2cd-438">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9b2cd-439">例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9b2cd-440">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9b2cd-441">在 JavaScript 客户端中，`configureLogging`存在类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9b2cd-442">提供指示`LogLevel`要生产的日志消息的最小级别的值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9b2cd-443">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9b2cd-444">还可以提供表示`LogLevel`日志级别名称`string`的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9b2cd-445">这在无法访问`LogLevel`常量的环境中配置 SignalR 日志记录时非常有用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9b2cd-446">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-446">The following table lists the available log levels.</span></span> <span data-ttu-id="9b2cd-447">您提供的值来`configureLogging`设置将记录的**最小**日志级别。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9b2cd-448">将记录在此级别记录的消息，**或表中列出的消息级别**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9b2cd-449">字符串</span><span class="sxs-lookup"><span data-stu-id="9b2cd-449">String</span></span>                      | <span data-ttu-id="9b2cd-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9b2cd-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9b2cd-451">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9b2cd-452">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9b2cd-453">要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9b2cd-454">有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9b2cd-455">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9b2cd-456">它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9b2cd-457">以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9b2cd-458">如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9b2cd-459">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9b2cd-460">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9b2cd-460">Configure allowed transports</span></span>

<span data-ttu-id="9b2cd-461">SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9b2cd-462">值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9b2cd-463">默认情况下，所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-463">All transports are enabled by default.</span></span>

<span data-ttu-id="9b2cd-464">例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9b2cd-465">在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9b2cd-466">在此版本中，Java 客户端 Websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9b2cd-467">在 Java 客户端中，使用 上`withTransport`的方法选择传输。 `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9b2cd-468">Java 客户端默认使用 WebSocket 传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9b2cd-469">SignalR Java 客户端还不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9b2cd-470">配置无记名身份验证</span><span class="sxs-lookup"><span data-stu-id="9b2cd-470">Configure bearer authentication</span></span>

<span data-ttu-id="9b2cd-471">要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9b2cd-472">在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9b2cd-473">在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9b2cd-474">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9b2cd-475">在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9b2cd-476">在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9b2cd-477">在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9b2cd-478">[与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="9b2cd-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9b2cd-479">调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9b2cd-480">配置超时和保持活动选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9b2cd-481">`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b2cd-482">.NET</span><span class="sxs-lookup"><span data-stu-id="9b2cd-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b2cd-483">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-483">Option</span></span> | <span data-ttu-id="9b2cd-484">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-484">Default value</span></span> | <span data-ttu-id="9b2cd-485">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9b2cd-486">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-487">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-487">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-488">如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b2cd-489">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-490">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9b2cd-491">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-491">15 seconds</span></span> | <span data-ttu-id="9b2cd-492">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b2cd-493">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b2cd-494">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-495">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b2cd-496">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-496">15 seconds</span></span> | <span data-ttu-id="9b2cd-497">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-498">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-499">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9b2cd-500">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9b2cd-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b2cd-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b2cd-502">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-502">Option</span></span> | <span data-ttu-id="9b2cd-503">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-503">Default value</span></span> | <span data-ttu-id="9b2cd-504">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9b2cd-505">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-506">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-506">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-507">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9b2cd-508">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-509">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9b2cd-510">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-511">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-512">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-513">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b2cd-514">Java</span><span class="sxs-lookup"><span data-stu-id="9b2cd-514">Java</span></span>](#tab/java)

| <span data-ttu-id="9b2cd-515">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-515">Option</span></span> | <span data-ttu-id="9b2cd-516">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-516">Default value</span></span> | <span data-ttu-id="9b2cd-517">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9b2cd-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9b2cd-519">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-520">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-520">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-521">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9b2cd-522">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-523">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9b2cd-524">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-524">15 seconds</span></span> | <span data-ttu-id="9b2cd-525">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b2cd-526">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9b2cd-527">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-528">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9b2cd-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9b2cd-530">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-531">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-532">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-533">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9b2cd-534">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-534">Configure additional options</span></span>

<span data-ttu-id="9b2cd-535">其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b2cd-536">.NET</span><span class="sxs-lookup"><span data-stu-id="9b2cd-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b2cd-537">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-537">.NET Option</span></span> |  <span data-ttu-id="9b2cd-538">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-538">Default value</span></span> | <span data-ttu-id="9b2cd-539">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9b2cd-540">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9b2cd-541">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-542">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-543">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9b2cd-544">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-544">Empty</span></span> | <span data-ttu-id="9b2cd-545">要发送到身份验证请求的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9b2cd-546">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-546">Empty</span></span> | <span data-ttu-id="9b2cd-547">要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9b2cd-548">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-548">Empty</span></span> | <span data-ttu-id="9b2cd-549">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9b2cd-550">5 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-550">5 seconds</span></span> | <span data-ttu-id="9b2cd-551">仅限 Web 插座。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-551">WebSockets only.</span></span> <span data-ttu-id="9b2cd-552">客户端关闭后等待服务器确认关闭请求的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9b2cd-553">如果服务器在此时间内未确认关闭，则客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9b2cd-554">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-554">Empty</span></span> | <span data-ttu-id="9b2cd-555">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9b2cd-556">可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9b2cd-557">不用于 Web 套接字连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="9b2cd-558">此委托必须返回一个非空值，并且它将作为参数接收默认值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9b2cd-559">修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9b2cd-560">**替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9b2cd-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9b2cd-561">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9b2cd-562">设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9b2cd-563">这支持使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9b2cd-564">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9b2cd-565">接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9b2cd-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b2cd-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b2cd-567">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-567">JavaScript Option</span></span> | <span data-ttu-id="9b2cd-568">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-568">Default Value</span></span> | <span data-ttu-id="9b2cd-569">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9b2cd-570">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9b2cd-571">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-572">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-573">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b2cd-574">Java</span><span class="sxs-lookup"><span data-stu-id="9b2cd-574">Java</span></span>](#tab/java)

| <span data-ttu-id="9b2cd-575">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-575">Java Option</span></span> | <span data-ttu-id="9b2cd-576">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-576">Default Value</span></span> | <span data-ttu-id="9b2cd-577">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9b2cd-578">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9b2cd-579">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-580">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-581">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9b2cd-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9b2cd-583">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-583">Empty</span></span> | <span data-ttu-id="9b2cd-584">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9b2cd-585">在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9b2cd-586">在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9b2cd-587">在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9b2cd-588">其他资源</span><span class="sxs-lookup"><span data-stu-id="9b2cd-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9b2cd-589">JSON/消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9b2cd-590">ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9b2cd-591">每个协议都有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9b2cd-592">JSON 序列化可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置，该方法可以在方法中的`Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9b2cd-593">该方法`AddJsonProtocol`采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9b2cd-594">该对象的[Payload 序列化器设置](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于`JsonSerializerSettings`配置参数和返回值序列化JSON.NET对象。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9b2cd-595">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9b2cd-596">例如，要将序列化程序配置为使用"PascalCase"属性名称，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9b2cd-597">在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9b2cd-598">必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9b2cd-599">此时无法在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9b2cd-600">消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-600">MessagePack serialization options</span></span>

<span data-ttu-id="9b2cd-601">消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9b2cd-602">有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2cd-603">此时无法在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9b2cd-604">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-604">Configure server options</span></span>

<span data-ttu-id="9b2cd-605">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9b2cd-606">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-606">Option</span></span> | <span data-ttu-id="9b2cd-607">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-607">Default Value</span></span> | <span data-ttu-id="9b2cd-608">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9b2cd-609">30 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-609">30 seconds</span></span> | <span data-ttu-id="9b2cd-610">如果客户端未在此间隔内收到消息（包括保持活动状态），服务器将考虑客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9b2cd-611">由于如何实现，实际将客户端标记为断开连接的时间可能比此超时间隔长。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9b2cd-612">建议的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9b2cd-613">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-613">15 seconds</span></span> | <span data-ttu-id="9b2cd-614">如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9b2cd-615">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-616">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b2cd-617">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-617">15 seconds</span></span> | <span data-ttu-id="9b2cd-618">如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9b2cd-619">更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9b2cd-620">`ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9b2cd-621">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9b2cd-621">All installed protocols</span></span> | <span data-ttu-id="9b2cd-622">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-622">Protocols supported by this hub.</span></span> <span data-ttu-id="9b2cd-623">默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9b2cd-624">如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9b2cd-625">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9b2cd-626">可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9b2cd-627">单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9b2cd-628">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9b2cd-629">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9b2cd-630">这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)在`Startup.Configure`中配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9b2cd-631">下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9b2cd-632">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-632">Option</span></span> | <span data-ttu-id="9b2cd-633">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-633">Default Value</span></span> | <span data-ttu-id="9b2cd-634">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9b2cd-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-635">32 KB</span></span> | <span data-ttu-id="9b2cd-636">从服务器缓冲的客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9b2cd-637">增加此值允许服务器接收较大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9b2cd-638">数据自动从应用于中心`Authorize`类的属性中收集。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9b2cd-639">用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9b2cd-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-640">32 KB</span></span> | <span data-ttu-id="9b2cd-641">服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9b2cd-642">增加此值允许服务器发送更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9b2cd-643">所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-643">All Transports are enabled.</span></span> | <span data-ttu-id="9b2cd-644">位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9b2cd-645">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-645">See below.</span></span> | <span data-ttu-id="9b2cd-646">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9b2cd-647">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-647">See below.</span></span> | <span data-ttu-id="9b2cd-648">特定于 WebSocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9b2cd-649">长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9b2cd-650">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-650">Option</span></span> | <span data-ttu-id="9b2cd-651">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-651">Default Value</span></span> | <span data-ttu-id="9b2cd-652">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9b2cd-653">90 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-653">90 seconds</span></span> | <span data-ttu-id="9b2cd-654">服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9b2cd-655">减小此值会导致客户端更频繁地发出新的轮询请求。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9b2cd-656">WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9b2cd-657">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-657">Option</span></span> | <span data-ttu-id="9b2cd-658">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-658">Default Value</span></span> | <span data-ttu-id="9b2cd-659">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9b2cd-660">5 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-660">5 seconds</span></span> | <span data-ttu-id="9b2cd-661">服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9b2cd-662">可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9b2cd-663">委托接收客户端请求的值作为输入，并预期返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9b2cd-664">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-664">Configure client options</span></span>

<span data-ttu-id="9b2cd-665">可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9b2cd-666">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9b2cd-667">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9b2cd-667">Configure logging</span></span>

<span data-ttu-id="9b2cd-668">使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9b2cd-669">日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9b2cd-670">有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2cd-671">要注册日志记录提供程序，必须安装必要的包。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9b2cd-672">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9b2cd-673">例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9b2cd-674">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9b2cd-675">在 JavaScript 客户端中，`configureLogging`存在类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9b2cd-676">提供指示`LogLevel`要生产的日志消息的最小级别的值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9b2cd-677">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9b2cd-678">要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9b2cd-679">有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9b2cd-680">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9b2cd-681">它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9b2cd-682">以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9b2cd-683">如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9b2cd-684">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9b2cd-685">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9b2cd-685">Configure allowed transports</span></span>

<span data-ttu-id="9b2cd-686">SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9b2cd-687">值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9b2cd-688">默认情况下，所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-688">All transports are enabled by default.</span></span>

<span data-ttu-id="9b2cd-689">例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9b2cd-690">在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9b2cd-691">在此版本中，Java 客户端 Websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9b2cd-692">配置无记名身份验证</span><span class="sxs-lookup"><span data-stu-id="9b2cd-692">Configure bearer authentication</span></span>

<span data-ttu-id="9b2cd-693">要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9b2cd-694">在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9b2cd-695">在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9b2cd-696">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9b2cd-697">在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9b2cd-698">在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9b2cd-699">在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9b2cd-700">[与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="9b2cd-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9b2cd-701">调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9b2cd-702">配置超时和保持活动选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9b2cd-703">`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b2cd-704">.NET</span><span class="sxs-lookup"><span data-stu-id="9b2cd-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b2cd-705">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-705">Option</span></span> | <span data-ttu-id="9b2cd-706">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-706">Default value</span></span> | <span data-ttu-id="9b2cd-707">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9b2cd-708">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-709">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-709">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-710">如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b2cd-711">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-712">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9b2cd-713">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-713">15 seconds</span></span> | <span data-ttu-id="9b2cd-714">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b2cd-715">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b2cd-716">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-717">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b2cd-718">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-718">15 seconds</span></span> | <span data-ttu-id="9b2cd-719">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-720">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-721">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9b2cd-722">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9b2cd-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b2cd-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b2cd-724">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-724">Option</span></span> | <span data-ttu-id="9b2cd-725">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-725">Default value</span></span> | <span data-ttu-id="9b2cd-726">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9b2cd-727">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-728">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-728">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-729">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9b2cd-730">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-731">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9b2cd-732">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-733">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-734">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-735">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b2cd-736">Java</span><span class="sxs-lookup"><span data-stu-id="9b2cd-736">Java</span></span>](#tab/java)

| <span data-ttu-id="9b2cd-737">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-737">Option</span></span> | <span data-ttu-id="9b2cd-738">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-738">Default value</span></span> | <span data-ttu-id="9b2cd-739">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9b2cd-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9b2cd-741">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-742">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-742">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-743">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9b2cd-744">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-745">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9b2cd-746">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-746">15 seconds</span></span> | <span data-ttu-id="9b2cd-747">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b2cd-748">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9b2cd-749">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-750">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9b2cd-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9b2cd-752">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-753">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9b2cd-754">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9b2cd-755">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9b2cd-756">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-756">Configure additional options</span></span>

<span data-ttu-id="9b2cd-757">其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b2cd-758">.NET</span><span class="sxs-lookup"><span data-stu-id="9b2cd-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b2cd-759">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-759">.NET Option</span></span> |  <span data-ttu-id="9b2cd-760">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-760">Default value</span></span> | <span data-ttu-id="9b2cd-761">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9b2cd-762">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9b2cd-763">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-764">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-765">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9b2cd-766">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-766">Empty</span></span> | <span data-ttu-id="9b2cd-767">要发送到身份验证请求的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9b2cd-768">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-768">Empty</span></span> | <span data-ttu-id="9b2cd-769">要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9b2cd-770">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-770">Empty</span></span> | <span data-ttu-id="9b2cd-771">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9b2cd-772">5 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-772">5 seconds</span></span> | <span data-ttu-id="9b2cd-773">仅限 Web 插座。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-773">WebSockets only.</span></span> <span data-ttu-id="9b2cd-774">客户端关闭后等待服务器确认关闭请求的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9b2cd-775">如果服务器在此时间内未确认关闭，则客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9b2cd-776">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-776">Empty</span></span> | <span data-ttu-id="9b2cd-777">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9b2cd-778">可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9b2cd-779">不用于 Web 套接字连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="9b2cd-780">此委托必须返回一个非空值，并且它将作为参数接收默认值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9b2cd-781">修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9b2cd-782">**替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9b2cd-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9b2cd-783">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9b2cd-784">设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9b2cd-785">这支持使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9b2cd-786">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9b2cd-787">接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9b2cd-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b2cd-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b2cd-789">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-789">JavaScript Option</span></span> | <span data-ttu-id="9b2cd-790">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-790">Default Value</span></span> | <span data-ttu-id="9b2cd-791">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9b2cd-792">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9b2cd-793">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-794">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-795">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b2cd-796">Java</span><span class="sxs-lookup"><span data-stu-id="9b2cd-796">Java</span></span>](#tab/java)

| <span data-ttu-id="9b2cd-797">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-797">Java Option</span></span> | <span data-ttu-id="9b2cd-798">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-798">Default Value</span></span> | <span data-ttu-id="9b2cd-799">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9b2cd-800">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9b2cd-801">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-802">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-803">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9b2cd-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9b2cd-805">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-805">Empty</span></span> | <span data-ttu-id="9b2cd-806">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9b2cd-807">在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9b2cd-808">在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9b2cd-809">在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9b2cd-810">其他资源</span><span class="sxs-lookup"><span data-stu-id="9b2cd-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9b2cd-811">JSON/消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9b2cd-812">ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9b2cd-813">每个协议都有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9b2cd-814">JSON 序列化可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置，该方法可以在方法中的`Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9b2cd-815">该方法`AddJsonProtocol`采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9b2cd-816">该对象的[Payload 序列化器设置](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于`JsonSerializerSettings`配置参数和返回值序列化JSON.NET对象。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9b2cd-817">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9b2cd-818">例如，要将序列化程序配置为使用"PascalCase"属性名称，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9b2cd-819">在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9b2cd-820">必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9b2cd-821">此时无法在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9b2cd-822">消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-822">MessagePack serialization options</span></span>

<span data-ttu-id="9b2cd-823">消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9b2cd-824">有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2cd-825">此时无法在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9b2cd-826">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-826">Configure server options</span></span>

<span data-ttu-id="9b2cd-827">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9b2cd-828">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-828">Option</span></span> | <span data-ttu-id="9b2cd-829">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-829">Default Value</span></span> | <span data-ttu-id="9b2cd-830">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9b2cd-831">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-831">15 seconds</span></span> | <span data-ttu-id="9b2cd-832">如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9b2cd-833">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-834">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9b2cd-835">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-835">15 seconds</span></span> | <span data-ttu-id="9b2cd-836">如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9b2cd-837">更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9b2cd-838">`ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9b2cd-839">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="9b2cd-839">All installed protocols</span></span> | <span data-ttu-id="9b2cd-840">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-840">Protocols supported by this hub.</span></span> <span data-ttu-id="9b2cd-841">默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9b2cd-842">如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9b2cd-843">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9b2cd-844">可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9b2cd-845">单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9b2cd-846">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9b2cd-847">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9b2cd-848">这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)在`Startup.Configure`中配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9b2cd-849">下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9b2cd-850">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-850">Option</span></span> | <span data-ttu-id="9b2cd-851">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-851">Default Value</span></span> | <span data-ttu-id="9b2cd-852">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9b2cd-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-853">32 KB</span></span> | <span data-ttu-id="9b2cd-854">从服务器缓冲的客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9b2cd-855">增加此值允许服务器接收较大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9b2cd-856">数据自动从应用于中心`Authorize`类的属性中收集。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9b2cd-857">用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9b2cd-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b2cd-858">32 KB</span></span> | <span data-ttu-id="9b2cd-859">服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9b2cd-860">增加此值允许服务器发送更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9b2cd-861">所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-861">All Transports are enabled.</span></span> | <span data-ttu-id="9b2cd-862">位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9b2cd-863">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-863">See below.</span></span> | <span data-ttu-id="9b2cd-864">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9b2cd-865">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-865">See below.</span></span> | <span data-ttu-id="9b2cd-866">特定于 WebSocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9b2cd-867">长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9b2cd-868">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-868">Option</span></span> | <span data-ttu-id="9b2cd-869">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-869">Default Value</span></span> | <span data-ttu-id="9b2cd-870">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9b2cd-871">90 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-871">90 seconds</span></span> | <span data-ttu-id="9b2cd-872">服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9b2cd-873">减小此值会导致客户端更频繁地发出新的轮询请求。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9b2cd-874">WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9b2cd-875">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-875">Option</span></span> | <span data-ttu-id="9b2cd-876">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-876">Default Value</span></span> | <span data-ttu-id="9b2cd-877">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9b2cd-878">5 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-878">5 seconds</span></span> | <span data-ttu-id="9b2cd-879">服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9b2cd-880">可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9b2cd-881">委托接收客户端请求的值作为输入，并预期返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9b2cd-882">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-882">Configure client options</span></span>

<span data-ttu-id="9b2cd-883">可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9b2cd-884">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9b2cd-885">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="9b2cd-885">Configure logging</span></span>

<span data-ttu-id="9b2cd-886">使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9b2cd-887">日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9b2cd-888">有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9b2cd-889">要注册日志记录提供程序，必须安装必要的包。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9b2cd-890">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9b2cd-891">例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9b2cd-892">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9b2cd-893">在 JavaScript 客户端中，`configureLogging`存在类似的方法。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9b2cd-894">提供指示`LogLevel`要生产的日志消息的最小级别的值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9b2cd-895">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9b2cd-896">要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9b2cd-897">有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9b2cd-898">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9b2cd-899">它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9b2cd-900">以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9b2cd-901">如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9b2cd-902">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9b2cd-903">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="9b2cd-903">Configure allowed transports</span></span>

<span data-ttu-id="9b2cd-904">SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9b2cd-905">值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9b2cd-906">默认情况下，所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-906">All transports are enabled by default.</span></span>

<span data-ttu-id="9b2cd-907">例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9b2cd-908">在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9b2cd-909">配置无记名身份验证</span><span class="sxs-lookup"><span data-stu-id="9b2cd-909">Configure bearer authentication</span></span>

<span data-ttu-id="9b2cd-910">要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9b2cd-911">在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9b2cd-912">在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9b2cd-913">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9b2cd-914">在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9b2cd-915">在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9b2cd-916">在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9b2cd-917">[与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="9b2cd-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9b2cd-918">调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9b2cd-919">配置超时和保持活动选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9b2cd-920">`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b2cd-921">.NET</span><span class="sxs-lookup"><span data-stu-id="9b2cd-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b2cd-922">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-922">Option</span></span> | <span data-ttu-id="9b2cd-923">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-923">Default value</span></span> | <span data-ttu-id="9b2cd-924">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9b2cd-925">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-926">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-926">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-927">如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b2cd-928">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-929">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9b2cd-930">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-930">15 seconds</span></span> | <span data-ttu-id="9b2cd-931">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b2cd-932">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9b2cd-933">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-934">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9b2cd-935">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9b2cd-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b2cd-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b2cd-937">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-937">Option</span></span> | <span data-ttu-id="9b2cd-938">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-938">Default value</span></span> | <span data-ttu-id="9b2cd-939">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9b2cd-940">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-941">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-941">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-942">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9b2cd-943">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-944">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b2cd-945">Java</span><span class="sxs-lookup"><span data-stu-id="9b2cd-945">Java</span></span>](#tab/java)

| <span data-ttu-id="9b2cd-946">选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-946">Option</span></span> | <span data-ttu-id="9b2cd-947">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-947">Default value</span></span> | <span data-ttu-id="9b2cd-948">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9b2cd-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9b2cd-950">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="9b2cd-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9b2cd-951">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-951">Timeout for server activity.</span></span> <span data-ttu-id="9b2cd-952">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9b2cd-953">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9b2cd-954">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9b2cd-955">15 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-955">15 seconds</span></span> | <span data-ttu-id="9b2cd-956">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="9b2cd-957">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9b2cd-958">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9b2cd-959">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9b2cd-960">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-960">Configure additional options</span></span>

<span data-ttu-id="9b2cd-961">其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9b2cd-962">.NET</span><span class="sxs-lookup"><span data-stu-id="9b2cd-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9b2cd-963">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-963">.NET Option</span></span> |  <span data-ttu-id="9b2cd-964">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-964">Default value</span></span> | <span data-ttu-id="9b2cd-965">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9b2cd-966">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9b2cd-967">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-968">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-969">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9b2cd-970">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-970">Empty</span></span> | <span data-ttu-id="9b2cd-971">要发送到身份验证请求的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9b2cd-972">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-972">Empty</span></span> | <span data-ttu-id="9b2cd-973">要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9b2cd-974">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-974">Empty</span></span> | <span data-ttu-id="9b2cd-975">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9b2cd-976">5 秒</span><span class="sxs-lookup"><span data-stu-id="9b2cd-976">5 seconds</span></span> | <span data-ttu-id="9b2cd-977">仅限 Web 插座。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-977">WebSockets only.</span></span> <span data-ttu-id="9b2cd-978">客户端关闭后等待服务器确认关闭请求的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9b2cd-979">如果服务器在此时间内未确认关闭，则客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9b2cd-980">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-980">Empty</span></span> | <span data-ttu-id="9b2cd-981">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9b2cd-982">可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9b2cd-983">不用于 Web 套接字连接。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="9b2cd-984">此委托必须返回一个非空值，并且它将作为参数接收默认值。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9b2cd-985">修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9b2cd-986">**替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。**</span><span class="sxs-lookup"><span data-stu-id="9b2cd-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9b2cd-987">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9b2cd-988">设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9b2cd-989">这支持使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9b2cd-990">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9b2cd-991">接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9b2cd-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9b2cd-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9b2cd-993">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-993">JavaScript Option</span></span> | <span data-ttu-id="9b2cd-994">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-994">Default Value</span></span> | <span data-ttu-id="9b2cd-995">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9b2cd-996">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9b2cd-997">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-998">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-999">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9b2cd-1000">Java</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="9b2cd-1001">Java 选项</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1001">Java Option</span></span> | <span data-ttu-id="9b2cd-1002">默认值</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1002">Default Value</span></span> | <span data-ttu-id="9b2cd-1003">说明</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9b2cd-1004">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9b2cd-1005">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9b2cd-1006">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9b2cd-1007">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9b2cd-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9b2cd-1009">空</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1009">Empty</span></span> | <span data-ttu-id="9b2cd-1010">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9b2cd-1011">在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9b2cd-1012">在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9b2cd-1013">在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9b2cd-1014">其他资源</span><span class="sxs-lookup"><span data-stu-id="9b2cd-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
