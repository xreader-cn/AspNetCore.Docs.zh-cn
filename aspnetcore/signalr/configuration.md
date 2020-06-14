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
ms.openlocfilehash: 09866f1fd56a4d0747ef3814c85ab5070cfb8d59
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756114"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="c2528-103">ASP.NET Core SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="c2528-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c2528-104">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c2528-105">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c2528-106">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c2528-107">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c2528-108">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c2528-109">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c2528-110">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c2528-111">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="c2528-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c2528-112">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c2528-113">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c2528-114">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c2528-115">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c2528-116">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c2528-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c2528-117">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="c2528-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c2528-118">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-118">MessagePack serialization options</span></span>

<span data-ttu-id="c2528-119">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c2528-120">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c2528-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-121">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c2528-122">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c2528-122">Configure server options</span></span>

<span data-ttu-id="c2528-123">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c2528-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c2528-124">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-124">Option</span></span> | <span data-ttu-id="c2528-125">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-125">Default Value</span></span> | <span data-ttu-id="c2528-126">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c2528-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-127">30 seconds</span></span> | <span data-ttu-id="c2528-128">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c2528-129">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c2528-130">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c2528-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-131">15 seconds</span></span> | <span data-ttu-id="c2528-132">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c2528-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c2528-133">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-134">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-135">15 seconds</span></span> | <span data-ttu-id="c2528-136">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c2528-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c2528-137">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c2528-138">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c2528-139">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c2528-139">All installed protocols</span></span> | <span data-ttu-id="c2528-140">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-140">Protocols supported by this hub.</span></span> <span data-ttu-id="c2528-141">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c2528-142">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c2528-143">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c2528-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c2528-144">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="c2528-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c2528-145">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="c2528-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c2528-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-146">32 KB</span></span> | <span data-ttu-id="c2528-147">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="c2528-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c2528-148">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c2528-149">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c2528-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c2528-150">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c2528-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c2528-151">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c2528-152">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c2528-153">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c2528-154">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-154">Option</span></span> | <span data-ttu-id="c2528-155">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-155">Default Value</span></span> | <span data-ttu-id="c2528-156">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c2528-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-157">32 KB</span></span> | <span data-ttu-id="c2528-158">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c2528-159">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c2528-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c2528-160">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c2528-161">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c2528-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c2528-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-162">32 KB</span></span> | <span data-ttu-id="c2528-163">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c2528-164">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c2528-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c2528-165">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c2528-165">All Transports are enabled.</span></span> | <span data-ttu-id="c2528-166">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c2528-167">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-167">See below.</span></span> | <span data-ttu-id="c2528-168">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c2528-169">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-169">See below.</span></span> | <span data-ttu-id="c2528-170">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c2528-171">0</span><span class="sxs-lookup"><span data-stu-id="c2528-171">0</span></span> | <span data-ttu-id="c2528-172">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="c2528-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c2528-173">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="c2528-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c2528-174">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c2528-175">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-175">Option</span></span> | <span data-ttu-id="c2528-176">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-176">Default Value</span></span> | <span data-ttu-id="c2528-177">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c2528-178">90秒</span><span class="sxs-lookup"><span data-stu-id="c2528-178">90 seconds</span></span> | <span data-ttu-id="c2528-179">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c2528-180">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c2528-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c2528-181">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c2528-182">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-182">Option</span></span> | <span data-ttu-id="c2528-183">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-183">Default Value</span></span> | <span data-ttu-id="c2528-184">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c2528-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-185">5 seconds</span></span> | <span data-ttu-id="c2528-186">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c2528-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c2528-187">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c2528-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c2528-188">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c2528-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c2528-189">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c2528-189">Configure client options</span></span>

<span data-ttu-id="c2528-190">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="c2528-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c2528-191">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c2528-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c2528-192">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c2528-192">Configure logging</span></span>

<span data-ttu-id="c2528-193">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c2528-194">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c2528-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c2528-195">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="c2528-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-196">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c2528-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c2528-197">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="c2528-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c2528-198">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c2528-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c2528-199">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c2528-200">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c2528-201">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c2528-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c2528-202">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c2528-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c2528-203">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="c2528-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c2528-204">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c2528-205">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="c2528-205">The following table lists the available log levels.</span></span> <span data-ttu-id="c2528-206">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="c2528-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c2528-207">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="c2528-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c2528-208">String</span><span class="sxs-lookup"><span data-stu-id="c2528-208">String</span></span>                      | <span data-ttu-id="c2528-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c2528-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c2528-210">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="c2528-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c2528-211">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="c2528-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c2528-212">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c2528-213">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c2528-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c2528-214">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c2528-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c2528-215">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c2528-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c2528-216">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c2528-217">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c2528-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c2528-218">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c2528-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c2528-219">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c2528-219">Configure allowed transports</span></span>

<span data-ttu-id="c2528-220">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="c2528-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c2528-221">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c2528-222">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-222">All transports are enabled by default.</span></span>

<span data-ttu-id="c2528-223">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c2528-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c2528-224">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c2528-225">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c2528-226">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c2528-227">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c2528-228">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="c2528-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c2528-229">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c2528-229">Configure bearer authentication</span></span>

<span data-ttu-id="c2528-230">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c2528-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c2528-231">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="c2528-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c2528-232">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c2528-233">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c2528-234">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c2528-235">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c2528-236">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c2528-237">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c2528-238">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c2528-239">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c2528-240">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c2528-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-241">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-242">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-242">Option</span></span> | <span data-ttu-id="c2528-243">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-243">Default value</span></span> | <span data-ttu-id="c2528-244">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c2528-245">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-246">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-246">Timeout for server activity.</span></span> <span data-ttu-id="c2528-247">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-248">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-249">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c2528-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-250">15 seconds</span></span> | <span data-ttu-id="c2528-251">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-252">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-253">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-254">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-255">15 seconds</span></span> | <span data-ttu-id="c2528-256">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-257">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-258">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c2528-259">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c2528-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c2528-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-261">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-261">Option</span></span> | <span data-ttu-id="c2528-262">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-262">Default value</span></span> | <span data-ttu-id="c2528-263">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c2528-264">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-265">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-265">Timeout for server activity.</span></span> <span data-ttu-id="c2528-266">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c2528-267">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-268">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c2528-269">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c2528-270">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-271">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-272">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-273">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-273">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-274">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-274">Option</span></span> | <span data-ttu-id="c2528-275">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-275">Default value</span></span> | <span data-ttu-id="c2528-276">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c2528-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c2528-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c2528-278">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-279">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-279">Timeout for server activity.</span></span> <span data-ttu-id="c2528-280">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-281">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-282">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c2528-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-283">15 seconds</span></span> | <span data-ttu-id="c2528-284">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-285">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-286">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-287">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c2528-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c2528-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c2528-289">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c2528-290">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-291">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-292">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c2528-293">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c2528-293">Configure additional options</span></span>

