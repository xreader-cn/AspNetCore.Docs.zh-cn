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
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223980"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="4940c-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="4940c-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4940c-104">JSON/消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="4940c-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4940c-105">ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="4940c-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4940c-106">每个协议都有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4940c-107">JSON 序列化可以使用[AddJson 协议](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="4940c-108">`AddJsonProtocol`可以在`Startup.ConfigureServices`[中添加SignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 。</span><span class="sxs-lookup"><span data-stu-id="4940c-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4940c-109">该方法`AddJsonProtocol`采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4940c-110">该对象的[Payload 序列化器选项](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是`System.Text.Json`<xref:System.Text.Json.JsonSerializerOptions>可用于配置参数和返回值序列化的对象。</span><span class="sxs-lookup"><span data-stu-id="4940c-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4940c-111">有关详细信息，请参阅[系统.Text.Json 文档](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="4940c-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="4940c-112">例如，要将序列化程序配置为不更改属性名称的大小写，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：</span><span class="sxs-lookup"><span data-stu-id="4940c-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="4940c-113">在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="4940c-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4940c-114">必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：</span><span class="sxs-lookup"><span data-stu-id="4940c-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4940c-115">此时无法在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="4940c-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="4940c-116">切换到牛顿软. Json</span><span class="sxs-lookup"><span data-stu-id="4940c-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="4940c-117">如果您需要在 中不支持`Newtonsoft.Json`的功能`System.Text.Json`，请参阅[切换到 Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="4940c-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4940c-118">消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="4940c-118">MessagePack serialization options</span></span>

<span data-ttu-id="4940c-119">消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4940c-120">有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。</span><span class="sxs-lookup"><span data-stu-id="4940c-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4940c-121">此时无法在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="4940c-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4940c-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="4940c-122">Configure server options</span></span>

<span data-ttu-id="4940c-123">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4940c-124">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-124">Option</span></span> | <span data-ttu-id="4940c-125">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-125">Default Value</span></span> | <span data-ttu-id="4940c-126">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4940c-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-127">30 seconds</span></span> | <span data-ttu-id="4940c-128">如果客户端未在此间隔内收到消息（包括保持活动状态），服务器将考虑客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4940c-129">由于如何实现，实际将客户端标记为断开连接的时间可能比此超时间隔长。</span><span class="sxs-lookup"><span data-stu-id="4940c-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4940c-130">建议的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="4940c-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4940c-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-131">15 seconds</span></span> | <span data-ttu-id="4940c-132">如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="4940c-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4940c-133">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-134">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4940c-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-135">15 seconds</span></span> | <span data-ttu-id="4940c-136">如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。</span><span class="sxs-lookup"><span data-stu-id="4940c-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4940c-137">更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4940c-138">`ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="4940c-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4940c-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="4940c-139">All installed protocols</span></span> | <span data-ttu-id="4940c-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="4940c-140">Protocols supported by this hub.</span></span> <span data-ttu-id="4940c-141">默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="4940c-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4940c-142">如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。</span><span class="sxs-lookup"><span data-stu-id="4940c-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4940c-143">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="4940c-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="4940c-144">可缓冲客户端上载流的最大项数。</span><span class="sxs-lookup"><span data-stu-id="4940c-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="4940c-145">如果达到此限制，则在服务器处理流项之前将阻止调用的处理。</span><span class="sxs-lookup"><span data-stu-id="4940c-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="4940c-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="4940c-146">32 KB</span></span> | <span data-ttu-id="4940c-147">单个传入中心消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="4940c-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="4940c-148">可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4940c-149">单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：</span><span class="sxs-lookup"><span data-stu-id="4940c-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4940c-150">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="4940c-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4940c-151">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4940c-152">这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)在`Startup.Configure`中配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4940c-153">下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4940c-154">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-154">Option</span></span> | <span data-ttu-id="4940c-155">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-155">Default Value</span></span> | <span data-ttu-id="4940c-156">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4940c-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="4940c-157">32 KB</span></span> | <span data-ttu-id="4940c-158">在应用背压之前，服务器缓冲的客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="4940c-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="4940c-159">增加此值允许服务器在不施加背压的情况下更快地接收更大的消息，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="4940c-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4940c-160">数据自动从应用于中心`Authorize`类的属性中收集。</span><span class="sxs-lookup"><span data-stu-id="4940c-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4940c-161">用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="4940c-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4940c-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="4940c-162">32 KB</span></span> | <span data-ttu-id="4940c-163">在观察背压之前，服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="4940c-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="4940c-164">增加此值允许服务器更快地缓冲较大的邮件，而无需等待回压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="4940c-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4940c-165">所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="4940c-165">All Transports are enabled.</span></span> | <span data-ttu-id="4940c-166">位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4940c-167">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="4940c-167">See below.</span></span> | <span data-ttu-id="4940c-168">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4940c-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="4940c-169">See below.</span></span> | <span data-ttu-id="4940c-170">特定于 WebSocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4940c-171">长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4940c-172">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-172">Option</span></span> | <span data-ttu-id="4940c-173">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-173">Default Value</span></span> | <span data-ttu-id="4940c-174">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4940c-175">90 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-175">90 seconds</span></span> | <span data-ttu-id="4940c-176">服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。</span><span class="sxs-lookup"><span data-stu-id="4940c-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4940c-177">减小此值会导致客户端更频繁地发出新的轮询请求。</span><span class="sxs-lookup"><span data-stu-id="4940c-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4940c-178">WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4940c-179">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-179">Option</span></span> | <span data-ttu-id="4940c-180">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-180">Default Value</span></span> | <span data-ttu-id="4940c-181">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4940c-182">5 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-182">5 seconds</span></span> | <span data-ttu-id="4940c-183">服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="4940c-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4940c-184">可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4940c-185">委托接收客户端请求的值作为输入，并预期返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="4940c-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4940c-186">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="4940c-186">Configure client options</span></span>

<span data-ttu-id="4940c-187">可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。</span><span class="sxs-lookup"><span data-stu-id="4940c-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4940c-188">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="4940c-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4940c-189">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="4940c-189">Configure logging</span></span>

<span data-ttu-id="4940c-190">使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="4940c-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4940c-191">日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。</span><span class="sxs-lookup"><span data-stu-id="4940c-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4940c-192">有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。</span><span class="sxs-lookup"><span data-stu-id="4940c-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4940c-193">要注册日志记录提供程序，必须安装必要的包。</span><span class="sxs-lookup"><span data-stu-id="4940c-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4940c-194">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="4940c-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4940c-195">例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4940c-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4940c-196">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="4940c-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4940c-197">在 JavaScript 客户端中，`configureLogging`存在类似的方法。</span><span class="sxs-lookup"><span data-stu-id="4940c-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4940c-198">提供指示`LogLevel`要生产的日志消息的最小级别的值。</span><span class="sxs-lookup"><span data-stu-id="4940c-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4940c-199">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="4940c-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="4940c-200">还可以提供表示`LogLevel`日志级别名称`string`的值，而不是值。</span><span class="sxs-lookup"><span data-stu-id="4940c-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="4940c-201">这在无法访问`LogLevel`常量的环境中配置 SignalR 日志记录时非常有用。</span><span class="sxs-lookup"><span data-stu-id="4940c-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="4940c-202">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="4940c-202">The following table lists the available log levels.</span></span> <span data-ttu-id="4940c-203">您提供的值来`configureLogging`设置将记录的**最小**日志级别。</span><span class="sxs-lookup"><span data-stu-id="4940c-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="4940c-204">将记录在此级别记录的消息，**或表中列出的消息级别**。</span><span class="sxs-lookup"><span data-stu-id="4940c-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="4940c-205">字符串</span><span class="sxs-lookup"><span data-stu-id="4940c-205">String</span></span>                      | <span data-ttu-id="4940c-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="4940c-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="4940c-207">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="4940c-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="4940c-208">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="4940c-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="4940c-209">要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。</span><span class="sxs-lookup"><span data-stu-id="4940c-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4940c-210">有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="4940c-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4940c-211">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="4940c-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4940c-212">它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="4940c-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4940c-213">以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="4940c-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4940c-214">如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="4940c-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4940c-215">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="4940c-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4940c-216">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="4940c-216">Configure allowed transports</span></span>

<span data-ttu-id="4940c-217">SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4940c-218">值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4940c-219">默认情况下，所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="4940c-219">All transports are enabled by default.</span></span>

<span data-ttu-id="4940c-220">例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="4940c-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4940c-221">在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="4940c-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4940c-222">在此版本中，Java 客户端 Websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="4940c-223">在 Java 客户端中，使用 上`withTransport`的方法选择传输。 `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="4940c-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="4940c-224">Java 客户端默认使用 WebSocket 传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4940c-225">SignalR Java 客户端还不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="4940c-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4940c-226">配置无记名身份验证</span><span class="sxs-lookup"><span data-stu-id="4940c-226">Configure bearer authentication</span></span>

<span data-ttu-id="4940c-227">要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4940c-228">在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。</span><span class="sxs-lookup"><span data-stu-id="4940c-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4940c-229">在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4940c-230">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="4940c-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4940c-231">在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4940c-232">在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="4940c-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4940c-233">在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。</span><span class="sxs-lookup"><span data-stu-id="4940c-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4940c-234">[与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="4940c-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4940c-235">调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="4940c-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4940c-236">配置超时和保持活动选项</span><span class="sxs-lookup"><span data-stu-id="4940c-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4940c-237">`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="4940c-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4940c-238">.NET</span><span class="sxs-lookup"><span data-stu-id="4940c-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4940c-239">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-239">Option</span></span> | <span data-ttu-id="4940c-240">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-240">Default value</span></span> | <span data-ttu-id="4940c-241">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4940c-242">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-243">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-243">Timeout for server activity.</span></span> <span data-ttu-id="4940c-244">如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4940c-245">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-246">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4940c-247">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-247">15 seconds</span></span> | <span data-ttu-id="4940c-248">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="4940c-249">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4940c-250">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-251">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4940c-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-252">15 seconds</span></span> | <span data-ttu-id="4940c-253">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="4940c-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4940c-254">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="4940c-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4940c-255">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4940c-256">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="4940c-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4940c-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4940c-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4940c-258">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-258">Option</span></span> | <span data-ttu-id="4940c-259">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-259">Default value</span></span> | <span data-ttu-id="4940c-260">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4940c-261">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-262">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-262">Timeout for server activity.</span></span> <span data-ttu-id="4940c-263">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4940c-264">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-265">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4940c-266">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4940c-267">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="4940c-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4940c-268">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="4940c-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4940c-269">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4940c-270">Java</span><span class="sxs-lookup"><span data-stu-id="4940c-270">Java</span></span>](#tab/java)

| <span data-ttu-id="4940c-271">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-271">Option</span></span> | <span data-ttu-id="4940c-272">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-272">Default value</span></span> | <span data-ttu-id="4940c-273">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4940c-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4940c-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4940c-275">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-276">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-276">Timeout for server activity.</span></span> <span data-ttu-id="4940c-277">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4940c-278">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-279">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4940c-280">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-280">15 seconds</span></span> | <span data-ttu-id="4940c-281">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="4940c-282">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4940c-283">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-284">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4940c-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4940c-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4940c-286">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4940c-287">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="4940c-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4940c-288">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="4940c-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4940c-289">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4940c-290">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="4940c-290">Configure additional options</span></span>

<span data-ttu-id="4940c-291">其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：</span><span class="sxs-lookup"><span data-stu-id="4940c-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4940c-292">.NET</span><span class="sxs-lookup"><span data-stu-id="4940c-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4940c-293">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-293">.NET Option</span></span> |  <span data-ttu-id="4940c-294">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-294">Default value</span></span> | <span data-ttu-id="4940c-295">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4940c-296">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4940c-297">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-298">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-299">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4940c-300">空</span><span class="sxs-lookup"><span data-stu-id="4940c-300">Empty</span></span> | <span data-ttu-id="4940c-301">要发送到身份验证请求的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="4940c-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4940c-302">空</span><span class="sxs-lookup"><span data-stu-id="4940c-302">Empty</span></span> | <span data-ttu-id="4940c-303">要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。</span><span class="sxs-lookup"><span data-stu-id="4940c-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4940c-304">空</span><span class="sxs-lookup"><span data-stu-id="4940c-304">Empty</span></span> | <span data-ttu-id="4940c-305">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="4940c-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4940c-306">5 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-306">5 seconds</span></span> | <span data-ttu-id="4940c-307">仅限 Web 插座。</span><span class="sxs-lookup"><span data-stu-id="4940c-307">WebSockets only.</span></span> <span data-ttu-id="4940c-308">客户端关闭后等待服务器确认关闭请求的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="4940c-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4940c-309">如果服务器在此时间内未确认关闭，则客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4940c-310">空</span><span class="sxs-lookup"><span data-stu-id="4940c-310">Empty</span></span> | <span data-ttu-id="4940c-311">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="4940c-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4940c-312">可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4940c-313">不用于 Web 套接字连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="4940c-314">此委托必须返回一个非空值，并且它将作为参数接收默认值。</span><span class="sxs-lookup"><span data-stu-id="4940c-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4940c-315">修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="4940c-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4940c-316">**替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。**</span><span class="sxs-lookup"><span data-stu-id="4940c-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4940c-317">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="4940c-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4940c-318">设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="4940c-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4940c-319">这支持使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="4940c-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4940c-320">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4940c-321">接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。</span><span class="sxs-lookup"><span data-stu-id="4940c-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4940c-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4940c-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4940c-323">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-323">JavaScript Option</span></span> | <span data-ttu-id="4940c-324">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-324">Default Value</span></span> | <span data-ttu-id="4940c-325">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4940c-326">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4940c-327">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-328">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-329">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4940c-330">Java</span><span class="sxs-lookup"><span data-stu-id="4940c-330">Java</span></span>](#tab/java)

| <span data-ttu-id="4940c-331">Java 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-331">Java Option</span></span> | <span data-ttu-id="4940c-332">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-332">Default Value</span></span> | <span data-ttu-id="4940c-333">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4940c-334">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4940c-335">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-336">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-337">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4940c-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4940c-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4940c-339">空</span><span class="sxs-lookup"><span data-stu-id="4940c-339">Empty</span></span> | <span data-ttu-id="4940c-340">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="4940c-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4940c-341">在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：</span><span class="sxs-lookup"><span data-stu-id="4940c-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4940c-342">在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：</span><span class="sxs-lookup"><span data-stu-id="4940c-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4940c-343">在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4940c-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4940c-344">其他资源</span><span class="sxs-lookup"><span data-stu-id="4940c-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4940c-345">JSON/消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="4940c-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4940c-346">ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="4940c-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4940c-347">每个协议都有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4940c-348">JSON 序列化可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置，该方法可以在方法中的`Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="4940c-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4940c-349">该方法`AddJsonProtocol`采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4940c-350">该对象的[Payload 序列化器设置](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于`JsonSerializerSettings`配置参数和返回值序列化JSON.NET对象。</span><span class="sxs-lookup"><span data-stu-id="4940c-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4940c-351">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="4940c-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="4940c-352">例如，要将序列化程序配置为使用"PascalCase"属性名称，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：</span><span class="sxs-lookup"><span data-stu-id="4940c-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="4940c-353">在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="4940c-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4940c-354">必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：</span><span class="sxs-lookup"><span data-stu-id="4940c-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4940c-355">此时无法在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="4940c-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4940c-356">消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="4940c-356">MessagePack serialization options</span></span>

<span data-ttu-id="4940c-357">消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4940c-358">有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。</span><span class="sxs-lookup"><span data-stu-id="4940c-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4940c-359">此时无法在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="4940c-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4940c-360">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="4940c-360">Configure server options</span></span>

<span data-ttu-id="4940c-361">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4940c-362">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-362">Option</span></span> | <span data-ttu-id="4940c-363">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-363">Default Value</span></span> | <span data-ttu-id="4940c-364">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4940c-365">30 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-365">30 seconds</span></span> | <span data-ttu-id="4940c-366">如果客户端未在此间隔内收到消息（包括保持活动状态），服务器将考虑客户端断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4940c-367">由于如何实现，实际将客户端标记为断开连接的时间可能比此超时间隔长。</span><span class="sxs-lookup"><span data-stu-id="4940c-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4940c-368">建议的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="4940c-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4940c-369">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-369">15 seconds</span></span> | <span data-ttu-id="4940c-370">如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="4940c-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4940c-371">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-372">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4940c-373">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-373">15 seconds</span></span> | <span data-ttu-id="4940c-374">如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。</span><span class="sxs-lookup"><span data-stu-id="4940c-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4940c-375">更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4940c-376">`ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="4940c-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4940c-377">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="4940c-377">All installed protocols</span></span> | <span data-ttu-id="4940c-378">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="4940c-378">Protocols supported by this hub.</span></span> <span data-ttu-id="4940c-379">默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="4940c-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4940c-380">如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。</span><span class="sxs-lookup"><span data-stu-id="4940c-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4940c-381">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="4940c-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="4940c-382">可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4940c-383">单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：</span><span class="sxs-lookup"><span data-stu-id="4940c-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4940c-384">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="4940c-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4940c-385">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4940c-386">这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)在`Startup.Configure`中配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4940c-387">下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4940c-388">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-388">Option</span></span> | <span data-ttu-id="4940c-389">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-389">Default Value</span></span> | <span data-ttu-id="4940c-390">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4940c-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="4940c-391">32 KB</span></span> | <span data-ttu-id="4940c-392">从服务器缓冲的客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="4940c-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="4940c-393">增加此值允许服务器接收较大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="4940c-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4940c-394">数据自动从应用于中心`Authorize`类的属性中收集。</span><span class="sxs-lookup"><span data-stu-id="4940c-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4940c-395">用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="4940c-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4940c-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="4940c-396">32 KB</span></span> | <span data-ttu-id="4940c-397">服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="4940c-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="4940c-398">增加此值允许服务器发送更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="4940c-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4940c-399">所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="4940c-399">All Transports are enabled.</span></span> | <span data-ttu-id="4940c-400">位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4940c-401">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="4940c-401">See below.</span></span> | <span data-ttu-id="4940c-402">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4940c-403">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="4940c-403">See below.</span></span> | <span data-ttu-id="4940c-404">特定于 WebSocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4940c-405">长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4940c-406">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-406">Option</span></span> | <span data-ttu-id="4940c-407">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-407">Default Value</span></span> | <span data-ttu-id="4940c-408">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4940c-409">90 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-409">90 seconds</span></span> | <span data-ttu-id="4940c-410">服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。</span><span class="sxs-lookup"><span data-stu-id="4940c-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4940c-411">减小此值会导致客户端更频繁地发出新的轮询请求。</span><span class="sxs-lookup"><span data-stu-id="4940c-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4940c-412">WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4940c-413">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-413">Option</span></span> | <span data-ttu-id="4940c-414">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-414">Default Value</span></span> | <span data-ttu-id="4940c-415">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4940c-416">5 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-416">5 seconds</span></span> | <span data-ttu-id="4940c-417">服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="4940c-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4940c-418">可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4940c-419">委托接收客户端请求的值作为输入，并预期返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="4940c-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4940c-420">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="4940c-420">Configure client options</span></span>

<span data-ttu-id="4940c-421">可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。</span><span class="sxs-lookup"><span data-stu-id="4940c-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4940c-422">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="4940c-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4940c-423">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="4940c-423">Configure logging</span></span>

<span data-ttu-id="4940c-424">使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="4940c-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4940c-425">日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。</span><span class="sxs-lookup"><span data-stu-id="4940c-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4940c-426">有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。</span><span class="sxs-lookup"><span data-stu-id="4940c-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4940c-427">要注册日志记录提供程序，必须安装必要的包。</span><span class="sxs-lookup"><span data-stu-id="4940c-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4940c-428">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="4940c-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4940c-429">例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4940c-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4940c-430">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="4940c-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4940c-431">在 JavaScript 客户端中，`configureLogging`存在类似的方法。</span><span class="sxs-lookup"><span data-stu-id="4940c-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4940c-432">提供指示`LogLevel`要生产的日志消息的最小级别的值。</span><span class="sxs-lookup"><span data-stu-id="4940c-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4940c-433">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="4940c-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4940c-434">要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。</span><span class="sxs-lookup"><span data-stu-id="4940c-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4940c-435">有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="4940c-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4940c-436">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="4940c-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4940c-437">它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="4940c-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4940c-438">以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="4940c-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4940c-439">如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="4940c-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4940c-440">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="4940c-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4940c-441">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="4940c-441">Configure allowed transports</span></span>

<span data-ttu-id="4940c-442">SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4940c-443">值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4940c-444">默认情况下，所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="4940c-444">All transports are enabled by default.</span></span>

<span data-ttu-id="4940c-445">例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="4940c-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4940c-446">在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="4940c-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4940c-447">在此版本中，Java 客户端 Websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4940c-448">配置无记名身份验证</span><span class="sxs-lookup"><span data-stu-id="4940c-448">Configure bearer authentication</span></span>

<span data-ttu-id="4940c-449">要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4940c-450">在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。</span><span class="sxs-lookup"><span data-stu-id="4940c-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4940c-451">在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4940c-452">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="4940c-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4940c-453">在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4940c-454">在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="4940c-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4940c-455">在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。</span><span class="sxs-lookup"><span data-stu-id="4940c-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4940c-456">[与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="4940c-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4940c-457">调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="4940c-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4940c-458">配置超时和保持活动选项</span><span class="sxs-lookup"><span data-stu-id="4940c-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4940c-459">`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="4940c-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4940c-460">.NET</span><span class="sxs-lookup"><span data-stu-id="4940c-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4940c-461">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-461">Option</span></span> | <span data-ttu-id="4940c-462">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-462">Default value</span></span> | <span data-ttu-id="4940c-463">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4940c-464">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-465">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-465">Timeout for server activity.</span></span> <span data-ttu-id="4940c-466">如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4940c-467">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-468">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4940c-469">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-469">15 seconds</span></span> | <span data-ttu-id="4940c-470">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="4940c-471">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4940c-472">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-473">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4940c-474">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-474">15 seconds</span></span> | <span data-ttu-id="4940c-475">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="4940c-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4940c-476">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="4940c-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4940c-477">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4940c-478">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="4940c-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4940c-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4940c-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4940c-480">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-480">Option</span></span> | <span data-ttu-id="4940c-481">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-481">Default value</span></span> | <span data-ttu-id="4940c-482">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4940c-483">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-484">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-484">Timeout for server activity.</span></span> <span data-ttu-id="4940c-485">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4940c-486">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-487">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4940c-488">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4940c-489">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="4940c-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4940c-490">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="4940c-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4940c-491">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4940c-492">Java</span><span class="sxs-lookup"><span data-stu-id="4940c-492">Java</span></span>](#tab/java)

| <span data-ttu-id="4940c-493">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-493">Option</span></span> | <span data-ttu-id="4940c-494">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-494">Default value</span></span> | <span data-ttu-id="4940c-495">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4940c-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4940c-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4940c-497">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-498">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-498">Timeout for server activity.</span></span> <span data-ttu-id="4940c-499">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4940c-500">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-501">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4940c-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-502">15 seconds</span></span> | <span data-ttu-id="4940c-503">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="4940c-504">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4940c-505">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-506">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4940c-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4940c-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4940c-508">15 秒（15，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4940c-509">确定客户端发送 ping 消息的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="4940c-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4940c-510">从客户端发送任何消息会将计时器重置为间隔的开始。</span><span class="sxs-lookup"><span data-stu-id="4940c-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4940c-511">如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4940c-512">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="4940c-512">Configure additional options</span></span>

<span data-ttu-id="4940c-513">其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：</span><span class="sxs-lookup"><span data-stu-id="4940c-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4940c-514">.NET</span><span class="sxs-lookup"><span data-stu-id="4940c-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4940c-515">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-515">.NET Option</span></span> |  <span data-ttu-id="4940c-516">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-516">Default value</span></span> | <span data-ttu-id="4940c-517">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4940c-518">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4940c-519">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-520">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-521">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4940c-522">空</span><span class="sxs-lookup"><span data-stu-id="4940c-522">Empty</span></span> | <span data-ttu-id="4940c-523">要发送到身份验证请求的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="4940c-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4940c-524">空</span><span class="sxs-lookup"><span data-stu-id="4940c-524">Empty</span></span> | <span data-ttu-id="4940c-525">要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。</span><span class="sxs-lookup"><span data-stu-id="4940c-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4940c-526">空</span><span class="sxs-lookup"><span data-stu-id="4940c-526">Empty</span></span> | <span data-ttu-id="4940c-527">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="4940c-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4940c-528">5 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-528">5 seconds</span></span> | <span data-ttu-id="4940c-529">仅限 Web 插座。</span><span class="sxs-lookup"><span data-stu-id="4940c-529">WebSockets only.</span></span> <span data-ttu-id="4940c-530">客户端关闭后等待服务器确认关闭请求的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="4940c-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4940c-531">如果服务器在此时间内未确认关闭，则客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4940c-532">空</span><span class="sxs-lookup"><span data-stu-id="4940c-532">Empty</span></span> | <span data-ttu-id="4940c-533">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="4940c-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4940c-534">可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4940c-535">不用于 Web 套接字连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="4940c-536">此委托必须返回一个非空值，并且它将作为参数接收默认值。</span><span class="sxs-lookup"><span data-stu-id="4940c-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4940c-537">修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="4940c-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4940c-538">**替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。**</span><span class="sxs-lookup"><span data-stu-id="4940c-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4940c-539">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="4940c-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4940c-540">设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="4940c-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4940c-541">这支持使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="4940c-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4940c-542">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4940c-543">接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。</span><span class="sxs-lookup"><span data-stu-id="4940c-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4940c-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4940c-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4940c-545">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-545">JavaScript Option</span></span> | <span data-ttu-id="4940c-546">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-546">Default Value</span></span> | <span data-ttu-id="4940c-547">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4940c-548">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4940c-549">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-550">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-551">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4940c-552">Java</span><span class="sxs-lookup"><span data-stu-id="4940c-552">Java</span></span>](#tab/java)

| <span data-ttu-id="4940c-553">Java 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-553">Java Option</span></span> | <span data-ttu-id="4940c-554">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-554">Default Value</span></span> | <span data-ttu-id="4940c-555">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4940c-556">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4940c-557">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-558">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-559">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4940c-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4940c-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4940c-561">空</span><span class="sxs-lookup"><span data-stu-id="4940c-561">Empty</span></span> | <span data-ttu-id="4940c-562">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="4940c-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4940c-563">在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：</span><span class="sxs-lookup"><span data-stu-id="4940c-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4940c-564">在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：</span><span class="sxs-lookup"><span data-stu-id="4940c-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4940c-565">在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4940c-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4940c-566">其他资源</span><span class="sxs-lookup"><span data-stu-id="4940c-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4940c-567">JSON/消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="4940c-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4940c-568">ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="4940c-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4940c-569">每个协议都有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4940c-570">JSON 序列化可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置，该方法可以在方法中的`Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。</span><span class="sxs-lookup"><span data-stu-id="4940c-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4940c-571">该方法`AddJsonProtocol`采用接收`options`对象的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4940c-572">该对象的[Payload 序列化器设置](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于`JsonSerializerSettings`配置参数和返回值序列化JSON.NET对象。</span><span class="sxs-lookup"><span data-stu-id="4940c-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4940c-573">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="4940c-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="4940c-574">例如，要将序列化程序配置为使用"PascalCase"属性名称，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：</span><span class="sxs-lookup"><span data-stu-id="4940c-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="4940c-575">在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="4940c-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4940c-576">必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：</span><span class="sxs-lookup"><span data-stu-id="4940c-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4940c-577">此时无法在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="4940c-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4940c-578">消息包序列化选项</span><span class="sxs-lookup"><span data-stu-id="4940c-578">MessagePack serialization options</span></span>

<span data-ttu-id="4940c-579">消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4940c-580">有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。</span><span class="sxs-lookup"><span data-stu-id="4940c-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4940c-581">此时无法在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="4940c-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4940c-582">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="4940c-582">Configure server options</span></span>

<span data-ttu-id="4940c-583">下表描述了用于配置 SignalR 集线器的选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4940c-584">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-584">Option</span></span> | <span data-ttu-id="4940c-585">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-585">Default Value</span></span> | <span data-ttu-id="4940c-586">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="4940c-587">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-587">15 seconds</span></span> | <span data-ttu-id="4940c-588">如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="4940c-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4940c-589">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-590">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4940c-591">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-591">15 seconds</span></span> | <span data-ttu-id="4940c-592">如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。</span><span class="sxs-lookup"><span data-stu-id="4940c-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4940c-593">更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4940c-594">`ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。</span><span class="sxs-lookup"><span data-stu-id="4940c-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4940c-595">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="4940c-595">All installed protocols</span></span> | <span data-ttu-id="4940c-596">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="4940c-596">Protocols supported by this hub.</span></span> <span data-ttu-id="4940c-597">默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="4940c-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4940c-598">如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。</span><span class="sxs-lookup"><span data-stu-id="4940c-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4940c-599">默认值为`false`，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="4940c-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="4940c-600">可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4940c-601">单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：</span><span class="sxs-lookup"><span data-stu-id="4940c-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4940c-602">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="4940c-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4940c-603">用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4940c-604">这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)在`Startup.Configure`中配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4940c-605">下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4940c-606">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-606">Option</span></span> | <span data-ttu-id="4940c-607">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-607">Default Value</span></span> | <span data-ttu-id="4940c-608">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4940c-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="4940c-609">32 KB</span></span> | <span data-ttu-id="4940c-610">从服务器缓冲的客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="4940c-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="4940c-611">增加此值允许服务器接收较大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="4940c-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4940c-612">数据自动从应用于中心`Authorize`类的属性中收集。</span><span class="sxs-lookup"><span data-stu-id="4940c-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4940c-613">用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="4940c-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4940c-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="4940c-614">32 KB</span></span> | <span data-ttu-id="4940c-615">服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="4940c-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="4940c-616">增加此值允许服务器发送更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="4940c-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4940c-617">所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="4940c-617">All Transports are enabled.</span></span> | <span data-ttu-id="4940c-618">位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4940c-619">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="4940c-619">See below.</span></span> | <span data-ttu-id="4940c-620">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4940c-621">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="4940c-621">See below.</span></span> | <span data-ttu-id="4940c-622">特定于 WebSocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="4940c-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4940c-623">长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4940c-624">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-624">Option</span></span> | <span data-ttu-id="4940c-625">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-625">Default Value</span></span> | <span data-ttu-id="4940c-626">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4940c-627">90 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-627">90 seconds</span></span> | <span data-ttu-id="4940c-628">服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。</span><span class="sxs-lookup"><span data-stu-id="4940c-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4940c-629">减小此值会导致客户端更频繁地发出新的轮询请求。</span><span class="sxs-lookup"><span data-stu-id="4940c-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4940c-630">WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4940c-631">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-631">Option</span></span> | <span data-ttu-id="4940c-632">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-632">Default Value</span></span> | <span data-ttu-id="4940c-633">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4940c-634">5 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-634">5 seconds</span></span> | <span data-ttu-id="4940c-635">服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。</span><span class="sxs-lookup"><span data-stu-id="4940c-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4940c-636">可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4940c-637">委托接收客户端请求的值作为输入，并预期返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="4940c-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4940c-638">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="4940c-638">Configure client options</span></span>

<span data-ttu-id="4940c-639">可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。</span><span class="sxs-lookup"><span data-stu-id="4940c-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4940c-640">它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。</span><span class="sxs-lookup"><span data-stu-id="4940c-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4940c-641">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="4940c-641">Configure logging</span></span>

<span data-ttu-id="4940c-642">使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="4940c-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4940c-643">日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。</span><span class="sxs-lookup"><span data-stu-id="4940c-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4940c-644">有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。</span><span class="sxs-lookup"><span data-stu-id="4940c-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4940c-645">要注册日志记录提供程序，必须安装必要的包。</span><span class="sxs-lookup"><span data-stu-id="4940c-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4940c-646">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="4940c-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4940c-647">例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4940c-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4940c-648">调用`AddConsole`扩展方法：</span><span class="sxs-lookup"><span data-stu-id="4940c-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4940c-649">在 JavaScript 客户端中，`configureLogging`存在类似的方法。</span><span class="sxs-lookup"><span data-stu-id="4940c-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4940c-650">提供指示`LogLevel`要生产的日志消息的最小级别的值。</span><span class="sxs-lookup"><span data-stu-id="4940c-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4940c-651">日志将写入浏览器控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="4940c-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4940c-652">要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。</span><span class="sxs-lookup"><span data-stu-id="4940c-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4940c-653">有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="4940c-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4940c-654">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="4940c-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4940c-655">它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="4940c-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4940c-656">以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。</span><span class="sxs-lookup"><span data-stu-id="4940c-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4940c-657">如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="4940c-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4940c-658">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="4940c-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4940c-659">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="4940c-659">Configure allowed transports</span></span>

<span data-ttu-id="4940c-660">SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。</span><span class="sxs-lookup"><span data-stu-id="4940c-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4940c-661">值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="4940c-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4940c-662">默认情况下，所有传输都已启用。</span><span class="sxs-lookup"><span data-stu-id="4940c-662">All transports are enabled by default.</span></span>

<span data-ttu-id="4940c-663">例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：</span><span class="sxs-lookup"><span data-stu-id="4940c-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4940c-664">在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：</span><span class="sxs-lookup"><span data-stu-id="4940c-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4940c-665">配置无记名身份验证</span><span class="sxs-lookup"><span data-stu-id="4940c-665">Configure bearer authentication</span></span>

<span data-ttu-id="4940c-666">要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4940c-667">在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。</span><span class="sxs-lookup"><span data-stu-id="4940c-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4940c-668">在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4940c-669">在这些情况下，访问令牌作为查询字符串值`access_token`提供。</span><span class="sxs-lookup"><span data-stu-id="4940c-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4940c-670">在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：</span><span class="sxs-lookup"><span data-stu-id="4940c-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4940c-671">在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="4940c-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4940c-672">在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。</span><span class="sxs-lookup"><span data-stu-id="4940c-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4940c-673">[与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="4940c-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4940c-674">调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="4940c-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4940c-675">配置超时和保持活动选项</span><span class="sxs-lookup"><span data-stu-id="4940c-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4940c-676">`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：</span><span class="sxs-lookup"><span data-stu-id="4940c-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4940c-677">.NET</span><span class="sxs-lookup"><span data-stu-id="4940c-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4940c-678">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-678">Option</span></span> | <span data-ttu-id="4940c-679">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-679">Default value</span></span> | <span data-ttu-id="4940c-680">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4940c-681">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-682">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-682">Timeout for server activity.</span></span> <span data-ttu-id="4940c-683">如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4940c-684">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-685">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4940c-686">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-686">15 seconds</span></span> | <span data-ttu-id="4940c-687">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="4940c-688">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="4940c-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4940c-689">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-690">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="4940c-691">在 .NET 客户端中，超时值指定为`TimeSpan`值。</span><span class="sxs-lookup"><span data-stu-id="4940c-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4940c-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4940c-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4940c-693">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-693">Option</span></span> | <span data-ttu-id="4940c-694">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-694">Default value</span></span> | <span data-ttu-id="4940c-695">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4940c-696">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-697">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-697">Timeout for server activity.</span></span> <span data-ttu-id="4940c-698">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4940c-699">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-700">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4940c-701">Java</span><span class="sxs-lookup"><span data-stu-id="4940c-701">Java</span></span>](#tab/java)

| <span data-ttu-id="4940c-702">选项</span><span class="sxs-lookup"><span data-stu-id="4940c-702">Option</span></span> | <span data-ttu-id="4940c-703">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-703">Default value</span></span> | <span data-ttu-id="4940c-704">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4940c-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4940c-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4940c-706">30 秒（30，000 毫秒）</span><span class="sxs-lookup"><span data-stu-id="4940c-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4940c-707">服务器活动的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-707">Timeout for server activity.</span></span> <span data-ttu-id="4940c-708">如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4940c-709">此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。</span><span class="sxs-lookup"><span data-stu-id="4940c-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4940c-710">建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。</span><span class="sxs-lookup"><span data-stu-id="4940c-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4940c-711">15 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-711">15 seconds</span></span> | <span data-ttu-id="4940c-712">初始服务器握手的超时。</span><span class="sxs-lookup"><span data-stu-id="4940c-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="4940c-713">如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。</span><span class="sxs-lookup"><span data-stu-id="4940c-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4940c-714">这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4940c-715">有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="4940c-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4940c-716">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="4940c-716">Configure additional options</span></span>

<span data-ttu-id="4940c-717">其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：</span><span class="sxs-lookup"><span data-stu-id="4940c-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4940c-718">.NET</span><span class="sxs-lookup"><span data-stu-id="4940c-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4940c-719">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-719">.NET Option</span></span> |  <span data-ttu-id="4940c-720">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-720">Default value</span></span> | <span data-ttu-id="4940c-721">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4940c-722">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4940c-723">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-724">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-725">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4940c-726">空</span><span class="sxs-lookup"><span data-stu-id="4940c-726">Empty</span></span> | <span data-ttu-id="4940c-727">要发送到身份验证请求的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="4940c-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4940c-728">空</span><span class="sxs-lookup"><span data-stu-id="4940c-728">Empty</span></span> | <span data-ttu-id="4940c-729">要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。</span><span class="sxs-lookup"><span data-stu-id="4940c-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4940c-730">空</span><span class="sxs-lookup"><span data-stu-id="4940c-730">Empty</span></span> | <span data-ttu-id="4940c-731">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="4940c-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4940c-732">5 秒</span><span class="sxs-lookup"><span data-stu-id="4940c-732">5 seconds</span></span> | <span data-ttu-id="4940c-733">仅限 Web 插座。</span><span class="sxs-lookup"><span data-stu-id="4940c-733">WebSockets only.</span></span> <span data-ttu-id="4940c-734">客户端关闭后等待服务器确认关闭请求的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="4940c-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4940c-735">如果服务器在此时间内未确认关闭，则客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4940c-736">空</span><span class="sxs-lookup"><span data-stu-id="4940c-736">Empty</span></span> | <span data-ttu-id="4940c-737">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="4940c-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4940c-738">可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4940c-739">不用于 Web 套接字连接。</span><span class="sxs-lookup"><span data-stu-id="4940c-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="4940c-740">此委托必须返回一个非空值，并且它将作为参数接收默认值。</span><span class="sxs-lookup"><span data-stu-id="4940c-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4940c-741">修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。</span><span class="sxs-lookup"><span data-stu-id="4940c-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4940c-742">**替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。**</span><span class="sxs-lookup"><span data-stu-id="4940c-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4940c-743">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="4940c-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4940c-744">设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="4940c-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4940c-745">这支持使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="4940c-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4940c-746">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="4940c-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4940c-747">接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。</span><span class="sxs-lookup"><span data-stu-id="4940c-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4940c-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4940c-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4940c-749">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-749">JavaScript Option</span></span> | <span data-ttu-id="4940c-750">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-750">Default Value</span></span> | <span data-ttu-id="4940c-751">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4940c-752">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4940c-753">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-754">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-755">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4940c-756">Java</span><span class="sxs-lookup"><span data-stu-id="4940c-756">Java</span></span>](#tab/java)

| <span data-ttu-id="4940c-757">Java 选项</span><span class="sxs-lookup"><span data-stu-id="4940c-757">Java Option</span></span> | <span data-ttu-id="4940c-758">默认值</span><span class="sxs-lookup"><span data-stu-id="4940c-758">Default Value</span></span> | <span data-ttu-id="4940c-759">说明</span><span class="sxs-lookup"><span data-stu-id="4940c-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4940c-760">返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。</span><span class="sxs-lookup"><span data-stu-id="4940c-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4940c-761">将此`true`设置为跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="4940c-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4940c-762">**仅当 WebSocket 传输是唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="4940c-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4940c-763">使用 Azure SignalR 服务时无法启用此设置。</span><span class="sxs-lookup"><span data-stu-id="4940c-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4940c-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4940c-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4940c-765">空</span><span class="sxs-lookup"><span data-stu-id="4940c-765">Empty</span></span> | <span data-ttu-id="4940c-766">要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="4940c-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4940c-767">在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：</span><span class="sxs-lookup"><span data-stu-id="4940c-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4940c-768">在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：</span><span class="sxs-lookup"><span data-stu-id="4940c-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4940c-769">在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4940c-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4940c-770">其他资源</span><span class="sxs-lookup"><span data-stu-id="4940c-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