<span data-ttu-id="c2528-294">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-295">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-296">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-296">.NET Option</span></span> |  <span data-ttu-id="c2528-297">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-297">Default value</span></span> | <span data-ttu-id="c2528-298">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c2528-299">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c2528-300">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-301">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-302">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c2528-303">空</span><span class="sxs-lookup"><span data-stu-id="c2528-303">Empty</span></span> | <span data-ttu-id="c2528-304">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c2528-305">空</span><span class="sxs-lookup"><span data-stu-id="c2528-305">Empty</span></span> | <span data-ttu-id="c2528-306">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c2528-307">空</span><span class="sxs-lookup"><span data-stu-id="c2528-307">Empty</span></span> | <span data-ttu-id="c2528-308">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c2528-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-309">5 seconds</span></span> | <span data-ttu-id="c2528-310">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c2528-310">WebSockets only.</span></span> <span data-ttu-id="c2528-311">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c2528-312">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c2528-313">空</span><span class="sxs-lookup"><span data-stu-id="c2528-313">Empty</span></span> | <span data-ttu-id="c2528-314">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c2528-315">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c2528-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c2528-316">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="c2528-317">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c2528-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c2528-318">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c2528-319">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c2528-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c2528-320">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c2528-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c2528-321">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c2528-322">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c2528-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c2528-323">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c2528-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c2528-324">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c2528-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-326">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-326">JavaScript Option</span></span> | <span data-ttu-id="c2528-327">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-327">Default Value</span></span> | <span data-ttu-id="c2528-328">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c2528-329">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c2528-330">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-331">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-332">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="c2528-333">指定是否将凭据与 CORS 请求一起发送。</span><span class="sxs-lookup"><span data-stu-id="c2528-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="c2528-334">Azure App Service 使用 cookie 进行粘滞会话，并且需要此选项才能正常工作。</span><span class="sxs-lookup"><span data-stu-id="c2528-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="c2528-335">有关 CORS 的详细信息 SignalR ，请参阅 <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="c2528-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-336">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-336">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-337">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-337">Java Option</span></span> | <span data-ttu-id="c2528-338">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-338">Default Value</span></span> | <span data-ttu-id="c2528-339">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c2528-340">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c2528-341">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-342">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-343">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c2528-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c2528-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c2528-345">空</span><span class="sxs-lookup"><span data-stu-id="c2528-345">Empty</span></span> | <span data-ttu-id="c2528-346">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c2528-347">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c2528-348">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c2528-349">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c2528-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c2528-350">其他资源</span><span class="sxs-lookup"><span data-stu-id="c2528-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c2528-351">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c2528-352">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c2528-353">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c2528-354">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c2528-355">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c2528-356">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c2528-357">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c2528-358">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="c2528-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c2528-359">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="c2528-360">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c2528-361">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c2528-362">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c2528-363">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c2528-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c2528-364">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="c2528-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c2528-365">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-365">MessagePack serialization options</span></span>

<span data-ttu-id="c2528-366">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c2528-367">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c2528-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-368">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c2528-369">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c2528-369">Configure server options</span></span>

<span data-ttu-id="c2528-370">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c2528-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c2528-371">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-371">Option</span></span> | <span data-ttu-id="c2528-372">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-372">Default Value</span></span> | <span data-ttu-id="c2528-373">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c2528-374">30 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-374">30 seconds</span></span> | <span data-ttu-id="c2528-375">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c2528-376">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c2528-377">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c2528-378">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-378">15 seconds</span></span> | <span data-ttu-id="c2528-379">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c2528-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c2528-380">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-381">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-382">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-382">15 seconds</span></span> | <span data-ttu-id="c2528-383">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c2528-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c2528-384">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c2528-385">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c2528-386">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c2528-386">All installed protocols</span></span> | <span data-ttu-id="c2528-387">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-387">Protocols supported by this hub.</span></span> <span data-ttu-id="c2528-388">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c2528-389">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c2528-390">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c2528-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c2528-391">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="c2528-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c2528-392">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="c2528-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c2528-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-393">32 KB</span></span> | <span data-ttu-id="c2528-394">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="c2528-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c2528-395">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c2528-396">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c2528-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c2528-397">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c2528-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c2528-398">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c2528-399">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c2528-400">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c2528-401">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-401">Option</span></span> | <span data-ttu-id="c2528-402">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-402">Default Value</span></span> | <span data-ttu-id="c2528-403">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c2528-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-404">32 KB</span></span> | <span data-ttu-id="c2528-405">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c2528-406">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c2528-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c2528-407">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c2528-408">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c2528-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c2528-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-409">32 KB</span></span> | <span data-ttu-id="c2528-410">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c2528-411">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c2528-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c2528-412">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c2528-412">All Transports are enabled.</span></span> | <span data-ttu-id="c2528-413">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c2528-414">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-414">See below.</span></span> | <span data-ttu-id="c2528-415">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c2528-416">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-416">See below.</span></span> | <span data-ttu-id="c2528-417">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="c2528-418">0</span><span class="sxs-lookup"><span data-stu-id="c2528-418">0</span></span> | <span data-ttu-id="c2528-419">指定 negotiate 协议的最低版本。</span><span class="sxs-lookup"><span data-stu-id="c2528-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="c2528-420">这用于将客户端限制到较新的版本。</span><span class="sxs-lookup"><span data-stu-id="c2528-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="c2528-421">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c2528-422">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-422">Option</span></span> | <span data-ttu-id="c2528-423">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-423">Default Value</span></span> | <span data-ttu-id="c2528-424">描述</span><span class="sxs-lookup"><span data-stu-id="c2528-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c2528-425">90秒</span><span class="sxs-lookup"><span data-stu-id="c2528-425">90 seconds</span></span> | <span data-ttu-id="c2528-426">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c2528-427">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c2528-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c2528-428">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c2528-429">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-429">Option</span></span> | <span data-ttu-id="c2528-430">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-430">Default Value</span></span> | <span data-ttu-id="c2528-431">描述</span><span class="sxs-lookup"><span data-stu-id="c2528-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c2528-432">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-432">5 seconds</span></span> | <span data-ttu-id="c2528-433">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c2528-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c2528-434">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c2528-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c2528-435">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c2528-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c2528-436">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c2528-436">Configure client options</span></span>

<span data-ttu-id="c2528-437">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="c2528-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c2528-438">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c2528-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c2528-439">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c2528-439">Configure logging</span></span>

<span data-ttu-id="c2528-440">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c2528-441">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c2528-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c2528-442">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="c2528-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-443">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c2528-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c2528-444">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="c2528-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c2528-445">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c2528-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c2528-446">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c2528-447">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c2528-448">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c2528-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c2528-449">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c2528-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c2528-450">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="c2528-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c2528-451">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c2528-452">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="c2528-452">The following table lists the available log levels.</span></span> <span data-ttu-id="c2528-453">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="c2528-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c2528-454">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="c2528-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c2528-455">String</span><span class="sxs-lookup"><span data-stu-id="c2528-455">String</span></span>                      | <span data-ttu-id="c2528-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c2528-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c2528-457">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="c2528-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c2528-458">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="c2528-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c2528-459">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c2528-460">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c2528-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c2528-461">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c2528-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c2528-462">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c2528-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c2528-463">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c2528-464">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c2528-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c2528-465">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c2528-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c2528-466">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c2528-466">Configure allowed transports</span></span>

<span data-ttu-id="c2528-467">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="c2528-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c2528-468">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c2528-469">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-469">All transports are enabled by default.</span></span>

<span data-ttu-id="c2528-470">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c2528-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c2528-471">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c2528-472">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c2528-473">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c2528-474">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c2528-475">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="c2528-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c2528-476">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c2528-476">Configure bearer authentication</span></span>

<span data-ttu-id="c2528-477">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c2528-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c2528-478">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="c2528-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c2528-479">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c2528-480">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c2528-481">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c2528-482">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c2528-483">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c2528-484">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c2528-485">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c2528-486">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c2528-487">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c2528-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-488">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-489">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-489">Option</span></span> | <span data-ttu-id="c2528-490">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-490">Default value</span></span> | <span data-ttu-id="c2528-491">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c2528-492">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-493">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-493">Timeout for server activity.</span></span> <span data-ttu-id="c2528-494">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-495">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-496">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c2528-497">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-497">15 seconds</span></span> | <span data-ttu-id="c2528-498">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-499">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-500">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-501">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-502">15 seconds</span></span> | <span data-ttu-id="c2528-503">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-504">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-505">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c2528-506">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c2528-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c2528-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-508">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-508">Option</span></span> | <span data-ttu-id="c2528-509">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-509">Default value</span></span> | <span data-ttu-id="c2528-510">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c2528-511">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-512">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-512">Timeout for server activity.</span></span> <span data-ttu-id="c2528-513">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c2528-514">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-515">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c2528-516">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c2528-517">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-518">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-519">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-520">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-520">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-521">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-521">Option</span></span> | <span data-ttu-id="c2528-522">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-522">Default value</span></span> | <span data-ttu-id="c2528-523">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c2528-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c2528-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c2528-525">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-526">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-526">Timeout for server activity.</span></span> <span data-ttu-id="c2528-527">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-528">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-529">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c2528-530">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-530">15 seconds</span></span> | <span data-ttu-id="c2528-531">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-532">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-533">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-534">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c2528-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c2528-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c2528-536">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c2528-537">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-538">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-539">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c2528-540">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c2528-540">Configure additional options</span></span>

<span data-ttu-id="c2528-541">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-542">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-543">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-543">.NET Option</span></span> |  <span data-ttu-id="c2528-544">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-544">Default value</span></span> | <span data-ttu-id="c2528-545">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c2528-546">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c2528-547">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-548">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-549">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c2528-550">空</span><span class="sxs-lookup"><span data-stu-id="c2528-550">Empty</span></span> | <span data-ttu-id="c2528-551">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c2528-552">空</span><span class="sxs-lookup"><span data-stu-id="c2528-552">Empty</span></span> | <span data-ttu-id="c2528-553">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c2528-554">空</span><span class="sxs-lookup"><span data-stu-id="c2528-554">Empty</span></span> | <span data-ttu-id="c2528-555">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c2528-556">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-556">5 seconds</span></span> | <span data-ttu-id="c2528-557">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c2528-557">WebSockets only.</span></span> <span data-ttu-id="c2528-558">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c2528-559">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c2528-560">空</span><span class="sxs-lookup"><span data-stu-id="c2528-560">Empty</span></span> | <span data-ttu-id="c2528-561">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c2528-562">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c2528-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c2528-563">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="c2528-564">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c2528-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c2528-565">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c2528-566">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c2528-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c2528-567">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c2528-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c2528-568">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c2528-569">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c2528-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c2528-570">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c2528-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c2528-571">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c2528-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-573">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-573">JavaScript Option</span></span> | <span data-ttu-id="c2528-574">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-574">Default Value</span></span> | <span data-ttu-id="c2528-575">描述</span><span class="sxs-lookup"><span data-stu-id="c2528-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c2528-576">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c2528-577">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-578">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-579">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-580">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-580">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-581">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-581">Java Option</span></span> | <span data-ttu-id="c2528-582">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-582">Default Value</span></span> | <span data-ttu-id="c2528-583">描述</span><span class="sxs-lookup"><span data-stu-id="c2528-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c2528-584">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c2528-585">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-586">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-587">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c2528-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c2528-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c2528-589">空</span><span class="sxs-lookup"><span data-stu-id="c2528-589">Empty</span></span> | <span data-ttu-id="c2528-590">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c2528-591">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c2528-592">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c2528-593">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c2528-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c2528-594">其他资源</span><span class="sxs-lookup"><span data-stu-id="c2528-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c2528-595">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c2528-596">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c2528-597">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c2528-598">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="c2528-599">`AddJsonProtocol`可以在中的[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c2528-600">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c2528-601">该对象上的[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是一个 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c2528-602">有关详细信息，请参阅[文档](/dotnet/api/system.text.json)中的System.Text.Js。</span><span class="sxs-lookup"><span data-stu-id="c2528-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="c2528-603">例如，若要将序列化程序配置为不更改属性名称的大小写（而不是默认的 "camelCase" 名称），请使用中的以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="c2528-604">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c2528-605">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c2528-606">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="c2528-607">切换到 Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="c2528-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="c2528-608">如果需要的功能 `Newtonsoft.Json` 在中不受支持 `System.Text.Json` ，请参阅[切换到 Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="c2528-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c2528-609">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-609">MessagePack serialization options</span></span>

<span data-ttu-id="c2528-610">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c2528-611">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c2528-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-612">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c2528-613">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c2528-613">Configure server options</span></span>

<span data-ttu-id="c2528-614">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c2528-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c2528-615">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-615">Option</span></span> | <span data-ttu-id="c2528-616">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-616">Default Value</span></span> | <span data-ttu-id="c2528-617">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c2528-618">30 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-618">30 seconds</span></span> | <span data-ttu-id="c2528-619">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c2528-620">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c2528-621">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c2528-622">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-622">15 seconds</span></span> | <span data-ttu-id="c2528-623">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c2528-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c2528-624">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-625">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-626">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-626">15 seconds</span></span> | <span data-ttu-id="c2528-627">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c2528-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c2528-628">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c2528-629">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c2528-630">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c2528-630">All installed protocols</span></span> | <span data-ttu-id="c2528-631">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-631">Protocols supported by this hub.</span></span> <span data-ttu-id="c2528-632">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c2528-633">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c2528-634">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c2528-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="c2528-635">可为客户端上载流缓冲的最大项数。</span><span class="sxs-lookup"><span data-stu-id="c2528-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="c2528-636">如果达到此限制，则会阻止处理调用，直到服务器处理流项。</span><span class="sxs-lookup"><span data-stu-id="c2528-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="c2528-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-637">32 KB</span></span> | <span data-ttu-id="c2528-638">单个传入集线器消息的最大大小。</span><span class="sxs-lookup"><span data-stu-id="c2528-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="c2528-639">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c2528-640">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c2528-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c2528-641">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c2528-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c2528-642">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c2528-643">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c2528-644">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c2528-645">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-645">Option</span></span> | <span data-ttu-id="c2528-646">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-646">Default Value</span></span> | <span data-ttu-id="c2528-647">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c2528-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-648">32 KB</span></span> | <span data-ttu-id="c2528-649">在应用反压之前，服务器从客户端接收的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="c2528-650">增大此值后，服务器可以更快地接收更大的消息，而无需应用反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c2528-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c2528-651">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c2528-652">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c2528-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c2528-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-653">32 KB</span></span> | <span data-ttu-id="c2528-654">在观察反压之前，服务器要发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="c2528-655">增大此值后，服务器可以更快地缓冲更大的消息，而无需等待反压，但会增加内存消耗。</span><span class="sxs-lookup"><span data-stu-id="c2528-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c2528-656">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c2528-656">All Transports are enabled.</span></span> | <span data-ttu-id="c2528-657">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c2528-658">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-658">See below.</span></span> | <span data-ttu-id="c2528-659">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c2528-660">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-660">See below.</span></span> | <span data-ttu-id="c2528-661">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c2528-662">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c2528-663">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-663">Option</span></span> | <span data-ttu-id="c2528-664">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-664">Default Value</span></span> | <span data-ttu-id="c2528-665">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c2528-666">90秒</span><span class="sxs-lookup"><span data-stu-id="c2528-666">90 seconds</span></span> | <span data-ttu-id="c2528-667">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c2528-668">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c2528-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c2528-669">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c2528-670">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-670">Option</span></span> | <span data-ttu-id="c2528-671">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-671">Default Value</span></span> | <span data-ttu-id="c2528-672">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c2528-673">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-673">5 seconds</span></span> | <span data-ttu-id="c2528-674">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c2528-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c2528-675">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c2528-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c2528-676">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c2528-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c2528-677">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c2528-677">Configure client options</span></span>

<span data-ttu-id="c2528-678">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="c2528-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c2528-679">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c2528-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c2528-680">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c2528-680">Configure logging</span></span>

<span data-ttu-id="c2528-681">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c2528-682">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c2528-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c2528-683">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="c2528-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-684">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c2528-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c2528-685">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="c2528-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c2528-686">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c2528-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c2528-687">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c2528-688">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c2528-689">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c2528-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c2528-690">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c2528-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="c2528-691">`LogLevel`您还可以提供一个 `string` 表示日志级别名称的值，而不是一个值。</span><span class="sxs-lookup"><span data-stu-id="c2528-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="c2528-692">当 SignalR 你在无法访问常量的环境中配置日志记录时，这非常有用 `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="c2528-693">下表列出了可用的日志级别。</span><span class="sxs-lookup"><span data-stu-id="c2528-693">The following table lists the available log levels.</span></span> <span data-ttu-id="c2528-694">为 `configureLogging` 设置将记录的**最小**日志级别而提供的值。</span><span class="sxs-lookup"><span data-stu-id="c2528-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="c2528-695">将记录在此级别上记录的消息**或在表中列出的级别**。</span><span class="sxs-lookup"><span data-stu-id="c2528-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="c2528-696">String</span><span class="sxs-lookup"><span data-stu-id="c2528-696">String</span></span>                      | <span data-ttu-id="c2528-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="c2528-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="c2528-698">`info`**或**`information`</span><span class="sxs-lookup"><span data-stu-id="c2528-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="c2528-699">`warn`**或**`warning`</span><span class="sxs-lookup"><span data-stu-id="c2528-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="c2528-700">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c2528-701">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c2528-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c2528-702">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c2528-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c2528-703">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c2528-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c2528-704">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c2528-705">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c2528-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c2528-706">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c2528-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c2528-707">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c2528-707">Configure allowed transports</span></span>

<span data-ttu-id="c2528-708">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="c2528-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c2528-709">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c2528-710">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-710">All transports are enabled by default.</span></span>

<span data-ttu-id="c2528-711">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c2528-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c2528-712">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c2528-713">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="c2528-714">在 Java 客户端中，通过中的方法选择了传输 `withTransport` `HttpHubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="c2528-715">Java 客户端默认使用 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c2528-716">SignalRJava 客户端尚不支持传输回退。</span><span class="sxs-lookup"><span data-stu-id="c2528-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c2528-717">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c2528-717">Configure bearer authentication</span></span>

<span data-ttu-id="c2528-718">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c2528-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c2528-719">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="c2528-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c2528-720">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c2528-721">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c2528-722">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c2528-723">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c2528-724">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c2528-725">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c2528-726">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c2528-727">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c2528-728">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c2528-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-729">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-730">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-730">Option</span></span> | <span data-ttu-id="c2528-731">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-731">Default value</span></span> | <span data-ttu-id="c2528-732">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c2528-733">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-734">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-734">Timeout for server activity.</span></span> <span data-ttu-id="c2528-735">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-736">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-737">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c2528-738">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-738">15 seconds</span></span> | <span data-ttu-id="c2528-739">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-740">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-741">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-742">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-743">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-743">15 seconds</span></span> | <span data-ttu-id="c2528-744">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-745">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-746">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c2528-747">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c2528-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c2528-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-749">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-749">Option</span></span> | <span data-ttu-id="c2528-750">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-750">Default value</span></span> | <span data-ttu-id="c2528-751">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c2528-752">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-753">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-753">Timeout for server activity.</span></span> <span data-ttu-id="c2528-754">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c2528-755">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-756">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c2528-757">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c2528-758">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-759">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-760">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-761">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-761">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-762">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-762">Option</span></span> | <span data-ttu-id="c2528-763">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-763">Default value</span></span> | <span data-ttu-id="c2528-764">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c2528-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c2528-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c2528-766">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-767">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-767">Timeout for server activity.</span></span> <span data-ttu-id="c2528-768">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-769">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-770">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c2528-771">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-771">15 seconds</span></span> | <span data-ttu-id="c2528-772">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-773">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-774">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-775">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c2528-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c2528-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c2528-777">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c2528-778">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-779">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-780">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c2528-781">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c2528-781">Configure additional options</span></span>

<span data-ttu-id="c2528-782">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-783">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-784">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-784">.NET Option</span></span> |  <span data-ttu-id="c2528-785">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-785">Default value</span></span> | <span data-ttu-id="c2528-786">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c2528-787">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c2528-788">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-789">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-790">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c2528-791">空</span><span class="sxs-lookup"><span data-stu-id="c2528-791">Empty</span></span> | <span data-ttu-id="c2528-792">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c2528-793">空</span><span class="sxs-lookup"><span data-stu-id="c2528-793">Empty</span></span> | <span data-ttu-id="c2528-794">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c2528-795">空</span><span class="sxs-lookup"><span data-stu-id="c2528-795">Empty</span></span> | <span data-ttu-id="c2528-796">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c2528-797">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-797">5 seconds</span></span> | <span data-ttu-id="c2528-798">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c2528-798">WebSockets only.</span></span> <span data-ttu-id="c2528-799">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c2528-800">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c2528-801">空</span><span class="sxs-lookup"><span data-stu-id="c2528-801">Empty</span></span> | <span data-ttu-id="c2528-802">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c2528-803">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c2528-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c2528-804">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="c2528-805">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c2528-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c2528-806">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c2528-807">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c2528-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c2528-808">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c2528-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c2528-809">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c2528-810">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c2528-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c2528-811">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c2528-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c2528-812">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c2528-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-814">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-814">JavaScript Option</span></span> | <span data-ttu-id="c2528-815">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-815">Default Value</span></span> | <span data-ttu-id="c2528-816">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c2528-817">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c2528-818">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-819">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-820">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-821">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-821">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-822">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-822">Java Option</span></span> | <span data-ttu-id="c2528-823">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-823">Default Value</span></span> | <span data-ttu-id="c2528-824">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c2528-825">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c2528-826">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-827">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-828">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c2528-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c2528-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c2528-830">空</span><span class="sxs-lookup"><span data-stu-id="c2528-830">Empty</span></span> | <span data-ttu-id="c2528-831">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c2528-832">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c2528-833">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c2528-834">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c2528-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c2528-835">其他资源</span><span class="sxs-lookup"><span data-stu-id="c2528-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c2528-836">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c2528-837">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c2528-838">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c2528-839">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c2528-840">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c2528-841">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c2528-842">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="c2528-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c2528-843">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c2528-844">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c2528-845">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c2528-846">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c2528-847">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-847">MessagePack serialization options</span></span>

<span data-ttu-id="c2528-848">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c2528-849">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c2528-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-850">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c2528-851">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c2528-851">Configure server options</span></span>

<span data-ttu-id="c2528-852">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c2528-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c2528-853">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-853">Option</span></span> | <span data-ttu-id="c2528-854">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-854">Default Value</span></span> | <span data-ttu-id="c2528-855">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="c2528-856">30 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-856">30 seconds</span></span> | <span data-ttu-id="c2528-857">如果客户端在此时间间隔内未收到消息（包括保持活动状态），则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="c2528-858">由于实现方式的原因，客户端实际标记为断开连接可能需要更长的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="c2528-859">建议值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="c2528-860">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-860">15 seconds</span></span> | <span data-ttu-id="c2528-861">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c2528-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c2528-862">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-863">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-864">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-864">15 seconds</span></span> | <span data-ttu-id="c2528-865">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c2528-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c2528-866">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c2528-867">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c2528-868">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c2528-868">All installed protocols</span></span> | <span data-ttu-id="c2528-869">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-869">Protocols supported by this hub.</span></span> <span data-ttu-id="c2528-870">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c2528-871">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c2528-872">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c2528-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c2528-873">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c2528-874">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c2528-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c2528-875">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c2528-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c2528-876">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c2528-877">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c2528-878">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c2528-879">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-879">Option</span></span> | <span data-ttu-id="c2528-880">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-880">Default Value</span></span> | <span data-ttu-id="c2528-881">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c2528-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-882">32 KB</span></span> | <span data-ttu-id="c2528-883">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c2528-884">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="c2528-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c2528-885">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c2528-886">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c2528-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c2528-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-887">32 KB</span></span> | <span data-ttu-id="c2528-888">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c2528-889">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="c2528-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c2528-890">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c2528-890">All Transports are enabled.</span></span> | <span data-ttu-id="c2528-891">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c2528-892">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-892">See below.</span></span> | <span data-ttu-id="c2528-893">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c2528-894">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-894">See below.</span></span> | <span data-ttu-id="c2528-895">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c2528-896">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c2528-897">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-897">Option</span></span> | <span data-ttu-id="c2528-898">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-898">Default Value</span></span> | <span data-ttu-id="c2528-899">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c2528-900">90秒</span><span class="sxs-lookup"><span data-stu-id="c2528-900">90 seconds</span></span> | <span data-ttu-id="c2528-901">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c2528-902">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c2528-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c2528-903">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c2528-904">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-904">Option</span></span> | <span data-ttu-id="c2528-905">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-905">Default Value</span></span> | <span data-ttu-id="c2528-906">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c2528-907">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-907">5 seconds</span></span> | <span data-ttu-id="c2528-908">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c2528-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c2528-909">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c2528-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c2528-910">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c2528-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c2528-911">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c2528-911">Configure client options</span></span>

<span data-ttu-id="c2528-912">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="c2528-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c2528-913">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c2528-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c2528-914">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c2528-914">Configure logging</span></span>

<span data-ttu-id="c2528-915">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c2528-916">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c2528-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c2528-917">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="c2528-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-918">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c2528-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c2528-919">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="c2528-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c2528-920">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c2528-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c2528-921">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c2528-922">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c2528-923">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c2528-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c2528-924">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c2528-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c2528-925">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c2528-926">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c2528-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c2528-927">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c2528-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c2528-928">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c2528-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c2528-929">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c2528-930">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c2528-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c2528-931">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c2528-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c2528-932">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c2528-932">Configure allowed transports</span></span>

<span data-ttu-id="c2528-933">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="c2528-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c2528-934">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c2528-935">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-935">All transports are enabled by default.</span></span>

<span data-ttu-id="c2528-936">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c2528-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c2528-937">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="c2528-938">此版本的 Java 客户端 websocket 是唯一可用的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c2528-939">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c2528-939">Configure bearer authentication</span></span>

<span data-ttu-id="c2528-940">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c2528-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c2528-941">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="c2528-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c2528-942">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c2528-943">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c2528-944">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c2528-945">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c2528-946">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c2528-947">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c2528-948">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c2528-949">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c2528-950">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c2528-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-951">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-952">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-952">Option</span></span> | <span data-ttu-id="c2528-953">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-953">Default value</span></span> | <span data-ttu-id="c2528-954">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c2528-955">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-956">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-956">Timeout for server activity.</span></span> <span data-ttu-id="c2528-957">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-958">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-959">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c2528-960">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-960">15 seconds</span></span> | <span data-ttu-id="c2528-961">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-962">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-963">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-964">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-965">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-965">15 seconds</span></span> | <span data-ttu-id="c2528-966">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-967">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-968">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="c2528-969">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c2528-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c2528-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-971">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-971">Option</span></span> | <span data-ttu-id="c2528-972">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-972">Default value</span></span> | <span data-ttu-id="c2528-973">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c2528-974">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-975">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-975">Timeout for server activity.</span></span> <span data-ttu-id="c2528-976">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c2528-977">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-978">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="c2528-979">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c2528-980">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-981">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-982">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-983">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-983">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-984">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-984">Option</span></span> | <span data-ttu-id="c2528-985">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-985">Default value</span></span> | <span data-ttu-id="c2528-986">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c2528-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c2528-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c2528-988">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-989">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-989">Timeout for server activity.</span></span> <span data-ttu-id="c2528-990">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-991">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-992">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c2528-993">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-993">15 seconds</span></span> | <span data-ttu-id="c2528-994">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-995">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-996">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-997">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="c2528-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="c2528-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="c2528-999">15秒（15000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="c2528-1000">确定客户端发送 ping 消息的间隔。</span><span class="sxs-lookup"><span data-stu-id="c2528-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="c2528-1001">如果从客户端发送任何消息，则会将计时器重置为间隔的开始时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="c2528-1002">如果客户端没有在服务器上的设置中发送消息 `ClientTimeoutInterval` ，则服务器会将客户端视为已断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c2528-1003">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1003">Configure additional options</span></span>

<span data-ttu-id="c2528-1004">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-1006">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1006">.NET Option</span></span> |  <span data-ttu-id="c2528-1007">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1007">Default value</span></span> | <span data-ttu-id="c2528-1008">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c2528-1009">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c2528-1010">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-1011">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-1012">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c2528-1013">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1013">Empty</span></span> | <span data-ttu-id="c2528-1014">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c2528-1015">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1015">Empty</span></span> | <span data-ttu-id="c2528-1016">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c2528-1017">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1017">Empty</span></span> | <span data-ttu-id="c2528-1018">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c2528-1019">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-1019">5 seconds</span></span> | <span data-ttu-id="c2528-1020">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c2528-1020">WebSockets only.</span></span> <span data-ttu-id="c2528-1021">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c2528-1022">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c2528-1023">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1023">Empty</span></span> | <span data-ttu-id="c2528-1024">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c2528-1025">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c2528-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c2528-1026">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="c2528-1027">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c2528-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c2528-1028">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c2528-1029">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c2528-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c2528-1030">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c2528-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c2528-1031">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c2528-1032">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c2528-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c2528-1033">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c2528-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c2528-1034">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c2528-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-1036">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1036">JavaScript Option</span></span> | <span data-ttu-id="c2528-1037">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1037">Default Value</span></span> | <span data-ttu-id="c2528-1038">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c2528-1039">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c2528-1040">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-1041">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-1042">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-1043">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-1044">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1044">Java Option</span></span> | <span data-ttu-id="c2528-1045">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1045">Default Value</span></span> | <span data-ttu-id="c2528-1046">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c2528-1047">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c2528-1048">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-1049">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-1050">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c2528-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c2528-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c2528-1052">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1052">Empty</span></span> | <span data-ttu-id="c2528-1053">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c2528-1054">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c2528-1055">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c2528-1056">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c2528-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c2528-1057">其他资源</span><span class="sxs-lookup"><span data-stu-id="c2528-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="c2528-1058">JSON/MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="c2528-1059">ASP.NET Core SignalR 支持两个用于编码消息的协议： [JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="c2528-1060">每个协议都具有序列化配置选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="c2528-1061">可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置 JSON 序列化，该扩展方法可在方法中[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c2528-1062">`AddJsonProtocol`方法采用接收对象的委托 `options` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="c2528-1063">该对象的[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是一个 JSON.NET `JsonSerializerSettings` 对象，该对象可用于配置自变量和返回值的序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="c2528-1064">有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。</span><span class="sxs-lookup"><span data-stu-id="c2528-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="c2528-1065">例如，若要将序列化程序配置为使用 "PascalCase" 属性名称，而不是默认的 "camelCase" 名称，请在中使用以下代码 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="c2528-1066">在 .NET 客户端中， `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="c2528-1067">`Microsoft.Extensions.DependencyInjection`必须导入命名空间才能解析扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="c2528-1068">目前不能在 JavaScript 客户端中配置 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="c2528-1069">MessagePack 序列化选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1069">MessagePack serialization options</span></span>

<span data-ttu-id="c2528-1070">可以通过向[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用提供委托来配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="c2528-1071">有关更多详细信息，请参阅[中 SignalR 的 MessagePack](xref:signalr/messagepackhubprotocol) 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-1072">目前不能在 JavaScript 客户端中配置 MessagePack 序列化。</span><span class="sxs-lookup"><span data-stu-id="c2528-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="c2528-1073">配置服务器选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1073">Configure server options</span></span>

<span data-ttu-id="c2528-1074">下表描述了用于配置中心的选项 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="c2528-1075">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1075">Option</span></span> | <span data-ttu-id="c2528-1076">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1076">Default Value</span></span> | <span data-ttu-id="c2528-1077">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="c2528-1078">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-1078">15 seconds</span></span> | <span data-ttu-id="c2528-1079">如果客户端在此时间间隔内未发送初始握手消息，连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="c2528-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="c2528-1080">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-1081">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="c2528-1082">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-1082">15 seconds</span></span> | <span data-ttu-id="c2528-1083">如果服务器未在此时间间隔内发送消息，则会自动发送 ping 消息，使连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="c2528-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="c2528-1084">更改时 `KeepAliveInterval` ，请更改 `ServerTimeout` / `serverTimeoutInMilliseconds` 客户端上的设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="c2528-1085">建议 `ServerTimeout` / `serverTimeoutInMilliseconds` 值为值的两倍 `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="c2528-1086">所有已安装的协议</span><span class="sxs-lookup"><span data-stu-id="c2528-1086">All installed protocols</span></span> | <span data-ttu-id="c2528-1087">此中心支持的协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="c2528-1088">默认情况下，将允许在服务器上注册的所有协议，但可以从此列表中删除协议，以禁用各个集线器的特定协议。</span><span class="sxs-lookup"><span data-stu-id="c2528-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="c2528-1089">如果为，则在 `true` 集线器方法中引发异常时，详细的异常消息将返回到客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="c2528-1090">默认值为 `false` ，因为这些异常消息可能包含敏感信息。</span><span class="sxs-lookup"><span data-stu-id="c2528-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="c2528-1091">可以通过在中提供对调用的选项委托，为所有中心配置选项 `AddSignalR` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="c2528-1092">单个集线器的选项用于替代和中提供的全局选项 `AddSignalR` ，可以使用进行配置 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="c2528-1093">高级 HTTP 配置选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="c2528-1094">用于 `HttpConnectionDispatcherOptions` 配置与传输和内存缓冲区管理相关的高级设置。</span><span class="sxs-lookup"><span data-stu-id="c2528-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="c2528-1095">这些选项通过将委托传递给中的[MapHub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)来配置 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="c2528-1096">下表描述了用于配置 ASP.NET Core SignalR 的高级 HTTP 选项的选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="c2528-1097">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1097">Option</span></span> | <span data-ttu-id="c2528-1098">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1098">Default Value</span></span> | <span data-ttu-id="c2528-1099">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="c2528-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-1100">32 KB</span></span> | <span data-ttu-id="c2528-1101">服务器缓冲的客户端接收到的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="c2528-1102">增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="c2528-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="c2528-1103">从应用于 Hub 类的属性中自动收集的数据 `Authorize` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="c2528-1104">用于确定客户端是否有权连接到集线器的[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。</span><span class="sxs-lookup"><span data-stu-id="c2528-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="c2528-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="c2528-1105">32 KB</span></span> | <span data-ttu-id="c2528-1106">由服务器缓冲的应用发送的最大字节数。</span><span class="sxs-lookup"><span data-stu-id="c2528-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="c2528-1107">增大此值后，服务器将发送更大的消息，但会对内存消耗产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="c2528-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="c2528-1108">所有传输均已启用。</span><span class="sxs-lookup"><span data-stu-id="c2528-1108">All Transports are enabled.</span></span> | <span data-ttu-id="c2528-1109">值的位标志枚举 `HttpTransportType` ，可限制客户端可用于连接的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="c2528-1110">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-1110">See below.</span></span> | <span data-ttu-id="c2528-1111">特定于长轮询传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="c2528-1112">请参阅下文。</span><span class="sxs-lookup"><span data-stu-id="c2528-1112">See below.</span></span> | <span data-ttu-id="c2528-1113">特定于 Websocket 传输的其他选项。</span><span class="sxs-lookup"><span data-stu-id="c2528-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="c2528-1114">长轮询传输具有可使用属性配置的其他选项 `LongPolling` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="c2528-1115">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1115">Option</span></span> | <span data-ttu-id="c2528-1116">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1116">Default Value</span></span> | <span data-ttu-id="c2528-1117">描述</span><span class="sxs-lookup"><span data-stu-id="c2528-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="c2528-1118">90秒</span><span class="sxs-lookup"><span data-stu-id="c2528-1118">90 seconds</span></span> | <span data-ttu-id="c2528-1119">服务器在终止单个轮询请求之前等待发送到客户端的消息的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="c2528-1120">减小此值将导致客户端更频繁地发出新的投票请求。</span><span class="sxs-lookup"><span data-stu-id="c2528-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="c2528-1121">WebSocket 传输具有可使用属性配置的其他选项 `WebSockets` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="c2528-1122">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1122">Option</span></span> | <span data-ttu-id="c2528-1123">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1123">Default Value</span></span> | <span data-ttu-id="c2528-1124">描述</span><span class="sxs-lookup"><span data-stu-id="c2528-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="c2528-1125">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-1125">5 seconds</span></span> | <span data-ttu-id="c2528-1126">服务器关闭后，如果客户端在此时间间隔内未能关闭，则连接将终止。</span><span class="sxs-lookup"><span data-stu-id="c2528-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="c2528-1127">一个委托，可用于将 `Sec-WebSocket-Protocol` 标头设置为自定义值。</span><span class="sxs-lookup"><span data-stu-id="c2528-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="c2528-1128">委托接收客户端请求的值作为输入，并且应返回所需的值。</span><span class="sxs-lookup"><span data-stu-id="c2528-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="c2528-1129">配置客户端选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1129">Configure client options</span></span>

<span data-ttu-id="c2528-1130">可以在类型上配置客户端选项 `HubConnectionBuilder` （在 .net 和 JavaScript 客户端中提供）。</span><span class="sxs-lookup"><span data-stu-id="c2528-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="c2528-1131">它在 Java 客户端中也可用，但 `HttpHubConnectionBuilder` 子类是包含生成器配置选项的内容，也是其 `HubConnection` 本身。</span><span class="sxs-lookup"><span data-stu-id="c2528-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="c2528-1132">配置日志记录</span><span class="sxs-lookup"><span data-stu-id="c2528-1132">Configure logging</span></span>

<span data-ttu-id="c2528-1133">使用方法在 .NET 客户端中配置日志记录 `ConfigureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="c2528-1134">日志提供程序和筛选器的注册方式与服务器上相同。</span><span class="sxs-lookup"><span data-stu-id="c2528-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="c2528-1135">有关详细信息，请参阅[ASP.NET Core 文档中的日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="c2528-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="c2528-1136">若要注册日志记录提供程序，必须安装所需的包。</span><span class="sxs-lookup"><span data-stu-id="c2528-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="c2528-1137">有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。</span><span class="sxs-lookup"><span data-stu-id="c2528-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="c2528-1138">例如，若要启用控制台日志记录，请安装 `Microsoft.Extensions.Logging.Console` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c2528-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="c2528-1139">调用 `AddConsole` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="c2528-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="c2528-1140">在 JavaScript 客户端中，存在类似的 `configureLogging` 方法。</span><span class="sxs-lookup"><span data-stu-id="c2528-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="c2528-1141">提供一个 `LogLevel` 值，该值指示要生成的日志消息的最小级别。</span><span class="sxs-lookup"><span data-stu-id="c2528-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="c2528-1142">日志将写入浏览器控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="c2528-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="c2528-1143">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="c2528-1144">有关日志记录的详细信息，请参阅[ SignalR 诊断文档](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="c2528-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="c2528-1145">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="c2528-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c2528-1146">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="c2528-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c2528-1147">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="c2528-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c2528-1148">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="c2528-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c2528-1149">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="c2528-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="c2528-1150">配置允许的传输</span><span class="sxs-lookup"><span data-stu-id="c2528-1150">Configure allowed transports</span></span>

<span data-ttu-id="c2528-1151">使用的传输 SignalR 可以在调用中配置 `WithUrl` （ `withUrl` 在 JavaScript 中为）。</span><span class="sxs-lookup"><span data-stu-id="c2528-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="c2528-1152">的值的按位 "或" `HttpTransportType` 可用于将客户端限制为仅使用指定的传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="c2528-1153">默认情况下，将启用所有传输。</span><span class="sxs-lookup"><span data-stu-id="c2528-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="c2528-1154">例如，若要禁用服务器发送的事件传输，但允许 Websocket 和长轮询连接，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c2528-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="c2528-1155">在 JavaScript 客户端中，通过 `transport` 在提供的选项对象上设置字段来配置传输 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="c2528-1156">配置持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="c2528-1156">Configure bearer authentication</span></span>

<span data-ttu-id="c2528-1157">若要与请求一起提供身份验证数据 SignalR ，请使用 `AccessTokenProvider` 选项（ `accessTokenFactory` 在 JavaScript 中）来指定返回所需访问令牌的函数。</span><span class="sxs-lookup"><span data-stu-id="c2528-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="c2528-1158">在 .NET 客户端中，此访问令牌作为 HTTP "持有者身份验证" 令牌传入（使用 `Authorization` 类型为的标头 `Bearer` ）。</span><span class="sxs-lookup"><span data-stu-id="c2528-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="c2528-1159">在 JavaScript 客户端中，访问令牌用作持有者令牌，**但**在某些情况下，浏览器 api 限制应用标头的能力（具体而言，在服务器发送事件和 websocket 请求中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="c2528-1160">在这些情况下，访问令牌作为查询字符串值提供 `access_token` 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="c2528-1161">在 .NET 客户端中， `AccessTokenProvider` 可使用中的选项委托指定选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="c2528-1162">在 JavaScript 客户端中，通过 `accessTokenFactory` 在中设置 "选项" 对象上的字段来配置访问令牌 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="c2528-1163">在 SignalR Java 客户端中，可以通过向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c2528-1164">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="c2528-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c2528-1165">如果调用了[单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c2528-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="c2528-1166">配置超时和 keep-alive 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="c2528-1167">用于配置超时和保持活动状态的其他选项可用于 `HubConnection` 对象本身：</span><span class="sxs-lookup"><span data-stu-id="c2528-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-1169">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1169">Option</span></span> | <span data-ttu-id="c2528-1170">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1170">Default value</span></span> | <span data-ttu-id="c2528-1171">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="c2528-1172">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-1173">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-1173">Timeout for server activity.</span></span> <span data-ttu-id="c2528-1174">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-1175">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-1176">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="c2528-1177">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-1177">15 seconds</span></span> | <span data-ttu-id="c2528-1178">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-1179">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `Closed` 事件（ `onclose` 在 JavaScript 中）。</span><span class="sxs-lookup"><span data-stu-id="c2528-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="c2528-1180">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-1181">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="c2528-1182">在 .NET 客户端中，超时值指定为 `TimeSpan` 值。</span><span class="sxs-lookup"><span data-stu-id="c2528-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="c2528-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-1184">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1184">Option</span></span> | <span data-ttu-id="c2528-1185">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1185">Default value</span></span> | <span data-ttu-id="c2528-1186">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="c2528-1187">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-1188">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-1188">Timeout for server activity.</span></span> <span data-ttu-id="c2528-1189">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onclose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="c2528-1190">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-1191">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-1192">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-1193">选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1193">Option</span></span> | <span data-ttu-id="c2528-1194">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1194">Default value</span></span> | <span data-ttu-id="c2528-1195">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="c2528-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="c2528-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="c2528-1197">30秒（30000毫秒）</span><span class="sxs-lookup"><span data-stu-id="c2528-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="c2528-1198">服务器活动超时。</span><span class="sxs-lookup"><span data-stu-id="c2528-1198">Timeout for server activity.</span></span> <span data-ttu-id="c2528-1199">如果服务器未在此时间间隔内发送消息，则客户端会将服务器视为断开连接，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-1200">此值必须足够大，以便从服务器发送 ping 消息 **，并**在超时间隔内由客户端接收该消息。</span><span class="sxs-lookup"><span data-stu-id="c2528-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="c2528-1201">建议值至少为服务器值的两倍 `KeepAliveInterval` ，以允许 ping 到达的时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="c2528-1202">15 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-1202">15 seconds</span></span> | <span data-ttu-id="c2528-1203">初始服务器握手的超时时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="c2528-1204">如果服务器在此时间间隔内未发送握手响应，则客户端将取消握手，并触发 `onClose` 事件。</span><span class="sxs-lookup"><span data-stu-id="c2528-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="c2528-1205">这是一种高级设置，只应在握手超时错误由于严重网络延迟而发生时进行修改。</span><span class="sxs-lookup"><span data-stu-id="c2528-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="c2528-1206">有关握手过程的详细信息，请参阅[ SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。</span><span class="sxs-lookup"><span data-stu-id="c2528-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="c2528-1207">配置其他选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1207">Configure additional options</span></span>

<span data-ttu-id="c2528-1208">可在上的 `WithUrl` （ `withUrl` JavaScript）方法中 `HubConnectionBuilder` 或在 `HttpHubConnectionBuilder` Java 客户端中的各种配置 api 上配置其他选项：</span><span class="sxs-lookup"><span data-stu-id="c2528-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="c2528-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="c2528-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="c2528-1210">.NET 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1210">.NET Option</span></span> |  <span data-ttu-id="c2528-1211">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1211">Default value</span></span> | <span data-ttu-id="c2528-1212">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="c2528-1213">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="c2528-1214">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-1215">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-1216">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="c2528-1217">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1217">Empty</span></span> | <span data-ttu-id="c2528-1218">要发送以对请求进行身份验证的 TLS 证书的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="c2528-1219">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1219">Empty</span></span> | <span data-ttu-id="c2528-1220">要随每个 HTTP 请求一起发送的 HTTP cookie 的集合。</span><span class="sxs-lookup"><span data-stu-id="c2528-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="c2528-1221">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1221">Empty</span></span> | <span data-ttu-id="c2528-1222">要随每个 HTTP 请求一起发送的凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="c2528-1223">5 秒</span><span class="sxs-lookup"><span data-stu-id="c2528-1223">5 seconds</span></span> | <span data-ttu-id="c2528-1224">仅 Websocket。</span><span class="sxs-lookup"><span data-stu-id="c2528-1224">WebSockets only.</span></span> <span data-ttu-id="c2528-1225">客户端在关闭之后等待服务器确认关闭请求的最长时间。</span><span class="sxs-lookup"><span data-stu-id="c2528-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="c2528-1226">如果服务器在这段时间内没有确认关闭，客户端将断开连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="c2528-1227">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1227">Empty</span></span> | <span data-ttu-id="c2528-1228">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="c2528-1229">一个委托，可用于配置或替换 `HttpMessageHandler` 用于发送 HTTP 请求的。</span><span class="sxs-lookup"><span data-stu-id="c2528-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="c2528-1230">不用于 WebSocket 连接。</span><span class="sxs-lookup"><span data-stu-id="c2528-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="c2528-1231">此委托必须返回非 null 值，并接收默认值作为参数。</span><span class="sxs-lookup"><span data-stu-id="c2528-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="c2528-1232">修改该默认值的设置并将其返回，或返回一个新的 `HttpMessageHandler` 实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="c2528-1233">**当替换处理程序时，请确保从提供的处理程序复制您要保留的设置，否则，配置的选项（如 Cookie 和标头）将不会应用于新的处理程序。**</span><span class="sxs-lookup"><span data-stu-id="c2528-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="c2528-1234">发送 HTTP 请求时要使用的 HTTP 代理。</span><span class="sxs-lookup"><span data-stu-id="c2528-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="c2528-1235">设置此布尔值可发送 HTTP 和 Websocket 请求的默认凭据。</span><span class="sxs-lookup"><span data-stu-id="c2528-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="c2528-1236">这样就可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="c2528-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="c2528-1237">可用于配置其他 WebSocket 选项的委托。</span><span class="sxs-lookup"><span data-stu-id="c2528-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="c2528-1238">接收可用于配置选项的[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)实例。</span><span class="sxs-lookup"><span data-stu-id="c2528-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="c2528-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="c2528-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="c2528-1240">JavaScript 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1240">JavaScript Option</span></span> | <span data-ttu-id="c2528-1241">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1241">Default Value</span></span> | <span data-ttu-id="c2528-1242">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="c2528-1243">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="c2528-1244">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-1245">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-1246">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="c2528-1247">Java</span><span class="sxs-lookup"><span data-stu-id="c2528-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="c2528-1248">Java 选项</span><span class="sxs-lookup"><span data-stu-id="c2528-1248">Java Option</span></span> | <span data-ttu-id="c2528-1249">默认值</span><span class="sxs-lookup"><span data-stu-id="c2528-1249">Default Value</span></span> | <span data-ttu-id="c2528-1250">说明</span><span class="sxs-lookup"><span data-stu-id="c2528-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="c2528-1251">一个函数，它返回作为 HTTP 请求中的持有者身份验证令牌提供的字符串。</span><span class="sxs-lookup"><span data-stu-id="c2528-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="c2528-1252">将此设置为 `true` 以跳过协商步骤。</span><span class="sxs-lookup"><span data-stu-id="c2528-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="c2528-1253">**仅当 websocket 传输为唯一启用的传输时才受支持**。</span><span class="sxs-lookup"><span data-stu-id="c2528-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="c2528-1254">使用 Azure 服务时，无法启用此设置 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="c2528-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="c2528-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="c2528-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="c2528-1256">空</span><span class="sxs-lookup"><span data-stu-id="c2528-1256">Empty</span></span> | <span data-ttu-id="c2528-1257">要随每个 HTTP 请求一起发送的附加 HTTP 标头的映射。</span><span class="sxs-lookup"><span data-stu-id="c2528-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="c2528-1258">在 .NET 客户端中，可以通过提供给的 options 委托来修改这些选项 `WithUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="c2528-1259">在 JavaScript 客户端中，可以在提供给的 JavaScript 对象中提供这些选项 `withUrl` ：</span><span class="sxs-lookup"><span data-stu-id="c2528-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="c2528-1260">在 Java 客户端中，这些选项可以在 `HttpHubConnectionBuilder` 从`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="c2528-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="c2528-1261">其他资源</span><span class="sxs-lookup"><span data-stu-id="c2528-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
